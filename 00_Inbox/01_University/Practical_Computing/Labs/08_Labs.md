
# Lab 8: Integrating Multiple Devices (State Diagram)
## 1) Why this lab matters

> [!info]
> In embedded/IoT systems, we usually do **event-driven** programming rather than constantly polling everything in a big loop.

This lab combines 3 core techniques:

1. **Interrupts** → react immediately when events happen  
2. **Timers** → run tasks periodically or after delays  
3. **FSM** → keep code organised and scalable using states/transitions

---

## 2) Core concepts

## 2.1 Interrupts

An interrupt is a hardware-triggered mechanism that temporarily diverts CPU execution to a callback (handler function).

Examples:
- Input pin change (e.g., PIR output goes HIGH)
- Timer reaches period
- Communication peripheral receives data

> [!tip]
> Interrupts let your CPU sleep/do other work instead of constantly checking sensors.

---

## 2.2 Timers

- **Hardware timer**: physical timer peripheral in MCU
- **Software timers**: multiple timer tasks managed via one hardware timer

In MicroPython on Pico (`machine.Timer`):
- `Timer.PERIODIC` → repeat every N ms
- `Timer.ONE_SHOT` → trigger once after N ms

Use in this lab:
- 1s periodic timer → run FSM callback
- 3s periodic timer → AM2320 reads
- 5s one-shot timer → turn off external LED after PIR event

---

## 2.3 FSM (Finite State Machine)

FSM = model system as:
- **States** (what mode system is in)
- **Transitions** (what causes state changes)
- **Actions** (what system does in each state/transition)

This lab needs **2 states**:

- `State 1` = Start-up / Idle (wait for PLAY)
- `State 2` = Running (sensors active, wait for POWER)

---

## 3) Task 1 — Hardware connections

## 3.1 AM2320 (I2C temperature/humidity)

![[Pasted image 20260317094928.png]]

### Important points

- Interface: **I2C**
- Sensor updates about every **2 seconds**
- Add **pull-up resistors** on SDA and SCL (2kΩ–10kΩ, provided 10kΩ)
- Use `I2C(0, sda=GP8, scl=GP9, freq=400000)`

> [!warning]
> AM2320 can be damaged by wrong power wiring. Double-check 3.3V and GND before powering.

### Wiring summary (recommended)

| AM2320 Pin | Pico |
|---|---|
| VCC | 3.3V |
| GND | GND |
| SDA | GP8 |
| SCL | GP9 |

Pull-ups:
- `SDA -> 10kΩ -> 3.3V`
- `SCL -> 10kΩ -> 3.3V`

---

## 3.2 HC-SR501 PIR sensor

![[Pasted image 20260317095012.png]]

### Behaviour

- Detects motion by thermal IR change (works in darkness)
- Output `S` goes HIGH briefly on motion
- Needs about **1 minute warm-up** after power-on
- Supply from **5V (VBUS)**

### Wiring summary

| PIR Pin | Pico |
|---|---|
| VCC | VBUS (5V) |
| GND | GND |
| S (OUT) | GP15 (digital input) |

> [!note]
> PIR output already has suitable drive levels; no pull resistor needed for input pin.

---

## 3.3 VS1838 IR receiver + remote

- Remote sends modulated IR at ~38kHz
- VS1838 demodulates to digital pulses
- Connected to digital input (no pull resistor needed)

### Wiring summary

| VS1838 Pin | Pico |
|---|---|
| VCC | 3.3V |
| GND | GND |
| OUT | GP16 |

Use `ir_rx` library (NEC protocol for provided remote):
```python
from ir_rx.nec import NEC_8
ir = NEC_8(Pin(16, Pin.IN), Remote_callback)
```

---

## 4) Required libraries/files on Pico

Upload these to Pico storage via Thonny Files panel:

- `am2320.py`
- folder `ir_rx/` containing extracted `.py` files from `ir_rx.zip`

> [!danger]
> Do **not** leave files inside `.zip`; you must extract first, then upload extracted files.

---

## 5) Desired system behaviour

1. On startup, turn **onboard LED ON**
2. Wait for remote **PLAY** button (`0x15`)
3. When PLAY received:
   - start PIR interrupt monitoring
   - start AM2320 periodic reading every 3s
4. On PIR trigger:
   - turn external LED ON for 5s
5. If remote **POWER** (`0x45`) received:
   - stop PIR + AM2320 callbacks
   - return to startup state waiting for PLAY

---

## 6) State diagram

```mermaid
stateDiagram-v2
    [*] --> State1

    State1: State 1 (Start-up/Idle)
    State2: State 2 (Running)

    State1: onboard LED = ON
    State1 --> State2: PLAY (0x15)
    note right of State1
      On transition to State2:
      - Enable PIR IRQ
      - Start AM2320 timer (3s periodic)
    end note

    State2: onboard LED = OFF
    State2 --> State1: POWER (0x45)
    note right of State2
      On transition to State1:
      - Disable PIR IRQ
      - Stop AM2320 timer
    end note
```

