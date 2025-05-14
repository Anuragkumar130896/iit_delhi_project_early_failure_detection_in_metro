# Predictive Maintenance for Air Compressor Systems

## 1. Project Overview

This project focuses on the predictive maintenance of air compressor systems, specifically within the context of a MetroPT3 locomotive's Air Production Unit (APU) . The primary objective is to analyze sensor data from the APU to detect patterns indicative of impending failures, enabling proactive maintenance and minimizing downtime

The project involves:

* Analyzing sensor data to identify patterns related to normal operation and potential failures
* Developing machine learning models to classify the system's state as either healthy or indicative of impending failure
* Identifying key failure indicators, such as high oil temperature, low oil level, and pressure fluctuations

## 2. Dataset Description

The dataset used in this project is "MetroPT3(AirCompressor).csv," which contains multivariate time-series data collected from sensors on a compressor in a train
* **Data Source:** The dataset was collected between February and August 2020 and comprises  data points recorded at a 1Hz frequency
* **Attributes:** The dataset includes 15 features from 7 analogue and 8 digital sensors
    1.  TP2 (bar): Pressure on the compressor.
    2.  TP3 (bar): Pressure at the pneumatic panel.
    3.  H1 (bar): Pressure drop in the cyclonic separator filter.
    4.  DV\_pressure (bar): Pressure drop in the air dryers
    5.  Reservoirs (bar): Downstream pressure of the reservoirs
    6.  Motor Current (A): Current of one phase of the motor
    7.  Oil\_temperature (°C): Oil temperature on the compressor
    8.  COMP: Electrical signal of the air intake valve.
    9.  DV\_electric: Electrical signal controlling the compressor outlet valve
    10. TOWERS: Electrical signal indicating the active air drying tower
    11. MPG: Electrical signal for starting the compressor under load.
    12. LPS: Electrical signal detecting low pressure
    13. Pressure Switch: Electrical signal detecting discharge in the air-drying towers.
    14. Oil Level: Electrical signal detecting low oil level
    15. Caudal Impulse: Electrical signal counting air flow from the APU to the reservoirs.

## 3. Project Methodology

The project follows a structured methodology:

### 3.1. Data Loading and Preprocessing

* The dataset is loaded using pandas, and the timestamp column is parsed as datetime.
* Irrelevant columns are dropped.
* The frequency of the timestamp is analyzed, revealing that the time span is not consistent throughout the data
* The total time span of the data collection is calculated
* Column names are corrected, and data types and null values are examined using `df.info()`
* Summary statistics of the data are displayed using `df.describe()`.
* The data is resampled to 10-second intervals, and missing values are imputed using time interpolation
### 3.2. Failure Data Analysis

* Failure information is extracted from a provided table, noting that all failures are of the same type: "air leak due to high stress"
* A `df_failures` DataFrame is created to store the failure data, including start and end times[].
* Rows corresponding to failure periods are removed from the main DataFrame to create a `normal_op_df` containing only normal operation data
### 3.3. Pre-Failure DataFrames

* Three DataFrames are created to analyze data leading up to failures at different time scales
    * `week_df`: Contains data for one week before each failure, with day-wise timestamps
    * `hourly_df`: Contains data for one day before each failure, with hour-wise timestamps.
    * `second_wise_df`: Contains data for one hour before each failure, with 10-second timestamps
* Binary columns (`week_pre_failure`, `hourly_pre_failure`, `second_wise_failure`) are added to `normal_op_df` to indicate data points within these pre-failure periods
### 3.4. Exploratory Data Analysis (EDA)

* The notebook performs EDA to understand the behavior of the system during normal operation and before failures.
* This includes analyzing the distributions and relationships of sensor readings, such as oil temperature, oil level, and pressure
* For example, the analysis calculates the ratio of the 'Pressure Switch' signal during normal operation and during the week before failure.
* The average difference in air pressure between the pneumatic panel and the reservoirs is also calculated for week, day and hour before failure

### 3.5. Feature Selection

* Highly correlated features are identified and may be dropped to avoid redundancy and improve model performance
* A heatmap is used to visualize the correlations between features

### 3.6. Model Training and Evaluation

* The dataset is split into training and testing sets.
* Features are scaled using StandardScaler.
* Two machine learning models are trained and evaluated:
    * Logistic Regression.
    * XGBoost Classifier
* Model performance is evaluated using metrics such as accuracy, precision, recall, F1 score, log loss, and ROC AUC
* Feature importance is analyzed to identify the most influential factors in predicting failures
## 4. Key Findings

* The analysis reveals that certain features are particularly important for predicting air leak failures:
    * Rolling average of oil temperature (week-long window)
    * Rolling average of the difference in pressure between the pneumatic panel and the reservoirs (week and day-long windows)
    * Rolling sum over a week of the low oil level indicator
* These findings suggest that changes and trends in oil temperature, oil level, and pressure differences are strong indicators of potential issues
* The XGBoost Classifier generally outperforms Logistic Regression in predicting these failures

## 5. Conclusion

This project demonstrates the application of machine learning for predictive maintenance in air compressor systems. By analyzing sensor data and identifying key predictive features, the developed models can provide early warnings of potential air leak failures[cite: 365, 366, 367]. This can contribute to:

* Reduced downtime
* Optimized maintenance schedules
* Improved operational efficiency.

## 6. Future Work

* Further model tuning and optimization to improve prediction accuracy.
* Expanding the analysis to include other types of failures if more data becomes available.
* Developing a real-time monitoring and alert system based on the model.

## 7. Relevant Papers

* \[1] Davari, N., Veloso, B., Ribeiro, R.P., Pereira, P.M., Gama, J.: Predictive maintenance based on anomaly detection using... (Incomplete title in the original notebook) [cite: 299, 300, 301]