
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

graph LR
    Pico_3V3[Pico 3.3V] --> Joystick_5V[Joystick +5V Pin]
    Pico_GND[Pico AGND] --> Joystick_GND[Joystick GND]
    Joystick_VRX[Joystick VRX] --> Pico_GP26[Pico GP26/ADC0]
    Joystick_VRY[Joystick VRY] --> Pico_GP27[Pico GP27/ADC1]
    Joystick_SW[Joystick SW] --> Pico_GP22[Pico GP22]