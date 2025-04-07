# EDA Findings and Data Cleaning Strategy Documentation

## Introduction
Before developing any analytical or predictive models, it is essential to thoroughly understand the dataset. This section presents key insights uncovered through exploratory data analysis (EDA) of the Air Quality dataset obtained from the UCI Machine Learning Repository. 

In general, we first replace the default placeholder for missing values (−200) with actual null values (NaN) to more accurately reflect the true nature of the data. All EDA is then conducted exclusively on the training dataset to prevent data leakage and maintain the integrity of model evaluation.


---

## EDA Findings 

1.	**Null Value**
The dataset uses -200 as the default placeholder for missing values. We first replaced all instances of -200 with actual null values and conducted exploratory data analysis (EDA) to identify patterns in the missing data. Ultimately, we chose to forward-fill the missing values using the previous valid observation, as this approach preserves daily and weekly trends.

Moreover, since NMHC(GT) contains high null values (83.90%), we decided to drop this pollutant from our analysis. And because the ground truth of NMHC contains a lot of null values, we cannot validate the sensor related to it, then we also drop PT08.S2NMHC from our analysis.

2.	**Distribution**
Based on the distribution plot, we can notice that most of the data follow Gaussian distribution except for CO(GT), C6H6(GT), NOx(GT). The criteria is that if skewness is more than 1, we will determine the data is not following Gaussian distribution. This suggests that for these skewed features, we may need to apply some transformation to make it follow Gaussian distribution before modeling.
![image](images/training_data_distribution_with_null.png)

3.	**Pair Plot**
Based on the pair plot, we can observe that most of the data are linearly related to each other, especially among the pollutant concentrations. This suggests that models well-suited for capturing linear dependencies, such as linear regression, may be effective for this dataset. Alternatively, it also indicates that certain pollutants could serve as useful predictors for estimating the concentrations of others.
![image](images/training_data_pair_plot_with_null.png)

4.	**Correlation heatmap between different pollutants**
Based on the heat map of correlation, we can observe that there are high correlations between the pollutant concentrations and its sensor readings. This suggests that we need to handle multicollinearity when we build a regression model.
![image](images/training_data_correlation_with_null.png)




