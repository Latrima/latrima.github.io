# IoT Lab Programs

This repository contains IoT and Embedded System programs using ESP32, Raspberry Pi, Sensors, and Actuators.

---

# PROGRAM 1

## LDR Brightness Control using ESP32

```cpp
const int sensorPin = 34;
const int ledPin = 18;

float ledVal = 0;
float alpha = 0.15;

void setup() {
    Serial.begin(115200);

    ledcAttach(ledPin, 5000, 8);
}

void loop() {

    int s = analogRead(sensorPin);

    int target = map(s, 0, 4095, 0, 255);

    ledVal += alpha * (target - ledVal);

    ledcWrite(ledPin, (int)ledVal);

    Serial.print("LDR=");
    Serial.print(s);

    Serial.print(" LED=");
    Serial.println((int)ledVal);

    delay(100);
}
```

## Output

```text
LDR=1780 LED=110
LDR=1890 LED=118
LDR=2050 LED=127
LDR=2200 LED=138
```

---

# PROGRAM 2

## Actuator Control using Relay and PIR Sensor

```cpp
int pirPin = 34;
int relayPin = 13;

void setup() {

    Serial.begin(115200);

    pinMode(pirPin, INPUT);
    pinMode(relayPin, OUTPUT);

    digitalWrite(relayPin, LOW);
}

void loop() {

    int motion = digitalRead(pirPin);

    if (motion == HIGH) {

        Serial.println("MOTION DETECTED!!");

        digitalWrite(relayPin, HIGH);

        delay(1000);

    } else {

        Serial.println("NO MOTION DETECTED!!");

        digitalWrite(relayPin, LOW);
    }

    delay(500);
}
```

## Output

```text
NO MOTION DETECTED!!
NO MOTION DETECTED!!
MOTION DETECTED!!
MOTION DETECTED!!
```

---

# PROGRAM 3

## DHT11 Sensor Monitoring using Raspberry Pi

### Step 1: Create Virtual Environment

```bash
python3 -m venv dht-env
```

### Step 2: Activate Environment

```bash
source dht-env/bin/activate
```

### Step 3: Install Required Libraries

```bash
pip install Adafruit_DHT
pip install adafruit-circuitpython-dht
pip install adafruit-blinka
```

### Step 4: Run Python File

```bash
python3 filename.py
```

## Python Code

```python
import time
import board
import adafruit_dht
import csv
from datetime import datetime
import os

# Sensor setup
dht = adafruit_dht.DHT11(board.D4)

# Thresholds
TEMP_THRESHOLD = 35
HUM_THRESHOLD = 80

# CSV file
CSV_FILE = "dht_data.csv"

# Create CSV with header
if not os.path.isfile(CSV_FILE):

    with open(CSV_FILE, 'w', newline='') as f:

        csv.writer(f).writerow(
            ["Date", "Time", "Temperature(C)", "Humidity(%)"]
        )

print("Starting Edge IoT Monitoring System...\n")

while True:

    try:

        t = dht.temperature
        h = dht.humidity

        if t is not None and h is not None:

            now = datetime.now()

            d = now.strftime("%Y-%m-%d")
            tm = now.strftime("%H:%M:%S")

            print(f"Date: {d}")
            print(f"Time: {tm}")

            print(f"Temperature: {t:.1f} °C")
            print(f"Humidity: {h:.1f} %")

            if t > TEMP_THRESHOLD:
                print("⚠ ALERT: High Temperature!")

            if h > HUM_THRESHOLD:
                print("⚠ ALERT: High Humidity!")

            print("-----------------------------------")

            with open(CSV_FILE, 'a', newline='') as f:
                csv.writer(f).writerow([d, tm, t, h])

        else:
            print("Sensor returned no data.")

    except RuntimeError as e:

        print("Sensor failure:", e)

    time.sleep(5)
```

## Output

```text
Starting Edge IoT Monitoring System...

Date: 2026-05-11
Time: 18:45:21
Temperature: 32.0 °C
Humidity: 74.0 %

-----------------------------------
```

---

# PROGRAM 4

## Traffic Monitoring System using IR Sensor

