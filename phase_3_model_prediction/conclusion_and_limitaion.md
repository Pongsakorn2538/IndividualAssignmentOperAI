# Conclusion and Limitations Document

## Conclusion
In this project, we developed a real-time air quality prediction pipeline using Apache Kafka and machine learning models on the UCI Air Quality dataset. Through careful preprocessing, feature engineering, and exploratory data analysis, we identified temporal patterns and relationships between pollutants.
Among the models tested, 
	For Carbon Monoxide, the Linear Regression with Elastic Net performed best based on MAE and RMSE.
	For Benzene, the simple Linear Regression performed best based on MAE and RMSE.
	For Nitrogen Oxides, the simple Linear Regression performed best based on RMSE.
	For Nitrogen Dioxide, the simple Linear Regression performed best based on MAE and RMSE.
The best-performing models for each pollutant were integrated into a Kafka-based streaming environment, enabling real-time predictions on incoming air quality sensor data.

---

## Limitations
1.	Domain Knowledge 
    - We don’t know the causal relationships between each pollutant. For example, certain pollutants may lead to the formation of others under specific environmental conditions. Understanding these dependencies could help in preventing data leakage, especially when designing relevant model features and lagged features
    - We don’t know how long each pollutant typically persists in the atmosphere. This would allow us to more effectively engineer lag features and rolling windows. As a result, it will reduce the complexity of model and computing power.
2.	Data
    - We should aim to collect more comprehensive and recent air quality data to enable training and validation across a full year. A larger and more diverse dataset would improve model generalization and robustness.
    - We should incorporate external datasets, such as traffic flow data, weather conditions, or industrial activity logs, which could significantly enhance model performance. These variables are theoretically known to influence pollutant levels and would provide additional context for prediction.
3.	Model
    - We should explore alternative modeling approaches, including deep learning techniques like LSTM or Temporal Convolutional Networks (TCNs), which may yield better performance, especially for capturing complex temporal patterns.
    - For SARIMA, we should investigate using automated hyperparameter selection tools to streamline model tuning. Relying solely on visual interpretation of ACF and PACF plots can be subjective and may not always result in the most optimal configuration.
