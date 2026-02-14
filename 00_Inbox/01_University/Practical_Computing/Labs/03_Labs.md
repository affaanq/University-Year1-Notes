
# Practical Computing: Analog Input (ADC) & Time-Based Sensing

## The 'What' (Technical Definition)
**Analog-to-Digital Conversion (ADC)** is the hardware process of sampling a continuous analog signal (voltage) and **quantizing** it into a discrete digital integer.

On the RP2040 (Raspberry Pi Pico), the hardware ADC has **12-bit resolution** (values 0-4095), but MicroPython virtually scales this to **16-bit** (0-65535) for API consistency.

**Time-of-Flight Sensing** is a method of calculating distance by measuring the precise time elapsed between the emission of a wave (ultrasound) and the detection of its reflection (echo), relying on the constant speed of the wave through the medium (air).

## The 'Why' (The Problem it Solves)

> [!IMPORTANT]
> **The Nightmare Scenario:**
> Without ADC, a microcontroller is legally blind to the real world. Digital GPIO pins are binary—they only understand `HIGH` (3.3V) or `LOW` (0V).
>
> If you connected a temperature sensor to a standard digital pin, you wouldn't know if it's 20°C or 50°C. You would only know if the voltage crossed the hardware logic threshold (e.g., ~2.0V).
>
> **ADC exists to bridge the gap between the infinite resolution of the physical world (Analog) and the discrete, step-based logic of the processor (Digital).**

## The 'Mental Model' (Analogy)

> [!INFO]
> **The Pixelated Ruler**
> Imagine you are trying to measure the height of water in a tank that rises continuously.
>
> * **The Reality (Analog):** The water level is exactly 12.34921... cm. It is smooth and infinite in precision.
> * **The ADC (Digital):** You have a ruler that only has marks every 1 cm.
>     * If the water is at 12.3cm, you write down "12".
>     * If the water is at 12.7cm, you write down "12" (or "13" depending on rounding).
>
> **Resolution (Bit-depth):**
> * A **1-bit ADC** is a ruler with only two marks: Empty (0) or Full (1).
> * A **12-bit ADC** (like the Pico) divides the tank into **4096** tiny tick marks. You still lose the data *between* the marks (quantization error), but it's precise enough for human context.

## The 'How' (Code Anatomy)

### 1. Reading Analog Voltage (The Potentiometer/Temp Sensor)
This pattern reads a raw integer and converts it back to the physical voltage.

```python
import machine
import utime

# ANATOMY NOTE:
# Pin 26 is physically connected to the ADC0 channel.
# We do not use Pin.IN here; we initialize the ADC hardware block.
potentiometer = machine.ADC(26) 

conversion_factor = 3.3 / 65535 

while True:
    # 1. READ_U16 INTERNALS:
    # The RP2040 ADC hardware is 12-bit (0-4095).
    # MicroPython automatically bit-shifts this value to fill 16 bits (0-65535).
    # This is done so code is portable across different microcontrollers.
    raw_value = potentiometer.read_u16() 
    
    # 2. VOLTAGE CALCULATION:
    # We multiply the raw count by the "volts per bit" (3.3V / total steps).
    # This recovers the actual voltage present on the pin.
    voltage = raw_value * conversion_factor
    
    print(f"Raw: {raw_value}, Voltage: {voltage}")
    utime.sleep(0.1)
````

### 2. Time-of-Flight (HC-SR04 Ultrasound)

![[Pasted image 20260214113752.png]]

This pattern measures the width of a digital pulse to calculate distance.

Python

``` python
from machine import Pin
import utime

trigger = Pin(19, Pin.OUT)
echo = Pin(18, Pin.IN)