```cpp
#define IR 13
#define B 18
#define O 21
#define R 23

int c10, c60;

unsigned long t10, t60;

bool detected = false;

void setup() {

    pinMode(IR, INPUT);

    pinMode(B, OUTPUT);
    pinMode(O, OUTPUT);
    pinMode(R, OUTPUT);

    Serial.begin(9600);

    Serial.println("Traffic Monitoring Started");

    t10 = t60 = millis();
}

void loop() {

    bool on = !digitalRead(IR);

    if (on && !detected)
        c10++, c60++, detected = true;

    if (!on)
        detected = false;

    if (millis() - t10 >= 10000) {

        digitalWrite(B, 0);
        digitalWrite(O, 0);
        digitalWrite(R, 0);

        Serial.print("10s Vehicles: ");
        Serial.println(c10);

        if (c10 < 5) {

            digitalWrite(B, 1);
            Serial.println("LOW TRAFFIC");

        } else if (c10 < 10) {

            digitalWrite(O, 1);
            Serial.println("MEDIUM TRAFFIC");

        } else {

            digitalWrite(R, 1);
            Serial.println("HEAVY TRAFFIC");
        }

        Serial.println();

        c10 = 0;
        t10 = millis();
    }

    if (millis() - t60 >= 60000) {

        Serial.print("LOG (60s): ");
        Serial.println(c60);

        c60 = 0;
        t60 = millis();
    }
}
```

## Output

```text
Traffic Monitoring Started

10s Vehicles: 3
LOW TRAFFIC

10s Vehicles: 8
MEDIUM TRAFFIC

10s Vehicles: 12
HEAVY TRAFFIC
```

---

# PROGRAM 5

## Pulse Monitoring System using Pulse Sensor

```cpp
#define PULSE_PIN 2

unsigned long lastBeatTime = 0;
unsigned long measureStart = 0;

int beatCount = 0;

float bpm = 0;

void setup() {

    Serial.begin(115200);

    pinMode(PULSE_PIN, INPUT);

    measureStart = millis();

    Serial.println("Edge-Based Remote Health Monitoring Started...");
}

void loop() {

    int pulseValue = analogRead(PULSE_PIN);

    // Simple Peak Detection
    if (pulseValue > 2000) {

        if (millis() - lastBeatTime > 500) {

            beatCount++;

            lastBeatTime = millis();
        }
    }

    // Calculate BPM every 10 seconds
    if (millis() - measureStart >= 10000) {

        bpm = beatCount * 6.0;

        beatCount = 0;

        measureStart = millis();

        Serial.print("Heart Rate: ");
        Serial.print(bpm);
        Serial.println(" BPM");

        // Edge Decision + Doctor Alert
        if (bpm > 100 || bpm < 60) {

            Serial.println("🚨 ALERT: Abnormal Heart Rate! Notify Doctor.");

        } else {

            Serial.println("✅ Status: Normal Heart Rate.");
        }

        Serial.println("----------------------------------");
    }

    delay(20);
}
```

## Output

```text
Heart Rate: 78 BPM
✅ Status: Normal Heart Rate.

----------------------------------

Heart Rate: 110 BPM
🚨 ALERT: Abnormal Heart Rate! Notify Doctor.
```

---

# PROGRAM 6

## Electricity Monitoring System

```cpp
#define I 2
#define L 4

float E;

unsigned long tL, tP;

void setup() {

    pinMode(I, INPUT);
    pinMode(L, OUTPUT);

    Serial.begin(9600);

    tL = millis();
}

void loop() {

    unsigned long t = millis();

    bool on = !digitalRead(I);

    digitalWrite(L, on);

    E += 5.0 * (on ? 0.08 : 0.065) * ((t - tL) / 1000.0);

    tL = t;

    if (t - tP >= 20000) {

        Serial.println("---- Energy Update (20s) ----");

        Serial.print("Voltage (V): ");
        Serial.println(5.0);

        Serial.print("LED State: ");
        Serial.println(on ? "ON" : "OFF");

        Serial.print("Energy used in last interval (J): ");
        Serial.println(E, 3);

        Serial.println();

        E = 0;

        tP = t;
    }
}
```

## Output

```text
---- Energy Update (20s) ----

Voltage (V): 5.0
LED State: ON
Energy used in last interval (J): 7.842
```

---
