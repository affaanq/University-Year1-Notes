
# Lab 3: Analog and Timed Inputs (RP2040)

**Tags:** #MicroPython #RP2040 #ADC #Sensors #Hardware #Computng
**Date:** 2026-02-10

---

## 1. The Core Concept: Analog to Digital Conversion (ADC)

### The Reality Gap
The physical world is **Analog** (Continuous). Computers are **Digital** (Discrete/Binary).
* **Analog:** Temperature is $21.567...^\circ C$. It flows.
* **Digital:** A switch is `1` (ON) or `0` (OFF).

To interface the two, we need a translator: the **ADC (Analog to Digital Converter)**.

> [!NOTE] Analogy: The Staircase
> Imagine a ramp (Analog signal). You can stand at any height.
> The ADC turns that ramp into a staircase (Digital values).
> * **Resolution:** The number of steps. More steps = smoother representation of the ramp.
> * **Reference Voltage ($V_{REF}$):** The height of the top of the staircase.
> * **Sampling Rate:** How often you check which step you are on.

### RP2040 Specs (The Brutal Truth)
The Raspberry Pi Pico (RP2040 chip) has a **12-bit ADC** hardware.
* **Hardware Range:** $0$ to $4095$ ($2^{12} - 1$).
* **MicroPython Abstraction:** MicroPython scales this automatically to **16-bit** ($0$ to $65535$) to maintain consistency across different boards.
* **Voltage Range:** $0V$ to $3.3V$.

### The Math (Don't skip this)
To convert the integer the Pico gives you back into a real voltage:

$$V_{in} = \frac{ADC_{value}}{65535} \times 3.3V$$

* **Resolution (Sensitivity):** The smallest change in voltage the Pico can detect.
    $$\text{Resolution} = \frac{3.3V}{4095} \approx 0.8 mV$$
    *(Note: We divide by 4095 because that is the hardware limit, even if software scales it up).*

---

## 2. Task 1.1: Internal Temperature Sensor

The RP2040 has a sensor built deeply into the silicon. It measures the temperature of the *chip*, not the room.

### The Hardware Reality
* **Input:** ADC Channel 4 (Internal, not on a GPIO pin).
* **Mechanism:** Measures the voltage across a biased diode ($V_{BE}$). As silicon heats up, the voltage drop across a diode decreases (Negative Temperature Coefficient).

### The Code
```python
from machine import Pin
import machine
from time import sleep

# Configure ADC on Channel 4 (Internal Temp Sensor)
temp_sens = machine.ADC(4) 

while True:
    # 1. Read raw 16-bit integer (0-65535)
    ADC_temp_value = temp_sens.read_u16() 
    
    # 2. Convert integer to Voltage (Equation 3)
    temp_V = ADC_temp_value * 3.3 / 65535 
    
    # 3. Convert Voltage to Celsius (Manufacturer Calibration)
    # 27 degrees is the reference point at 0.706V.
    # Slope is -1.721mV per degree.
    temp_C = 27 - (temp_V - 0.706) / 0.001721
    
    print("ADC:", ADC_temp_value, " | Volts:", round(temp_V, 4), "V | Temp:", round(temp_C, 2), "C")
    sleep(2)
```
    
> [!WARNING] Blind Spot This sensor is noisy. It sits next to the CPU cores. If your code runs a heavy loop, the CPU heats up, and this sensor reading will spike, regardless of the room temperature. **Do not use this for ambient room temperature sensing.**

## 3. Task 1.2: Analog Joystick

A joystick is not a magic device. It is simply **two potentiometers** (variable resistors) and one button.

### Concept: The Voltage Divider

A potentiometer acts as a voltage divider.

$$ V_{out} = V_{in} \times \frac{R_2}{R_1 + R_2} $$

As you move the stick, you slide a wiper along a resistive track, changing the ratio of $R_1$ to $R_2$, effectively varying the voltage from $0V$ to $3.3V$.

### Wiring Diagram (Mental Model)

- **VCC:** Connect to `3V3(OUT)` (Pin 36). **CRITICAL:** Do not use 5V (VBUS). The ADC pins are not 5V tolerant. You will damage the ADC.
    
- **GND:** Connect to `AGND` (Pin 33).
    
- **VRx (Horizontal):** Connect to `ADC0` (GP26 / Pin 31).
    
- **VRy (Vertical):** Connect to `ADC1` (GP27 / Pin 32).
    
- **SW (Switch):** Connect to `GP22` (Pin 29).


```
graph LR
    Pico_3V3[Pico 3.3V] --> Joystick_5V[Joystick +5V Pin]
    Pico_GND[Pico AGND] --> Joystick_GND[Joystick GND]
    Joystick_VRX[Joystick VRX] --> Pico_GP26[Pico GP26/ADC0]
    Joystick_VRY[Joystick VRY] --> Pico_GP27[Pico GP27/ADC1]
    Joystick_SW[Joystick SW] --> Pico_GP22[Pico GP22]
```

```python
from machine import Pin
from time import sleep

led_onboard = Pin(25, Pin.OUT)

# Configure Analog Inputs
X = machine.ADC(26) # GP26
Y = machine.ADC(27) # GP27

# Configure Digital Input (Button) with Internal Pull-Up
# We need PULL_UP because the switch connects to GND when pressed.
SW = Pin(22, Pin.IN, Pin.PULL_UP) 

while True:
    X_value = X.read_u16() # Range 0-65535
    Y_value = Y.read_u16() # Range 0-65535
    button_value = SW.value() # 1 (released) or 0 (pressed)

    print(f"X: {X_value}, Y: {Y_value}, Button: {button_value}")

    # Visual feedback
    if button_value == 0: # Active Low
        led_onboard.value(1)
    else:
        led_onboard.value(0)

    sleep(0.1)
```