---

## 7) Callback architecture (design)

## 7.1 `Remote_callback(data, addr, ctrl)`
- Stores latest IR code in global `code`
- Ignore repeat codes (`data < 0`)

## 7.2 `AM2320_callback(timer)`
- `sensor.measure()`
- print temperature + humidity

## 7.3 `PIR_callback(pin)`
When motion detected:
1. Turn external LED ON
2. Start one-shot timer for 5s -> `LED_callback`
3. Disable PIR IRQ temporarily (avoid retrigger storm)

## 7.4 `LED_callback(timer)`
1. Turn external LED OFF
2. Re-enable PIR IRQ

## 7.5 `FSM_callback(timer)` (called every 1 second)
- Evaluate current state
- Apply state action
- Check transition condition(s)
- Do transition side-effects (enable/disable interrupts/timers)

---

## 8) Full reference implementation (MicroPython)

```python
from machine import Pin, I2C, Timer
import am2320
from ir_rx.nec import NEC_8  # NEC remote, 8-bit addr

# -------------------------
# Globals
# -------------------------
code = 0
current_state = 1  # 1 = Start-up, 2 = Running

# -------------------------
# Pin config
# -------------------------
onboard_led = Pin("LED", Pin.OUT)
my_led = Pin(14, Pin.OUT)      # external LED on GP14
PIR = Pin(15, Pin.IN)          # PIR output on GP15

# -------------------------
# AM2320 setup (I2C0)
# -------------------------
i2c = I2C(0, scl=Pin(9), sda=Pin(8), freq=400000)
sensor = am2320.AM2320(i2c)

# Timers
AM2320_timer = Timer()
LED_timer = Timer()
FSM_timer = Timer()

# -------------------------
# Callbacks
# -------------------------
def Remote_callback(data, addr, ctrl):
    global code
    if data < 0:
        # NEC repeat frame
        return
    code = data
    print("IR Data 0x{:02x} Addr 0x{:04x}".format(data, addr))

def AM2320_callback(t):
    try:
        sensor.measure()
        print("{:.1f}C".format(sensor.temperature()))
        print("{:.1f}%".format(sensor.humidity()))
    except Exception as e:
        print("AM2320 read error:", e)

def LED_callback(t):
    # 5 seconds elapsed after PIR trigger
    my_led.value(0)
    # re-enable PIR interrupt
    PIR.irq(trigger=Pin.IRQ_RISING, handler=PIR_callback)

def PIR_callback(pin):
    # motion event
    my_led.value(1)
    # disable PIR while LED timer runs
    PIR.irq(handler=None)
    LED_timer.init(period=5000, mode=Timer.ONE_SHOT, callback=LED_callback)

def FSM_callback(t):
    global current_state, code

    if current_state == 1:  # START-UP / IDLE
        onboard_led.value(1)

        if hex(code) == '0x15':  # PLAY button
            print("Moving to state 2 (RUNNING)")
            current_state = 2

            # activate sensors in RUNNING
            PIR.irq(trigger=Pin.IRQ_RISING, handler=PIR_callback)
            AM2320_timer.init(period=3000, mode=Timer.PERIODIC, callback=AM2320_callback)

            # optional: clear code so transition doesn't retrigger accidentally
            code = 0

    elif current_state == 2:  # RUNNING
        onboard_led.value(0)

        if hex(code) == '0x45':  # POWER button
            print("Moving back to state 1 (START-UP)")
            current_state = 1

            # deactivate sensors
            PIR.irq(handler=None)
            AM2320_timer.deinit()
            LED_timer.deinit()
            my_led.value(0)

            # optional: clear code
            code = 0

# -------------------------
# IR decoder setup
# -------------------------
ir = NEC_8(Pin(16, Pin.IN), Remote_callback)

print("System start: State 1")
print("Press PLAY (0x15) to run, POWER (0x45) to stop.")

# Run FSM every 1 second
FSM_timer.init(period=1000, mode=Timer.PERIODIC, callback=FSM_callback)
```

---

## 9) Common issues and fixes

> [!bug] No IR key detection
> - Wrong protocol class (must use `NEC_8` for provided remote)
> - VS1838 pinout reversed
> - Bad ground connection

> [!bug] AM2320 always same values
> - Reading too fast (<2s)
> - Wiring/pull-up issue on SDA/SCL

> [!bug] PIR triggers randomly at startup
> - Normal warm-up behaviour (~1 min). Ignore until settled.

> [!bug] External LED stays ON forever
> - `LED_timer` not initialized one-shot
> - `LED_callback` not re-enabling/disabling IRQ correctly
> - Missing `my_led.value(0)` on stop transition

---

## 12) Quick recap

> [!success]
> This lab demonstrates a **clean embedded architecture**:
> - Event handling via **interrupts**
> - Time-based tasks via **timers**
> - System orchestration via **FSM**
>
> This pattern is directly reusable in coursework and real IoT projects.