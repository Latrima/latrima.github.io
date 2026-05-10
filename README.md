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

---)

model = RandomForestClassifier()
model.fit(X_train, y_train)

predictions = model.predict(X_test)

print(classification_report(y_test, predictions))
```

## Output

```text
              precision    recall  f1-score   support

           0       0.93      0.94      0.94        87
           1       0.58      0.54      0.56        13

    accuracy                           0.89       100
   macro avg       0.76      0.74      0.75       100
weighted avg       0.89      0.89      0.89       100
```

---

# PROGRAM 2

## Reducing the Dimensionality of the Iris Dataset using PCA

```python
import pandas as pd
from sklearn.datasets import load_iris
from sklearn.decomposition import PCA

iris = load_iris()
X = iris.data

print("Initial Dataset:")
print(pd.DataFrame(X, columns=iris.feature_names).head())

print("\nReduced Dataset after PCA:")
print(
    pd.DataFrame(
        PCA(2).fit_transform(X),
        columns=["PC1", "PC2"]
    ).head()
)
```

## Output

```text
Initial Dataset:

   sepal length (cm)  sepal width (cm)  petal length (cm)  petal width (cm)
0                5.1               3.5                1.4               0.2
1                4.9               3.0                1.4               0.2
2                4.7               3.2                1.3               0.2
3                4.6               3.1                1.5               0.2
4                5.0               3.6                1.4               0.2

Reduced Dataset after PCA:

        PC1       PC2
0 -2.684126  0.319397
1 -2.714142 -0.177001
2 -2.888991 -0.144949
3 -2.745343 -0.318299
4 -2.728717  0.326755
```

---

# PROGRAM 3

## Predicting User Temperature Preference using Linear Regression

```python
import pandas as pd
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split

df = pd.DataFrame({
    'OutsideTemp':[30,35,28,40,25,32,38,27],
    'TimeOfDay':[14,15,10,16,9,13,18,8],
    'Humidity':[60,55,70,50,65,58,45,72],
    'PreferredTemp':[24,25,23,26,22,24,27,22]
})

print("Home Temperature:\n", df)

Xtr, Xte, ytr, yte = train_test_split(
    df.iloc[:,:-1],
    df.iloc[:,-1],
    test_size=0.2,
    random_state=42
)

model = LinearRegression()
model.fit(Xtr, ytr)

print("\nPredicted Temperatures:", model.predict(Xte))
print("Actual Temperature:", yte.values)

print(
    "Predicted Preferred Indoor Temperature:",
    round(model.predict([[34,14,55]])[0], 2),
    "°C"
)
```

## Output

```text
Predicted Temperatures: [25.27947343 24.25451492]

Actual Temperature: [25 24]

Predicted Preferred Indoor Temperature: 24.84 °C
```

---

# PROGRAM 4

## Predicting Indoor Air Quality using Support Vector Regression

```python
import pandas as pd
from sklearn.svm import SVR
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

df = pd.DataFrame({
    'Humidity':[30,35,40,45,50,55,60,70,75],
    'Indoor Air Quality':[85,82,78,75,70,65,60,55,50]
})

print("Environmental Dataset:\n", df)

Xtr, Xte, ytr, yte = train_test_split(
    df[['Humidity']],
    df['Indoor Air Quality'],
    test_size=0.2,
    random_state=42
)

scaler = StandardScaler()

model = SVR(kernel='rbf')
model.fit(scaler.fit_transform(Xtr), ytr)

print("\nPredicted IAQ:", model.predict(scaler.transform(Xte)))
print("Actual IAQ:", yte.values)

print(
    "\nPredicted Indoor Air Quality for 58% humidity:",
    round(model.predict(scaler.transform([[58]]))[0], 2)
)
```

## Output

```text
Predicted IAQ: [68.22292735 72.09819141]

Actual IAQ: [55 82]

Predicted Indoor Air Quality for 58% humidity: 68.36
```

---

# PROGRAM 5

## Predicting Pollution Levels using Decision Tree Regression

```python
import pandas as pd
from sklearn.tree import DecisionTreeRegressor
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score

df = pd.DataFrame({
    'PM2.5':[45,56,78,35,90,110,65,80,40,95],
    'PM10':[60,70,90,50,120,140,85,100,55,130],
    'NO2':[30,35,40,20,50,60,45,55,25,65],
    'SO2':[10,12,15,8,20,25,18,22,9,28],
    'Temperature':[30,32,35,28,38,40,34,36,29,39],
    'Humidity':[65,60,55,70,50,45,58,52,68,48],
    'AQI':[120,140,180,100,220,260,160,200,110,240]
})

Xtr, Xte, ytr, yte = train_test_split(
    df.drop('AQI', axis=1),
    df['AQI'],
    test_size=0.25,
    random_state=1
)

model = DecisionTreeRegressor(max_depth=4, random_state=1)
model.fit(Xtr, ytr)

yp = model.predict(Xte)

print("Actual AQI:", list(yte))
print("Predicted AQI:", list(yp))