def get_distance():
    # 1. TRIGGER PULSE
    # We scream into the void for 10 microseconds.
    trigger.low()
    utime.sleep_us(2)
    trigger.high()
    utime.sleep_us(5) # Datasheet requires min 10us usually, lab says 5us
    trigger.low()
    
    # 2. WAIT FOR ECHO HIGH (Blocking Loop)
    # The sensor sets the Echo pin HIGH when it sends the burst.
    # We wait for the pin to go HIGH to mark the start time.
    while echo.value() == 0:
        pass
    start_time = utime.ticks_us() # Snapshot CPU microsecond counter
    
    # 3. WAIT FOR ECHO LOW (Blocking Loop)
    # The sensor sets Echo LOW when the sound bounces back.
    # We wait for this to mark the end time.
    while echo.value() == 1:
        pass
    end_time = utime.ticks_us()
    
    # 4. PHYSICS MATH
    # Duration is the time for sound to go THERE AND BACK.
    duration = end_time - start_time
    
    # Distance = (Time x Speed of Sound) / 2
    # Speed of sound is ~343m/s or 0.0343 cm/us
    # 1 / 0.0343 ≈ 29.1. 
    # The lab manual uses a simplified divisor of 58 for round-trip (29 * 2).
    distance_cm = duration / 58
    
    return distance_cm
```

## 3. Analog Joystick Integration

![[Pasted image 20260214113725.png]]

## The 'What' (Technical Definition)

An **Analog Joystick** is physically two **potentiometers** (variable resistors) mounted perpendicularly (X and Y axes) and one **tactile push-button** (Z axis).

- **X-Axis:** Outputs a variable voltage (0V to 3.3V) based on horizontal position.
    
- **Y-Axis:** Outputs a variable voltage (0V to 3.3V) based on vertical position.
    
- **Switch (SW):** A digital signal (High/Low) indicating a "click."
    
## The 'Mental Model' (Analogy)

> [!INFO]
> 
> **The Voltage Divider**
> 
> Imagine two water pipes connected by a sliding valve.
> 
> - **One pipe is full pressure (3.3V).**
>     
> - **One pipe is empty/drain (GND).**
>     
> 
> The joystick handle is the slider.
> 
> - When centered, it sits exactly in the middle of the flow: **1.65V (Half Pressure)**.
>     
> - Push it fully one way: You open the floodgates to **3.3V**.
>     
> - Push it fully the other: You dump everything to **0V**.
>     
> 
> The ADC measures this pressure to tell the code where the handle is.

## The 'How' (Hardware & Code Anatomy)

### 1. The Wiring (Non-Negotiable)

From **Figure 3 (Page 5)** of the Lab Manual.

|**Joystick Pin**|**Pico Pin**|**Board Pin #**|**Function**|
|---|---|---|---|
|**GND**|AGND|**Pin 33**|Common Ground|
|**+5V**|3V3(OUT)|**Pin 36**|**CRITICAL:** Supply 3.3V, NOT 5V|
|**VRX**|ADC0 / GP26|**Pin 31**|X-Axis Analog Input|
|**VRY**|ADC1 / GP27|**Pin 32**|Y-Axis Analog Input|
|**SW**|GP22|**Pin 29**|Digital Button Input|

### 2. The Code Implementation

This is **Code 2 (Page 5)**, annotated for execution context.

``` python
from machine import Pin, ADC
from time import sleep

# --- HARDWARE CONFIGURATION ---
# GP25 is the internal LED on the Pico
led_onboard = Pin(25, Pin.OUT)

# Initialize ADC (Analog to Digital Converters)
# machine.ADC(26) corresponds to ADC0 on the physical board
# This opens the channel to sample voltage on the X-axis
X_sensor = ADC(26) 

# machine.ADC(27) corresponds to ADC1
# This opens the channel to sample voltage on the Y-axis
Y_sensor = ADC(27)

# Initialize the Push Button
# GP22 is a standard digital pin.
# Pin.PULL_UP is MANDATORY. 
# WHY? The switch connects to Ground when pressed. 
# When NOT pressed, the pin is "floating". PULL_UP forces it to 3.3V (High) internally.
# Pressed = 0 (Low), Released = 1 (High).
SW = Pin(22, Pin.IN, Pin.PULL_UP)

