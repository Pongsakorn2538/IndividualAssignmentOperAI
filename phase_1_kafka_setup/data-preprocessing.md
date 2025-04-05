# Data Preprocessing Strategy Documentation

## Introduction

This document outlines the data preprocessing steps applied to producer and consumer python file. The preprocessing strategy ensures that the dataset is clean, consistent, and ready for downstream tasks.

## Data Source

- **Dataset:** [UCI Air Quality Data Set](https://archive.ics.uci.edu/dataset/360/air+quality)
- **Accessed via:** `fetch_ucirepo(id=360)` from the `ucimlrepo` package
- **Content:** Time-series air pollution data collected in an Italian city over several months

## Data Preprocessing Strategy 

1. **Producer.py**
  - Reads the UCI Air Quality dataset
  - Timestamp Parsing and Sorting
    - The dataset contains separate `Date` and `Time` columns.
    - These are combined into a single `date_time` column using `pd.to_datetime()`.
    - All rows are sorted chronologically by `date_time` to preserve temporal order.
    - After sorting, the `date_time` column is dropped for simplicity.
  - Test Dataset Selection
    - Only records **after December 31, 2004** are retained.
    - This is done to simulate a testing environment where the model has not seen these data beforehand.
  - Cleanup
    - Converts the Date back to a string for serialization and drops the temporary date_time column.
  - Error Handling
    - Raises a `ValueError` if the dataset fails to load.
    - Raises a `KeyError` if either the `Date` or `Time` columns are missing.
    - Logs exceptions and data shapes using the `logging` module.
    - Ensures that any issues during preprocessing are traceable through the `producer.log` file.

2. **Consumer.py**
  - Subscribes to the topic (uci_air_quality_data) using confluent_kafka.Consumer.
  - Logs all Kafka activity to a file named consumer.log in the current directory.
  - Loads the last 25 rows from the validation dataset cleaned_validation_dataset.pkl as historical context
  - Incoming JSON messages from Kafka are converted into a one-row DataFrame df_msg.
  - Timestamp & Temporal Features
    - Combines Date and Time columns into a date_time column.
    - Extracts day_of_week and hour from date_time.
    - Sorts and resets index after appending each new record to df_test.
  - Data Cleaning
    - Replaces -200 values with NaN for all numeric features.
    - Based on the EDA process, the data presented the hourly/day of the week patterns. Therefore, the system will use Forward-fills missing values to capture those trend.
    - Drops irrelevant columns: NMHC(GT), PT08.S2(NMHC), Date, and Time.
  - Feature Engineering
    - Rolling statistics:
      - Computes rolling mean and standard deviation for each numeric column for window sizes from 2 to 24.
    - Lag features:
      - Creates lagged versions (1–24 lags) for each target variable.
    - Dummy variables:
      - One-hot encodes hour and day_of_week.
      - Adds missing dummy columns to maintain consistency with the training dataset.
      - Drops hour_0 and day_of_week_Sunday to avoid multicollinearity.
  - Model Prediction For each target (COGT, C6H6GT, NOxGT, NO2GT):
    - Loads the scaler for each target variable (scaler_for_<target>.pkl) and applies scaling to selected columns to maintain consistency with the training dataset.
    - Aligns the column order using column_orderfor_<target>.json.
    - Loads the trained ElasticNet model (elasticnet_model_for_<target>.pkl) and performs prediction.
    - Appends the predicted and actual values to tracking lists for performance evaluation.
  - Output and Logging
    - After each message is processed, four JSON files are written (one for each tracked list):
      - performance_date_time_asof_<timestamp>.json
      - performance_target_val_asof_<timestamp>.json
      - performance_predict_val_asof_<timestamp>.json
      - performance_actual_val_asof_<timestamp>.json
    - These files are saved in the Model_Performance_Monitoring directory for monitoring and auditability.
    - Any exception during message processing is logged using logger.exception(...).
  - Error Handling
    - Kafka Polling Errors prevents malformed messages from crashing the consumer loop.
    - Message Processing Errors ensures that if one message causes an error (e.g., malformed JSON, missing columns, or model input mismatch), it is logged and skipped without terminating the stream.
    - Kafka Shutdown ensures the Kafka consumer is always closed cleanly