print("\nMean Squared Error:", mean_squared_error(yte, yp))
print("R² Score:", r2_score(yte, yp))
```

## Output

```text
Actual AQI: [180, 240, 160]

Predicted AQI: [140.0, 260.0, 200.0]

Mean Squared Error: 1200.0

R² Score: -0.03846153846153855
```

---

# PROGRAM 6

## Forecast Prediction using Naive Bayes

```python
import pandas as pd
from sklearn.preprocessing import LabelEncoder
from sklearn.naive_bayes import GaussianNB

df = pd.DataFrame({
    'Outlook':['Sunny','Sunny','Overcast','Rain','Rain','Rain','Overcast','Sunny','Sunny','Rain','Sunny','Overcast','Overcast','Rain'],
    'Temp':['Hot','Hot','Hot','Mild','Cool','Cool','Mild','Cool','Mild','Mild','Mild','Mild','Hot','Mild'],
    'Humidity':['High','High','High','High','Normal','Normal','Normal','High','Normal','Normal','Normal','High','Normal','High'],
    'Wind':['Weak','Strong','Weak','Weak','Weak','Strong','Strong','Weak','Weak','Weak','Strong','Strong','Weak','Strong'],
    'Play':['No','No','Yes','Yes','Yes','No','Yes','No','Yes','Yes','Yes','Yes','Yes','No']
})

le = LabelEncoder()

df = df.apply(le.fit_transform)

model = GaussianNB()
model.fit(df.iloc[:,:-1], df.iloc[:,-1])

print("Predicted Forecast:", model.predict([[2,2,1,0]]))
```

## Output

```text
Predicted Forecast: [1]
```

---

# PROGRAM 7

## Predicting Heart Rate Condition using Logistic Regression

```python
from sklearn.linear_model import LogisticRegression

model = LogisticRegression()

model.fit(
    [
        [25,72,1],
        [40,90,0],
        [30,75,1],
        [50,110,0],
        [28,68,1],
        [60,120,0]
    ],
    [0,1,0,1,0,1]
)

n = int(input("Enter number of patients: "))

patients = [
    [
        int(input(f"\nPatient {i+1}:\nAge: ")),
        int(input("Heart Rate: ")),
        int(input("Exercise (1=Yes, 0=No): "))
    ]
    for i in range(n)
]

for i, x in enumerate(model.predict(patients)):
    print(
        f"Patient {i+1}: "
        f"{'Normal' if x==0 else 'Abnormal'} Heart Rate"
    )
```

## Output

```text
Patient 1: Normal Heart Rate
Patient 2: Abnormal Heart Rate
Patient 3: Abnormal Heart Rate
```

---

# PROGRAM 8

## Predicting Energy Consumption using Random Forest Regression

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error

df = pd.read_csv(
    r"C:\Users\HP\OneDrive\Documents\23IOTA08\energy_data.csv"
)

Xtr, Xte, ytr, yte = train_test_split(
    df[['Voltage','Current','PowerFactor']],
    df['Energy'],
    test_size=0.2,
    random_state=42
)

model = RandomForestRegressor(random_state=42)

model.fit(Xtr, ytr)

print(
    "Mean Squared Error:",
    mean_squared_error(yte, model.predict(Xte))
)

print(
    "Predicted Energy Consumption:",
    model.predict([[220,5,0.9]])
)
```

## Output

```text
Mean Squared Error: 2427.561266666662

Predicted Energy Consumption: [992.13]
```

---

# PROGRAM 9

## Patient Risk Classification using K-Means Clustering

```python
import pandas as pd
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler
import matplotlib.pyplot as plt

df = pd.DataFrame({
    'HeartRate':[72,85,90,110,60,95,130,70,88,120],
    'BloodPressure':[120,130,135,160,110,145,170,118,140,165],
    'Glucose':[90,110,115,180,85,140,200,95,130,190]
})

scaler = StandardScaler()

df['Cluster'] = KMeans(
    3,
    random_state=42
).fit_predict(
    scaler.fit_transform(df)
)

df['Risk'] = df['Cluster'].map({
    0:"Low",
    1:"Moderate",
    2:"High"
})

print(df)

plt.scatter(
    df['HeartRate'],
    df['Glucose'],
    c=df['Cluster'],
    cmap='viridis'
)

plt.xlabel("Heart Rate")
plt.ylabel("Glucose")
plt.title("Patient Risk Clusters")

plt.colorbar()
plt.show()
```

## Output

```text
   HeartRate  BloodPressure  Glucose  Cluster      Risk
0         72            120       90        1  Moderate
1         85            130      110        2      High
2         90            135      115        2      High
3        110            160      180        0       Low
4         60            110       85        1  Moderate
5         95            145      140        2      High
6        130            170      200        0       Low
7         70            118       95        1  Moderate
8         88            140      130        2      High
9        120            165      190        0       Low
```

## Output Graph

<img width="556" height="453" alt="Patient Risk Clusters" src="https://github.com/user-attachments/assets/f8162afe-438d-4341-b7c4-6e27b17ccd53" />

---
