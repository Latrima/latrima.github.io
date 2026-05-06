**#PROGRAM1

1. Preprocessing IoT sensor dataset , train a machine learning model to predict sensor failure
**

import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import classification_report
df = pd.read_csv(r"C:\Users\HP\OneDrive\Documents\\iot_dataset_pg1.csv")
X = df[["temp","vibration","humidity","voltage"]]
y = df["failure"]
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
print(classification_report(y_test,RandomForestClassifier().fit(X_train, y_train).predict(X_test)))
              precision    recall  f1-score   support

           0       0.93      0.94      0.94        87
           1       0.58      0.54      0.56        13

    accuracy                           0.89       100
   macro avg       0.76      0.74      0.75       100
weighted avg       0.89      0.89      0.89       100
**
#PROGRAM2

2. Reducing the dimensionality of the iris dataset using principal component analysis**

import pandas as pd
from sklearn.datasets import load_iris
from sklearn.decomposition import PCA

iris = load_iris()
X = iris.data

print("Initial Dataset:")
print(pd.DataFrame(X, columns=iris.feature_names).head())

print("\nReduced Dataset after PCA:")
print(pd.DataFrame(PCA(2).fit_transform(X), columns=["PC1","PC2"]).head())
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

**
#PROGRAM3

3. Using home temperature dataset, train a linear regression model to predict user temperature preference
**
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

Xtr, Xte, ytr, yte = train_test_split(df.iloc[:,:-1], df.iloc[:,-1], test_size=0.2, random_state=42)
m = LinearRegression().fit(Xtr, ytr)

print("\n Predicted Temperatures:", m.predict(Xte))
print("Actual Temperature:", yte.values)
print("Predicted Preferred Indoor Temperature:", round(m.predict([[34,14,55]])[0],2), "°C")
Home Temperature:
    OutsideTemp  TimeOfDay  Humidity  PreferredTemp
0           30         14        60             24
1           35         15        55             25
2           28         10        70             23
3           40         16        50             26
4           25          9        65             22
5           32         13        58             24
6           38         18        45             27
7           27          8        72             22

 Predicted Temperatures: [25.27947343 24.25451492]
