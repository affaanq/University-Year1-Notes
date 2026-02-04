# Lab 1: Raspberry Pi Pico & MicroPython Fundamentals

This lab covers the transition from general-purpose Python to **MicroPython** for embedded systems using the **RP2040** microcontroller.

---

## 1. Core Hardware: Raspberry Pi Pico

The Pico is a microcontroller, not a full computer like the Raspberry Pi 4/5. It lacks an Operating System (OS) and runs code directly on the "bare metal."

### Pinout Essentials

- **RP2040 MCU:** The brain of the board.
    
- **GPIO Pins:** General Purpose Input/Output. These pins can be programmed to be either inputs (sensors) or outputs (LEDs, motors).
    
- **Power:** Typically powered via USB (5V), but internally operates at **3.3V**.
    
- **Onboard LED:** * **Standard Pico:** Internally connected to **GP25**.
    
    - **Pico W (Wi-Fi):** Accessed via the string `"LED"`.
        

---

## 2. Software Environment (Thonny IDE)

To program the Pico, the environment must be configured to talk to the hardware via a serial connection (COM port).

- **Interpreter:** You must switch Thonny from "Local Python 3" to **"MicroPython (Raspberry Pi Pico)"** in the bottom-right corner.
    
- **The Shell:** The bottom window in Thonny is a **REPL** (Read-Eval-Print Loop). You can type commands here to execute them instantly on the Pico without saving a file.
    
- **File Naming:** Files saved as `main.py` on the Pico will run automatically whenever the board is powered on.
    

---

## 3. Code Breakdown:


Python

```
from machine import Pin
from time import sleep

my_led = Pin(25, Pin.OUT)

while True;

    my_led.value(1)
    sleep(0.5)
    
    my_led.value(0)
    sleep(0.5)
```

### Analysis of Instructions

|**Component**|**Function**|**Detail**|
|---|---|---|
|`from machine import Pin`|Hardware Access|Imports the library needed to control physical pins.|
|`Pin(25, Pin.OUT)`|Configuration|Sets Pin 25 as an **Output**. Using `Pin.IN` would be for sensors.|
|`while True:`|Infinite Loop|Embedded programs usually run in a "super-loop" to stay active.|
|`.value(1)`|High Logic|Sends 3.3V to the pin (Turns LED ON).|
|`.value(0)`|Low Logic|Sends 0V to the pin (Turns LED OFF).|
|`sleep(0.5)`|Delay|Pauses execution for 500ms. Without this, the LED would blink too fast for the human eye to see.|

---

## 4. Advanced Interaction: Console I/O

Microcontrollers often interact with a computer via the USB-Serial port for debugging.

- **`print()`**: Sends data from the Pico _to_ your computer screen (Thonny Shell).
    
- **`input()`**: Sends data from your keyboard _to_ the Pico. **Warning:** `input()` is a "blocking" function. The Pico will stop all code execution (including blinking) until you press "Enter" in the console.
    
- **Type Casting**: `input()` always returns a **string**. If you need a number for calculations (like a loop counter), you must wrap it in `int()`.
    

> [!IMPORTANT]
> 
> **Indentation Matters:** In Python, spaces define the "scope" of a loop. If `my_led.value(0)` is not aligned with `my_led.value(1)`, it is not part of the same loop.

---
