# Real-Time Air Quality Prediction Pipeline with Kafka

This project implements a real-time data pipeline for air quality prediction using **Apache Kafka** for streaming and **ElasticNet regression models** for forecasting pollutant levels. It simulates real-time data ingestion, processes incoming records, applies feature engineering, and predicts multiple target pollutants using pre-trained models.

---

## Project Structure

1. **phase_1_kafka_setup**
    - Contains producer.py and consumer.py to simulates real-time streaming prediction integreated with machine learning model.
    - Contains markdown file to explain kafka setup process and data preprocessing process that happen in producer and consumer file.
    - Contains log file for both producer and consumer file to keep tranking the processes.
2. **phase_2_eda**
    - Contains the 02_00_TrainValidationTestSplit.ipynb to perform chronological train validation and test split and export train and validation dataset as .csv
    - Contains the 02_01_00_DataCleaningAndEDA_for_Train.ipynb to perform data cleaning and EDA on train dataset and export cleaned_train_dataset as pickle file at phase_3_model_prediction folder.
    - Contains the 02_01_00_DataCleaningAndEDA_for_Validation.ipynb to perform data cleaning and EDAon validation dataset and export cleaned_validation_dataset as pickle file at phase_3_model_prediction folder.
3. **phase_3_model_prediction**
    - Contains the 03_00_Feature_Engineering.ipynb to perform feature engineering on train and validation dataset and export both cleaned_train_dataset.pkl and cleaned_validation_dataset.pkl
    - Contains the column_order for each pollution to ensure that the test dataset has the same column order with train dataset
    - Contains the scaling logic for each pollution to ensure that the test dataset has the same scaling logic with train dataset
    - Contains the 03_01_Simple_Regression.ipynb, 03_02_ElasticNet_Regression.ipynb, 03_03_LightGBM.ipynb, 03_04_SARIMA.ipynb to build and tune the model for each pollutant and export the best challenger model as pkl file.
    - Contains Model_Performance_Monitoring folder that collect the model prediction value from the consumer.py so that we can perform the model evaluation for test dataset.
    - Contains 04_00_Performance_Monitoring.ipynb to perform the model evalutaion between based-line model and challenger model on test dataset.
    - Contains Performance_on_test_dataset.csv to consolidate all prediction value from both based-line and challenger model as well as the actual value for each time period.
4. **final_report**
    - Contains Final Report.pdf to document all important topics relating to this project.

---

## How It Works

1. **producer.py**
    - Reads the UCI Air Quality dataset
    - Selecting test dataset
    - Simulates real-time streaming using a time delay
    - Publishes JSON-encoded records to Kafka topic (`uci_air_quality_data`)
    - Sends an `end_of_stream` marker when done

2. **consumer.py**
    - Subscribes to the Kafka topic
    - Applies data cleaning, rolling averages, lag features, and dummy encoding
    - Loads pre-trained models and scalers for each target pollutant (`COGT`, `NOxGT`, `NO2GT`, `C6H6GT`)
    - Makes predictions for each incoming record
    - Exports prediction logs to `phase_3_model_prediction/Model_Performance_Monitoring/`