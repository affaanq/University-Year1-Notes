
# 🕹️ Raspberry Pi Pico: GPIO, LEDs, and Switches

## 1. GPIO Overview (General Purpose Input-Output)

Microcontrollers interact with the physical world through GPIO pins. Each pin can be independently configured as either a **Digital Input** or a **Digital Output**.

### A. Digital Input (Reading)

- **Purpose:** To "read" an external voltage and convert it into a digital value (1 or 0) in code.
    
- **Logic HIGH (1):** External voltage is close to the supply voltage (3.3V for Pico).
    
- **Logic LOW (0):** External voltage is close to 0V (Ground/GND).
    
- **Pin Mapping:** Pico board pins (grey) do not always match MCU GP numbers (green). For example, board pin 20 is internally connected to **GP15**.
    

### B. Digital Output (Writing)

- **Purpose:** To "write" a digital value from the MCU to a pin, creating an external voltage to act on devices like LEDs.
    
- **Limitations:** MCU pins are not power supplies. They have a **maximum current of 12mA** per pin and a **total limit of 50mA** across all pins. Drawing too much current can damage the device.
    

---

## 2. Practical Hardware Setup: The Breadboard

Prototyping requires physical connections without soldering. Breadboards facilitate this via internal metal plates.

- **Power Rails:** The top and bottom rows (marked + and -) extend the 3.3V and GND supply along the board.
    
- **Component Columns:** The 5 holes in each vertical column are interconnected, allowing multiple components to share a connection.
    
- **The Middle Gap:** Columns are split by a center gap, allowing chips (like the Pico) to be mounted without short-circuiting opposite pins.
    

---

## 3. Implementation: Controlling LEDs

To drive an external LED, you must connect it in series with a **current-limiting resistor**.

### LED Polarity

- **Anode (+):** Longer pin / smaller metal piece inside the plastic.
    
- **Cathode (-):** Shorter pin / marked with a flat cut on the plastic base.
    

### Code: Blinking an External LED

Python

``` python
from machine import Pin
from time import sleep

# Configure GP15 (Board Pin 20) as an output
my_led = Pin(15, Pin.OUT)

while True:
    my_led.value(1) # LED ON (3.3V)
    sleep(0.5)
    my_led.value(0) # LED OFF (0V)
    sleep(0.5)
```

---

## 4. Implementation: Digital Inputs (Buttons)

Connecting a button requires a **Pull Resistor** to prevent "floating" pins (where the input picks up electrical noise when the circuit is open).

### Pull Directions

1. **Pull-Down:** Pin is held at 0V by default. Pressing the button connects it to 3.3V (Logic 1).
    
2. **Pull-Up:** Pin is held at 3.3V by default. Pressing the button connects it to GND (Logic 0).
    

### Code: Polling a Button

"Polling" means the CPU constantly checks the button state in a loop. This is simple but energy-inefficient.

Python

``` python
from machine import Pin

# Set GP14 (Board Pin 19) as input with internal Pull-Down resistor
my_button = Pin(14, Pin.IN, Pin.PULL_DOWN)
my_led = Pin(15, Pin.OUT)

while True:
    if my_button.value() == 1: # If button is pressed
        my_led.value(1)
    else:
        my_led.value(0)
```

---

## 5. Advanced Concept: Interrupts (IRQ)

Interrupts allow the CPU to stop its current task only when a specific event occurs (like a button press). This is far more efficient than polling.

### Rising vs. Falling Edges

- **Rising Edge (`IRQ_RISING`):** Triggered the instant the voltage moves from 0 to 1 (pressing the button in a pull-down setup).
    
- **Falling Edge (`IRQ_FALLING`):** Triggered when the voltage drops from 1 to 0 (releasing the button).
    

### Code: Reaction Time Game

This script uses a **callback function** that runs only when the interrupt is triggered.

Python

``` python
from machine import Pin
from utime import sleep, ticks_ms, ticks_diff
import urandom

pressed = True
my_led = Pin(15, Pin.OUT)
my_button = Pin(14, Pin.IN, Pin.PULL_DOWN)

# Callback function (Handler)
def button_handler(pin):
    global pressed, timer_start
    if not pressed: #if the light went off and we are waiting for a reaction
        pressed = True
        # Calculate time difference between now and the start signal
        timer_reaction = ticks_diff(ticks_ms(), timer_start)
        print("Your reaction time was " + str(timer_reaction) + " ms!")

# Attach interrupt to the button pin
my_button.irq(trigger=Pin.IRQ_RISING, handler=button_handler)

while True:
    my_led.value(1) # Light ON
    sleep(urandom.uniform(5, 10)) # Wait random time
    my_led.value(0) # Light OFF - START!
    
    pressed = False # waiting for the response from the user
    timer_start = ticks_ms() # Capture start time
    
    while not pressed: # Wait for interrupt to change 'pressed' to True
        pass
```

---

### Key Takeaways for Lab Report

- **Active Wait:** `sleep()` does not put the CPU to sleep; it keeps the CPU busy checking a timer.
    
- **Idempotency:** Using `DROP TABLE IF EXISTS` in SQLite (from your previous notes) mirrors the need for clean initial states in hardware.
    
- **Global Variables:** Used in callbacks to communicate between the interrupt handler and the main program loop.
