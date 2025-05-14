# Predictive Maintenance for Air Compressor Systems

## 1. Project Overview

This project focuses on the predictive maintenance of air compressor systems, specifically within the context of a MetroPT3 locomotive's Air Production Unit (APU)[cite: 301, 302, 303, 304, 305, 306, 307, 308, 309, 310, 311, 312, 313, 314, 315, 316, 317]. The primary objective is to analyze sensor data from the APU to detect patterns indicative of impending failures, enabling proactive maintenance and minimizing downtime[cite: 301, 302, 303, 304, 305, 306, 307, 308, 309, 310, 311, 312, 313, 314, 315, 316, 317].

The project involves:

* Analyzing sensor data to identify patterns related to normal operation and potential failures[cite: 302, 303, 304, 305, 306, 307, 308, 309, 310, 311, 312, 313].
* Developing machine learning models to classify the system's state as either healthy or indicative of impending failure[cite: 305, 306, 307, 308, 309, 310, 311, 312, 313].
* Identifying key failure indicators, such as high oil temperature, low oil level, and pressure fluctuations[cite: 314, 315, 316, 317].

## 2. Dataset Description

The dataset used in this project is "MetroPT3(AirCompressor).csv," which contains multivariate time-series data collected from sensors on a compressor in a train[cite: 318, 319, 320, 321, 322, 323].

* **Data Source:** The dataset was collected between February and August 2020 and comprises 1,516,948 data points recorded at a 1Hz frequency[cite: 318, 319, 320, 321, 322, 323].
* **Attributes:** The dataset includes 15 features from 7 analogue and 8 digital sensors[cite: 322, 323]:

    1.  TP2 (bar): Pressure on the compressor[cite: 323].
    2.  TP3 (bar): Pressure at the pneumatic panel[cite: 324].
    3.  H1 (bar): Pressure drop in the cyclonic separator filter[cite: 325].
    4.  DV\_pressure (bar): Pressure drop in the air dryers[cite: 326, 327].
    5.  Reservoirs (bar): Downstream pressure of the reservoirs[cite: 328].
    6.  Motor Current (A): Current of one phase of the motor[cite: 329].
    7.  Oil\_temperature (°C): Oil temperature on the compressor[cite: 330].
    8.  COMP: Electrical signal of the air intake valve[cite: 331].
    9.  DV\_electric: Electrical signal controlling the compressor outlet valve[cite: 332, 333].
    10. TOWERS: Electrical signal indicating the active air drying tower[cite: 334, 335].
    11. MPG: Electrical signal for starting the compressor under load[cite: 336].
    12. LPS: Electrical signal detecting low pressure[cite: 337].
    13. Pressure Switch: Electrical signal detecting discharge in the air-drying towers[cite: 338].
    14. Oil Level: Electrical signal detecting low oil level[cite: 339, 340].
    15. Caudal Impulse: Electrical signal counting air flow from the APU to the reservoirs[cite: 341].

## 3. Project Methodology

The project follows a structured methodology:

### 3.1. Data Loading and Preprocessing

* The dataset is loaded using pandas, and the timestamp column is parsed as datetime[cite: 299, 300, 301].
* Irrelevant columns are dropped[cite: 342, 344].
* The frequency of the timestamp is analyzed, revealing that the time span is not consistent throughout the data[cite: 345, 346, 347].
* The total time span of the data collection is calculated[cite: 346, 347].
* Column names are corrected, and data types and null values are examined using `df.info()`[cite: 347, 348, 349, 351].
* Summary statistics of the data are displayed using `df.describe()`[cite: 351, 353].
* The data is resampled to 10-second intervals, and missing values are imputed using time interpolation[cite: 353, 355, 357, 358].

### 3.2. Failure Data Analysis

* Failure information is extracted from a provided table, noting that all failures are of the same type: "air leak due to high stress"[cite: 358, 359, 360, 361, 362, 363, 364, 365, 366, 368, 369].
* A `df_failures` DataFrame is created to store the failure data, including start and end times[cite: 368, 369].
* Rows corresponding to failure periods are removed from the main DataFrame to create a `normal_op_df` containing only normal operation data[cite: 371, 372, 373, 374, 375, 376, 377, 378, 379, 380, 381, 382, 383].

### 3.3. Pre-Failure DataFrames

