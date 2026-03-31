
# Lab 10 — IoT with Pico W and Blynk Cloud

## 1) Lab Overview

This lab introduces how to connect a **Raspberry Pi Pico W** to an **IoT cloud platform** (Blynk) so your physical device can be monitored and controlled remotely.

### Why Blynk?
Blynk is used because:
- It has a useful **free tier**
- Setup is relatively simple
- It provides:
  - Device templates
  - Datastreams (virtual pins)
  - Web and mobile dashboards

---

## 2) IoT Concepts You Need First

## 2.1 What is IoT in this lab?
IoT (Internet of Things) means your microcontroller (Pico W) connects to internet services and exchanges data with a cloud dashboard.

In this setup:

- **Pico W** = device node (sensor + actuator)
- **Blynk Cloud** = message broker + management
- **Dashboard** = user interface (switches/charts/labels)

---

## 2.2 Templates vs Devices (very important)

### Template
A reusable “blueprint” defining:
- Datastreams (V0, V1, V2...)
- Dashboards/widgets
- Hardware & connection metadata

### Device
An actual instance created from a template.
- Each device gets unique identity/auth credentials
- Multiple devices can share one template design

**Key idea:**  
Template = product model  
Device = one physical unit of that model

---

## 2.3 Datastreams and Virtual Pins

A **datastream** defines how data is represented (type/range/unit).  
A **virtual pin** is the software channel used in code (e.g., V0, V1).

- On Pico side: you read/write virtual pins with Blynk library
- On Cloud side: widgets bind to those same datastreams

This creates a clean separation from hardware GPIO pins.

---

## 3) Step-by-Step: Blynk Cloud Setup

## 3.1 Create Blynk account
1. Go to: `https://blynk.cloud/dashboard/login`
2. Sign up/login
3. Open dashboard console

Docs:
- Signup: `https://docs.blynk.io/en/getting-started/signup`
- Main docs: `https://docs.blynk.io/en`

---

## 3.2 Create a new template
In **Developer Zone**:
1. Click **New Template**
2. Set:
   - Name: (your choice)
   - Hardware: **Raspberry Pi**
   - Connection: **WiFi**
   - Description: optional
3. Click **Done**

---

## 3.3 Define datastreams (core communication layer)

Create these 3 virtual pin datastreams:

### Datastream 1 — LED control
- Name: `Control LED Int V0`
- Type: Integer
- Min: 0
- Max: 1
- Pin: V0

Purpose: receive button state from cloud and control onboard LED.

---

### Datastream 2 — Temperature
- Name: `Temperature Double V1`
- Type: Double
- Min: 0
- Max: 100
- Unit: °C
- Decimals: 2
- Pin: V1

Purpose: send floating-point temperature readings from Pico to cloud.

---

### Datastream 3 — Uptime
- Name: `Uptime Int V2`
- Type: Integer
- Min: 0
- Max: 10,000,000
- Unit: seconds (optional text)
- Pin: V2

Purpose: send elapsed connected time to verify device is alive.

> Save template after creating datastreams.

---

## 3.4 Build Web Dashboard
Open **Web Dashboard** editor and add:

1. **Switch widget**
   - Bind to: `Control LED Int V0`
   - Optional labels: ON/OFF

2. **Chart widget**
   - Bind to: `Temperature Double V1`
   - Optional: Show Y-axis + Autoscale

3. **Label widget**
   - Bind to: `Uptime Int V2`

Save dashboard.

---

## 3.5 Create device from template
1. Go to **Devices**
2. Click **New Device**
3. Choose **From Template**
4. Select your template

Blynk creates a real device instance and gives credentials including:

- `BLYNK_AUTH_TOKEN` (critical unique token)

You will paste this token into `keys.py`.

---

## 4) Pico W Setup in Thonny