## 4. Task 2: Ultrasonic Sensor (HC-SR04)

This moves us from Analog inputs to **Timed Digital Inputs**. We aren't measuring voltage amplitude; we are measuring pulse duration.

### Concept: Time of Flight (ToF)

> [!NOTE] Analogy: The Canyon Echo
> 
> You yell "Hello" into a canyon. You wait. 2 seconds later, you hear "Hello".
> 
> You know sound travels at ~340m/s.
> 
> The sound traveled there AND back.
> 
> Distance = (Time $\times$ Speed) / 2.

### The Protocol (Handshake)

1. **Trigger:** Pico sends a `10us` HIGH pulse. This tells the sensor "Wake up and fire".
    
2. **Burst:** Sensor fires 8 pulses at 40kHz (ultrasonic).
    
3. **Echo:** Sensor sets the ECHO pin HIGH. It stays HIGH exactly as long as it takes for the sound to bounce back.
    
4. **Listen:** Pico measures how long the ECHO pin stays HIGH.
    

### Connection Diagram

**Warning:** The HC-SR04 requires **5V** power to operate the ultrasound burst physics, but the Pico logic is **3.3V**.

- **VCC:** Connect to `VBUS` (Pin 40 - 5V).
    
- **Trig:** `GP19` (Pin 25).
    
- **Echo:** `GP18` (Pin 24). _Technically, this sends a 5V signal back to the Pico. The Pico GPIO has some tolerance, but for long-term production, you should use a voltage divider (2k/1k resistors) to drop this to 3.3V. For this lab, direct connection is "acceptable risks"._
    

### The Code (Blocking Method)

This code uses "polling" (loops that wait).

- **Flaw:** While the `while` loops are running, the processor freezes. It cannot do anything else.
    

Python

```python
from machine import Pin
from utime import sleep, sleep_us, sleep_ms, ticks_us

trigger = Pin(19, Pin.OUT)
echo = Pin(18, Pin.IN)

def getDistance_cm():
    trigger.low()
    sleep_us(2)
    
    # 1. Send 10us pulse
    trigger.high()
    sleep_us(10) # Minimum requirement per datasheet
    trigger.low()
    
    # 2. Wait for Echo to go HIGH (Start of timing)
    # We use a loop to wait. 
    while echo.value() == 0:
        pass
    t1 = ticks_us()
    
    # 3. Wait for Echo to go LOW (End of timing)
    while echo.value() == 1:
        pass
    t2 = ticks_us()
    
    # 4. Calculate Duration
    tof = t2 - t1
    
    # 5. Physics Calculation
    # Speed of sound = 343 m/s = 0.0343 cm/us
    # Distance = (Time * Speed) / 2
    # Distance = (tof * 0.0343) / 2  => tof / 58.3
    
    if tof >= 38000: # Timeout (38ms) per datasheet
        return 0
    
    distance = tof / 58
    return distance

while True:
    dist = getDistance_cm()
    print(f"Distance: {dist:.1f} cm")
    sleep(1)
```

---

## 5. Task 3: Light Dependent Resistor (LDR)

### Concept: Resistive Sensors

An LDR changes its resistance based on photons hitting it.

- **Dark:** High Resistance (~M$\Omega$).
    
- **Light:** Low Resistance (~k$\Omega$).
    

**Problem:** The ADC reads **Voltage**, not Resistance.

**Solution:** A **Voltage Divider** circuit. We add a fixed resistor ($R_{fixed} = 1k\Omega$) in series.

### Math: Deriving Lux

1. **The Circuit Equation:**
    
    $$ V_{in} = 3.3V \times \frac{R_{fixed}}{R_{LDR} + R_{fixed}} $$
    
2. **Solving for LDR Resistance:**
    
    $$ R_{LDR} = R_{fixed} \times (\frac{65535}{ADC_{raw}} - 1) $$
    
3. **Lux Conversion:**
    
    This is non-linear. It requires a Log-Log plot (Calibration Curve).
    
    - Eyeball method (Figure 7): $10k\Omega \approx 30 \text{ Lux}$.
        

### Connections

- **LDR Leg 1:** `3V3(OUT)`.
    
- **LDR Leg 2:** Connected to `GP28/ADC2` **AND** to the Resistor.
    
- **Resistor Leg 2:** `GND`.
    
    _(This creates the divider where the measurement is taken in the middle)._
    

Python

``` python
# (Snippet to add to previous loop)
ldr_adc = machine.ADC(28)
R_fixed = 1000 # 1k Ohm

raw_value = ldr_adc.read_u16()

# Avoid division by zero
if raw_value > 0:
    # Calculate Resistance of LDR
    r_ldr = R_fixed * ((65535 / raw_value) - 1)
    print(f"LDR Resistance: {r_ldr:.0f} Ohms")
    
    # Note: To get Lux, you need the specific equation for your LDR model.
    # Usually: Lux = C * (Resistance ^ -slope)
```

---

## Critical Takeaways (The "No Fluff" Summary)

1. **ADC Resolution:** Pico = 12-bit hardware, scaled to 16-bit software. Always use `65535` in your math for MicroPython.
    
2. **Voltage Dividers:** Essential for reading resistive sensors (LDRs, Thermistors, Potentiometers). You cannot read resistance directly.
    
3. **5V vs 3.3V:** The Pico is a 3.3V device. The Ultrasonic sensor (HC-SR04) uses 5V logic. Direct connection is risky; level shifting is professional.
    
4. **Blocking Code:** The Ultrasonic code uses `while` loops. If the sensor wire breaks, your code hangs forever inside that loop (unless you add a timeout counter). This is a common rookie mistake in embedded systems.