* Three DataFrames are created to analyze data leading up to failures at different time scales[cite: 384, 385, 386, 387, 388, 389, 390, 391, 392, 393, 394, 395, 396, 397, 398, 399]:
    * `week_df`: Contains data for one week before each failure, with day-wise timestamps[cite: 386, 387, 388, 389, 390, 391, 392, 393, 394, 395, 396, 397, 398, 399].
    * `hourly_df`: Contains data for one day before each failure, with hour-wise timestamps.
    * `second_wise_df`: Contains data for one hour before each failure, with 10-second timestamps[cite: 394, 395, 396, 397, 398, 399].
* Binary columns (`week_pre_failure`, `hourly_pre_failure`, `second_wise_failure`) are added to `normal_op_df` to indicate data points within these pre-failure periods[cite: 83, 84, 85, 86, 87, 88, 89, 90].

### 3.4. Exploratory Data Analysis (EDA)

* The notebook performs EDA to understand the behavior of the system during normal operation and before failures.
* This includes analyzing the distributions and relationships of sensor readings, such as oil temperature, oil level, and pressure[cite: 159, 160, 170, 171, 172, 173].
* For example, the analysis calculates the ratio of the 'Pressure Switch' signal during normal operation and during the week before failure[cite: 159].
* The average difference in air pressure between the pneumatic panel and the reservoirs is also calculated for week, day and hour before failure[cite: 170, 171, 172, 173].

### 3.5. Feature Selection

* Highly correlated features are identified and may be dropped to avoid redundancy and improve model performance[cite: 173, 174, 175, 176, 177, 178, 179, 180, 181, 182, 183, 184, 185].
* A heatmap is used to visualize the correlations between features[cite: 177, 178, 179, 180, 181, 182, 183, 184, 185, 186].

### 3.6. Model Training and Evaluation

* The dataset is split into training and testing sets[cite: 223].
* Features are scaled using StandardScaler[cite: 223].
* Two machine learning models are trained and evaluated:
    * Logistic Regression[cite: 223, 224, 225, 226, 227, 228, 229, 230, 231, 232, 233, 234, 235, 236, 237, 238, 239, 240].
    * XGBoost Classifier[cite: 241, 242, 243, 244, 245, 246, 247, 248, 249, 250, 251].
* Model performance is evaluated using metrics such as accuracy, precision, recall, F1 score, log loss, and ROC AUC[cite: 223, 224, 225, 226, 227, 228, 229, 230, 231, 232, 233, 234, 235, 236, 237, 238, 239, 240, 241, 242, 243, 244, 245, 246, 247, 248, 249, 250, 251].
* Feature importance is analyzed to identify the most influential factors in predicting failures[cite: 234, 235, 236, 237, 238, 239, 240, 248].

## 4. Key Findings

* The analysis reveals that certain features are particularly important for predicting air leak failures:
    * Rolling average of oil temperature (week-long window)[cite: 249, 250, 251].
    * Rolling average of the difference in pressure between the pneumatic panel and the reservoirs (week and day-long windows)[cite: 170, 171, 172, 173].
    * Rolling sum over a week of the low oil level indicator[cite: 249, 250, 251].
* These findings suggest that changes and trends in oil temperature, oil level, and pressure differences are strong indicators of potential issues[cite: 249, 250, 251].
* The XGBoost Classifier generally outperforms Logistic Regression in predicting these failures[cite: 241, 242, 243, 244, 245, 246, 247, 248, 249, 250, 251].

## 5. Conclusion

This project demonstrates the application of machine learning for predictive maintenance in air compressor systems[cite: 365, 366, 367]. By analyzing sensor data and identifying key predictive features, the developed models can provide early warnings of potential air leak failures[cite: 365, 366, 367]. This can contribute to:

* Reduced downtime[cite: 317].
* Optimized maintenance schedules[cite: 317].
* Improved operational efficiency[cite: 317].

## 6. Future Work

* Further model tuning and optimization to improve prediction accuracy.
* Expanding the analysis to include other types of failures if more data becomes available.
* Developing a real-time monitoring and alert system based on the model.

## 7. Relevant Papers

* \[1] Davari, N., Veloso, B., Ribeiro, R.P., Pereira, P.M., Gama, J.: Predictive maintenance based on anomaly detection using... (Incomplete title in the original notebook) [cite: 299, 300, 301]