Actual Temperature: [25 24]
Predicted Preferred Indoor Temperature: 24.84 °C
C:\ProgramData\anaconda3\Lib\site-packages\sklearn\base.py:493: UserWarning: X does not have valid feature names, but LinearRegression was fitted with feature names
  warnings.warn(
**
  
#PROGRAM4

4. Applying support vector machine regression model on environmental dataset (humidity) and predict indoor air quality
**
import pandas as pd
from sklearn.svm import SVR
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

df = pd.DataFrame({
    'Humidity':[30,35,40,45,50,55,60,70,75],
    'Indoor Air Quality':[85,82,78,75,70,65,60,55,50]
})

print("Environmental Dataset:\n", df)

Xtr,Xte,ytr,yte = train_test_split(df[['Humidity']], df['Indoor Air Quality'], test_size=0.2, random_state=42)

sc = StandardScaler()
m = SVR(kernel='rbf').fit(sc.fit_transform(Xtr), ytr)

print("\nPredicted IAQ:", m.predict(sc.transform(Xte)))
print("Actual IAQ:", yte.values)
print("\nPredicted Indoor Air Quality for 58% humidity:", round(m.predict(sc.transform([[58]]))[0],2))
Environmental Dataset:
    Humidity  Indoor Air Quality
0        30                  85
1        35                  82
2        40                  78
3        45                  75
4        50                  70
5        55                  65
6        60                  60
7        70                  55
8        75                  50

Predicted IAQ: [68.22292735 72.09819141]
Actual IAQ: [55 82]

Predicted Indoor Air Quality for 58% humidity: 68.36
C:\ProgramData\anaconda3\Lib\site-packages\sklearn\base.py:493: UserWarning: X does not have valid feature names, but StandardScaler was fitted with feature names
  warnings.warn(

  **
#PROGRAM5

5. Building a decision tree regression model to predict pollution levels in smart city pollution sensor dataset, and evaluate its performance
**
import pandas as pd
from sklearn.tree import DecisionTreeRegressor
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error,r2_score

df=pd.DataFrame({'PM2.5':[45,56,78,35,90,110,65,80,40,95],'PM10':[60,70,90,50,120,140,85,100,55,130],'NO2':[30,35,40,20,50,60,45,55,25,65],'SO2':[10,12,15,8,20,25,18,22,9,28],'Temperature':[30,32,35,28,38,40,34,36,29,39],'Humidity':[65,60,55,70,50,45,58,52,68,48],'AQI':[120,140,180,100,220,260,160,200,110,240]})

Xtr,Xte,ytr,yte=train_test_split(df.drop('AQI',axis=1),df['AQI'],test_size=0.25,random_state=1)
m=DecisionTreeRegressor(max_depth=4,random_state=1).fit(Xtr,ytr)
yp=m.predict(Xte)

print("Actual AQI:",list(yte))
print("Predicted AQI:",list(yp))
print("\nMean Squared Error:",mean_squared_error(yte,yp))
print("R² Score:",r2_score(yte,yp))
Actual AQI: [180, 240, 160]
Predicted AQI: [140.0, 260.0, 200.0]

Mean Squared Error: 1200.0
R² Score: -0.03846153846153855


**#PROGRAM6

6. Predicting the forecast of the day with the help of a weather dataset using Naive Bayes
**

import pandas as pd
from sklearn.preprocessing import LabelEncoder
from sklearn.naive_bayes import GaussianNB

df=pd.DataFrame({'Outlook':['Sunny','Sunny','Overcast','Rain','Rain','Rain','Overcast','Sunny','Sunny','Rain','Sunny','Overcast','Overcast','Rain'],
'Temp':['Hot','Hot','Hot','Mild','Cool','Cool','Mild','Cool','Mild','Mild','Mild','Mild','Hot','Mild'],
'Humidity':['High','High','High','High','Normal','Normal','Normal','High','Normal','Normal','Normal','High','Normal','High'],
'Wind':['Weak','Strong','Weak','Weak','Weak','Strong','Strong','Weak','Weak','Weak','Strong','Strong','Weak','Strong'],
'Play':['No','No','Yes','Yes','Yes','No','Yes','No','Yes','Yes','Yes','Yes','Yes','No']})

le=LabelEncoder()
df=df.apply(le.fit_transform)

m=GaussianNB().fit(df.iloc[:,:-1],df.iloc[:,-1])
print("Predicted Forecast:",m.predict([[2,2,1,0]]))
Predicted Forecast: [1]
C:\ProgramData\anaconda3\Lib\site-packages\sklearn\base.py:493: UserWarning: X does not have valid feature names, but GaussianNB was fitted with feature names
  warnings.warn(

  
**#PROGRAM7

7. Using Logistic regression to predicate the heart rate is normal or abnormal
**
from sklearn.linear_model import LogisticRegression

m=LogisticRegression().fit([[25,72,1],[40,90,0],[30,75,1],[50,110,0],[28,68,1],[60,120,0]],[0,1,0,1,0,1])

n=int(input("Enter number of patients: "))
p=[[int(input(f"\nPatient {i+1}:\n Age: ")),int(input(" Heart Rate: ")),int(input(" Exercise (1=Yes, 0=No): "))] for i in range(n)]

for i,x in enumerate(m.predict(p)):
    print(f"Patient {i+1}: {'Normal' if x==0 else 'Abnormal'} Heart Rate")
Patient 1: Normal Heart Rate
Patient 2: Abnormal Heart Rate
Patient 3: Abnormal Heart Rate


**#PROGRAM8

8. Predicting the power consumption using IoT Energy consumption dataset using random forest model
**
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error

df=pd.read_csv(r"C:\Users\HP\OneDrive\Documents\23IOTA08\energy_data.csv")
Xtr,Xte,ytr,yte=train_test_split(df[['Voltage','Current','PowerFactor']],df['Energy'],test_size=0.2,random_state=42)

m=RandomForestRegressor(random_state=42).fit(Xtr,ytr)

print("Mean Squared Error:",mean_squared_error(yte,m.predict(Xte)))
print("Predicted Energy Consumption:",m.predict([[220,5,0.9]]))
Mean Squared Error: 2427.561266666662
Predicted Energy Consumption: [992.13]
C:\ProgramData\anaconda3\Lib\site-packages\sklearn\base.py:493: UserWarning: X does not have valid feature names, but RandomForestRegressor was fitted with feature names
  warnings.warn(

  
**#PROGRAM9

9. Using K - Mean clustering to group IoT based patients data on vitual signs like heart rate, blood pressure, and glucose levels to identity high risk, moderate -risk and low risk patients
**
import pandas as pd
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler
import matplotlib.pyplot as plt

df=pd.DataFrame({'HeartRate':[72,85,90,110,60,95,130,70,88,120],
'BloodPressure':[120,130,135,160,110,145,170,118,140,165],
'Glucose':[90,110,115,180,85,140,200,95,130,190]})

df['Cluster']=KMeans(3,random_state=42).fit_predict(StandardScaler().fit_transform(df))
df['Risk']=df['Cluster'].map({0:"Low",1:"Moderate",2:"High"})

print(df)

plt.scatter(df['HeartRate'],df['Glucose'],c=df['Cluster'],cmap='viridis')
plt.xlabel("Heart Rate"); plt.ylabel("Glucose"); plt.title("Patient Risk Clusters")
plt.colorbar(); plt.show()
C:\ProgramData\anaconda3\Lib\site-packages\sklearn\cluster\_kmeans.py:1429: UserWarning: KMeans is known to have a memory leak on Windows with MKL, when there are less chunks than available threads. You can avoid it by setting the environment variable OMP_NUM_THREADS=1.
  warnings.warn(
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
No description has been provided for this image



 <img width="556" height="453" alt="image" src="https://github.com/user-attachments/assets/f8162afe-438d-4341-b7c4-6e27b17ccd53" />