# --- MAIN LOOP ---
while True:
    # 1. READ X AXIS
    # Returns integer 0-65535. 
    # Center position should be roughly ~32768 (half of 65535).
    X_value = X_sensor.read_u16()
    print("X Value:", X_value)
    
    # 2. READ Y AXIS
    Y_value = Y_sensor.read_u16()
    print("Y Value:", Y_value)
    
    # 3. READ BUTTON
    # .value() returns 0 or 1.
    # Because of PULL_UP: 0 means PRESSED, 1 means RELEASED.
    button_state = SW.value()
    
    # Logic: If button is pressed (logic 0? Lab code actually checks for True/1, see note below)
    # NOTE: The lab manual code checks `if button_value == True:`.
    # Since we used PULL_UP, `True` (1) actually means NOT PRESSED.
    # If the LED turns on when you DO NOT touch the button, this logic is inverted.
    if button_state == 1: 
        led_onboard.value(1) # LED ON
    else:
        led_onboard.value(0) # LED OFF
        
    sleep(1) # Wait 1 second before next sample
```

> [!WARNING]
> 
> **Lab Manual Logic Error Alert**
> 
> The lab manual code says: `SW = Pin(22, Pin.IN, Pin.PULL_UP)` and then `if button_value == True: led...value(1)`.
> 
> - **Physical Reality:** A switch connects two points. This switch connects Pin 22 to GND (0V).
>     
> - **Pull Up:** Keeps pin at 1 (True) when open.
>     
> - **Result:** Pressing the button sends a **0 (False)**. Releasing it sends a **1 (True)**.
>     
> - **The Bug:** The code in the manual turns the LED **ON** when you are **NOT** pressing the button.
>
## Edge Cases & Gotchas (The "Junior" Mistakes)

### 1. The 3.3V vs. 5V Trap (Hardware Suicide)

- **The Mistake:** Connecting a 5V sensor (like the HC-SR04 Echo pin) directly to the Pico's GPIO.
    
- **The Consequence:** The RP2040 GPIO pins are **not 5V tolerant**. You will fry the pin or the entire chip.
    
- **The Fix:** Use a voltage divider (resistors) to step 5V down to 3.3V, or rely on the specific tolerance (risky) mentioned in the lab manual which suggests the short pulse duration might save you (do not rely on this in production).
    

### 2. The Floating Pin (Antenna Effect)

- **The Mistake:** Reading `adc.read_u16()` on a pin that has nothing connected to it.
    
- **The Consequence:** You get random, fluctuating values. The pin acts as an antenna picking up electromagnetic noise from the environment (mains hum, Wi-Fi, your hand).
    
- **The Fix:** Always ensure the circuit is closed (connected to a sensor or a pull-up/down resistor).
    

### 3. The "Infinite Loop" Hang

- **The Mistake:** In the ultrasound code, using `while echo.value() == 0: pass` without a timeout.
    
- **The Consequence:** If the sensor disconnects or breaks _during_ operation, the code freezes forever inside the `while` loop waiting for a signal that never comes.
    
- **The Fix:** Implement a counter or `ticks_diff` check inside the loop to `break` if it waits too long.
    

### 4. Floating Point Performance

- **The Mistake:** Doing heavy floating-point math (`distance = duration * 0.017`) inside a high-speed interrupt loop.
    
- **The Consequence:** The RP2040 (Cortex-M0+) has no Floating Point Unit (FPU). It simulates floats in software, which is slow.
    
- **The Fix:** Use integer math where possible (e.g., `duration // 58` instead of `duration * 0.017`).
    

### 5. LDR Non-Linearity

- **The Mistake:** Assuming the LDR resistance changes linearly with light intensity.
    
- **The Reality:** As shown in the lab manual (Fig 7), the relationship is **logarithmic**. You cannot simply linearly map the ADC value to Lux. You need the specific equation derived from the datasheet graph.