## 4.1 Upload Blynk library
- Download `BlynkLib.py` (from your course source or official repo)
- In Thonny Files panel: right-click `BlynkLib.py` → **Upload to /**
- Ensure file is on Pico filesystem

---

## 4.2 Create `keys.py` (secrets file)
Create a file named `keys.py` and store private credentials:

```python name=keys.py
BLYNK_AUTH_TOKEN = "YOUR_DEVICE_TOKEN"
WIFI_NAME = "YOUR_WIFI_SSID"
WIFI_PASSWORD = "YOUR_WIFI_PASSWORD"
```

### Why this matters
- Keeps secrets separate from main logic
- Safer sharing: you can share `main.py` but **never** share `keys.py`

> If using a shared lab Pico, remove your secrets afterward.

---

## 5) Main Program Logic (Deep Explanation)

Your `main.py` does five jobs:

1. Initialize hardware (LED + ADC temp sensor)
2. Connect Pico W to Wi-Fi
3. Connect to Blynk cloud with auth token
4. Listen for remote switch changes on V0
5. Periodically send temperature (V1) and uptime (V2)

---

## 5.1 Full code (cleaned)

```python name=main.py
from machine import Pin, ADC
import network
from time import sleep, time
import BlynkLib
import keys

# Hardware setup
temp_sens = ADC(4)              # Internal temperature sensor
led = Pin("LED", Pin.OUT)       # Pico W onboard LED

def connect_to_WiFi(ssid, password):
    wlan = network.WLAN(network.STA_IF)
    wlan.active(True)
    wlan.connect(ssid, password)

    max_wait = 10
    while max_wait > 0:
        if wlan.status() < 0 or wlan.status() >= 3:
            break
        max_wait -= 1
        print("waiting for connection...")
        sleep(1)

    if wlan.status() != 3:
        print("Wi-Fi status:", wlan.status())
        raise RuntimeError("network connection failed")
    else:
        print("connected")
        print("Wi-Fi status:", wlan.status())
        print("IP config:", wlan.ifconfig())

# 1) Connect Wi-Fi
connect_to_WiFi(keys.WIFI_NAME, keys.WIFI_PASSWORD)

# 2) Start timer for uptime
start_time = time()

# 3) Create Blynk client
blynk = BlynkLib.Blynk(keys.BLYNK_AUTH_TOKEN, insecure=True)

# 4) Listener for virtual pin V0 (switch widget)
@blynk.on("V0")
def v0_write_handler(value):
    print("Current button value:", value[0])
    if value[0] == "0":
        led.value(0)
    else:
        led.value(1)

# 5) Main loop: send telemetry + run blynk event loop
while True:
    adc_value = temp_sens.read_u16()
    temp_v = adc_value * 3.3 / 65535
    temp_c = 27 - (temp_v - 0.706) / 0.001721

    print(str(round(temp_c, 2)) + " C")

    # Send sensor data to Blynk
    blynk.virtual_write(1, temp_c)             # V1 temperature
    blynk.virtual_write(2, int(time() - start_time))  # V2 uptime in seconds

    # Process incoming/outgoing Blynk traffic
    blynk.run()

    sleep(2)
```

---

## 5.2 How each Blynk part works

### `blynk = BlynkLib.Blynk(token, insecure=True)`
Creates session to cloud using your device token.

### `@blynk.on("V0")`
Registers callback for incoming writes to V0 from dashboard switch.

### `blynk.virtual_write(1, temp_c)`
Sends temperature to datastream V1.

### `blynk.virtual_write(2, uptime)`
Sends uptime seconds to V2.

### `blynk.run()`
Processes network communication and callbacks.  
Without this, dashboard interactions won’t update correctly.

---

## 5.3 Temperature conversion note
You are converting raw ADC to voltage, then voltage to °C using RP2040 internal sensor approximation.

Equivalent formula used often:
- `T = 27 - (V - 0.706) / 0.001721`

Sensor is okay for rough trends, not precise calibration-grade measurement.

---

## 6) End-to-End Data Flow

1. You toggle switch on dashboard
2. Blynk cloud writes value to V0
3. Pico callback `v0_write_handler()` executes
4. LED turns on/off

At same time:

1. Pico reads internal temperature
2. Pico sends value to V1
3. Dashboard chart updates
4. Pico sends uptime to V2
5. Label updates

---

## 7) Common Problems + Fixes

## 7.1 Device stays offline
- Wrong SSID/password in `keys.py`
- `BLYNK_AUTH_TOKEN` copied incorrectly
- `BlynkLib.py` not uploaded to Pico
- Wi-Fi network blocks device

## 7.2 Switch does nothing
- Datastream pin mismatch (dashboard V0 vs code listener V0)
- `blynk.run()` not called frequently enough
- LED pin incorrect (use `Pin("LED", Pin.OUT)` on Pico W)

## 7.3 No chart updates
- Writing to wrong pin (`virtual_write(1, ...)` must match V1)
- Widget not bound to right datastream
- Loop crashed silently (check Thonny shell errors)

## 7.4 Unstable connection
- Reduce loop blocking time
- Keep `blynk.run()` called often
- Ensure power and Wi-Fi signal are stable

---

## 8) Security Best Practices (Important)

- Never commit/share `keys.py`
- Rotate token if leaked
- Use separate credentials for personal hotspots
- Remove secret files from shared lab boards after finishing

---

## 9) Optional: Mobile Dashboard (Blynk.App)

1. Install Blynk.App (Android/iOS)
2. Login
3. Enable Developer Mode
4. Open your template
5. Build mobile dashboard widgets (similar to web)

---
