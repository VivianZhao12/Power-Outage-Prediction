
## Identifying problem 

### **Prediction Problem:**
The prediction problem in this case is regression. 
We aim to **predict the severity of a major power outage, specifically the 'OUTAGE.DURATION'.(Regression)**

### **Response Variable:**

The response variable, or the variable we are predicting, can be one of the following:

**'OUTAGE.DURATION'**: Duration of the power outage in hours.

**Choice of Response Variable:**

Outage Duration is an important metric of **measuring outage severity**. At the start of an outage, people will be most concerned about how long it will last. The duration of an outage can have significant implications for the affected individuals, businesses, and infrastructure. By predicting the outage duration, we can gain insights into the potential impact, planning, and resource allocation required during such events.

Understanding the outage duration can help utility companies, emergency response teams, and policymakers make informed decisions and develop strategies to minimize the impact of power outages. It can also aid in the assessment of system vulnerabilities and the development of more robust infrastructure.

**By focusing on the outage duration as the response variable, we can provide valuable information for proactive planning, resource management, and response efforts to mitigate the consequences of major power outages.**

### **Information Known at the Time of Prediction:**
At the time of prediction, we would know the features that are available up until that point. These features would include the historical data of power outages and the corresponding information such as **'YEAR', 'HURRICANE.NAMES', 'U.S._STATE', 'CLIMATE.REGION', 'ANOMALY.LEVEL', 'CLIMATE.CATEGORY', 'CAUSE.CATEGORY', 'CAUSE.CATEGORY.DETAIL', and 'OUTAGE.START'.**

### **Metric for Model Evaluation:**
An appropriate metric for evaluating the regression model could be the R-Squared.

**R-Squared** 
The metric we are using to evaluate our model is the **R-squared (coefficient of determination)**. We chose R-squared as the evaluation metric because we are dealing with a regression problem where we are predicting the duration of outages.

R-squared measures the **proportion of the variance in the target variable (outage duration) that is explained by the independent variables (features) in our model**. It provides an indication of how well the model fits the data and captures the relationship between the predictors and the target variable.

In the context of outage duration prediction, R-squared is a suitable metric because it assesses the overall predictive power of the model. It quantifies the percentage of the variance in outage duration that can be explained by the selected features. A higher R-squared value indicates a better fit of the model to the data, implying that the independent variables are capturing a larger portion of the variation in the target variable.

Accuracy and F1-score are commonly used metrics for classification tasks, where the goal is to predict categorical outcomes. In our case, we are interested in predicting a continuous variable (outage duration), making regression evaluation metrics such as R-squared more appropriate.

Therefore, we chose R-squared as the metric to evaluate our model's performance because it directly measures the goodness of fit of the regression model, providing insights into how well it explains the variability in outage duration based on the selected features.

### Data Cleaning
> This dataset contains many null data and unused columns. In order to make the analysis process easier, we should first clean the data. Here is a snippet of the original dataframe (1540 rows, 57 columns).

---

|    | Major power outage events in the continental U.S.                                                           | Unnamed: 1   | Unnamed: 2   | Unnamed: 3   | Unnamed: 4   |
|---:|:------------------------------------------------------------------------------------------------------------|:-------------|:-------------|:-------------|:-------------|
|  0 | Time period: January 2000 - July 2016                                                                       | nan          | nan          | nan          | nan          |
|  1 | Regions affected: Outages reported in this data file affected a single U.S. state at the time of occurrence | nan          | nan          | nan          | nan          |
|  2 | nan                                                                                                         | nan          | nan          | nan          | nan          |
|  3 | nan                                                                                                         | nan          | nan          | nan          | nan          |
|  4 | variables                                                                                                   | OBS          | YEAR         | MONTH        | U.S._STATE   |
|  5 | Units                                                                                                       | nan          | nan          | nan          | nan          |

<br>

---

**1. Correct the index and columns (set column names, remove invalid rows, remove original index from excel file)**

Since the dataset is imported from an excel file, there are some formatting issues that need to be fixed. These operations will return a dataframe with the correct index and column names. Specifically, we will remove the first 4 lines of NaNs, remove the NaN row at index=5, remove two useless columns, set the column names to the values in the first row, remove the first row (index=4), and finally reset the index. 

**2. Convert START and RESTORATION times and dates to timestamp.Create new columns "OUTAGE.START" and "OUTAGE.RESTORATION".**

The columns OUTAGE.START.DATE, OUTAGE.START.TIME, OUTAGE.RESTORATION.DATE, and OUTAGE.RESTORATION.TIME are string type variables and contain similar information types. To make these information easier to understand and work with, we should combine and convert them to timestamp objects.

**3. Drop rows that have more than 10 missing values**

Browsing through the dataset, we see some rows with a lot of missing information. These rows assumingly do not contain as much useful information compared to others. I assume that these null values exist because such information was never recorded and does not depend on any other columns or the missing value itself. Thus, I will remove the rows that have more than 10 missing values to make the dataset more clear.

**4. Filter out unneeded columns, keep columns that are relevant the answering the question only**

Since our main focus in this analysis is duration time and its causes, we should delete the columns that will not give us any useful information regarding answering out question. (columns to keep: ["YEAR","HURRICANE.NAMES", 'U.S._STATE','CLIMATE.REGION', 'ANOMALY.LEVEL', 'CLIMATE.CATEGORY', 'CAUSE.CATEGORY', 'CAUSE.CATEGORY.DETAIL', 'OUTAGE.DURATION', 'CUSTOMERS.AFFECTED', 'OUTAGE.START', 'OUTAGE.RESTORATION', "TOTAL.PRICE"])

**5. Convert datatypes of columns**

When checking the datatypes of columns, we see that many numbers that should be numerical are saved as string objects. In order to have data that are computable and can bring us more information, we should convert them to float.

**6. impute Hurricane Names to "Not Applicable" for ones not cuased by hurricanes**

the Hurrican names column is missing at random. The missingness of hurricane names depends on CAUSE.CATEGORY.DETAIL. This is very straighforward. The power outages that are not caused by hurricanes will not have hurricane names. However, there are instances where it is caused by a hurricane but the name was not recorded. In order to distinguish these missingness, we should set the hurricane names that are not caused by hurricanse to not applicable.

**7. five occurrences of missing region** 

There are five occurences of missing regions, and with closer inspection, we see that they all have Hawaii as there state. This data is missing at random since it depends on the STATE column. Through a quick google search, Hawaii belongs to the "Pacific" Region. So all we have to do to resolve this missingness issue is to impute it with the correct region.

**8. Since we are trying to answer a question related to duration time (which is continuous), it is helpful to bin them into categories to better visualize them.**

So we will create a new column that stores the durations as one of four categories: ['Less than a week', 'One to two weeks', 'Two to three weeks', 'More than three weeks']

---
<br>

> ### After the cleaning operations, we get a clear dataset: 


|   index |   YEAR | HURRICANE.NAMES   | U.S._STATE   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED | OUTAGE.START        | OUTAGE.RESTORATION   |   TOTAL.PRICE | OUTAGE.DURATION.WEEKS   |
|--------:|-------:|:------------------|:-------------|:-------------------|----------------:|:-------------------|:-------------------|:------------------------|------------------:|---------------------:|:--------------------|:---------------------|--------------:|:------------------------|
|       0 |   2011 | Not Applicable    | Minnesota    | East North Central |            -0.3 | normal             | severe weather     | nan                     |              3060 |                70000 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |          9.28 | Less than a week        |
|       1 |   2014 | Not Applicable    | Minnesota    | East North Central |            -0.1 | normal             | intentional attack | vandalism               |                 1 |                  nan | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |          9.28 | Less than a week        |
|       2 |   2010 | Not Applicable    | Minnesota    | East North Central |            -1.5 | cold               | severe weather     | heavy wind              |              3000 |                70000 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |          8.15 | Less than a week        |
|       3 |   2012 | Not Applicable    | Minnesota    | East North Central |            -0.1 | normal             | severe weather     | thunderstorm            |              2550 |                68200 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |          9.19 | Less than a week        |
|       4 |   2015 | Not Applicable    | Minnesota    | East North Central |             1.2 | warm               | severe weather     | nan                     |              1740 |               250000 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |         10.43 | Less than a week  


## Baseline Model
### **Model Description:**
The baseline model used in this scenario is a **simple linear regression model** implemented using scikit-learn. The purpose of the model is to predict the duration of a power outage based on the selected features. **The model contains one quantitative variable and one nominal variable.**

### **Features in the Model:**
The model includes two features:

**'ANOMALY.LEVEL':** This feature is quantitative and represents the anomaly level associated with the power outage. It is treated as a numerical feature and is scaled using the MinMaxScaler within the pipeline.

**'CAUSE.CATEGORY'**: This feature is nominal and represents the category of the cause of the power outage. Since it is a categorical feature, it is encoded using the OneHotEncoder within the pipeline.

### **Encodings:**
To handle the 'CAUSE.CATEGORY' feature, which is nominal, we apply one-hot encoding using the OneHotEncoder from scikit-learn. This encoding transforms the categorical feature into multiple binary features, each representing a unique category. These binary features are then used as input to the model.
We scaled the 'ANOMALY.LEVEL' feature, which is quantitative. This estimator scales and translates each feature individually such that it is in the given range on the training set, (between zero and one).

### **Performance of the Model:**

The performance of the model is evaluated using the R-squared score on the test set, which is approximately 0.132. The R-squared score represents the proportion of the variance in the target variable (outage duration) that can be explained by the selected features and the linear regression model.

In this case, the **R-squared score of 0.132 indicates that only around 13.2% of the variability in the power outage duration can be accounted for by the features included in the model.** This suggests that the current model has **limited ability to accurately capture and predict the complexities of power outage durations based on the selected features alone.**

A higher R-squared score close to 1 would indicate that a larger proportion of the variance in the target variable is explained by the model, indicating a better fit. However, with an R-squared score of 0.069, it is evident that the current model's predictive power is very limited.

Therefore, based on the R-squared score, **the current model is not performing well in terms of accurately predicting power outage duration.** It is important to explore alternative features, consider more advanced modeling techniques, or incorporate additional relevant data to improve the model's performance and achieve a higher R-squared score, indicating a better fit to the data.


## Final Model
 
 In the final model, the following features were used:

**'CLIMATE.REGION'**: This feature represents the climate region where the power outage occurred. It can capture climate-related factors such as extreme temperatures, storms, or natural disasters that can contribute to power outages.

**'ANOMALY.LEVEL'**: This feature represents the anomaly level during the power outage. It was transformed using binarization with a threshold of 15. Binarizing this feature can help capture the presence or absence of significant anomalies during the outage, which may be indicative of the severity or complexity of the situation.

**'CLIMATE.CATEGORY'**: This feature represents the climate category associated with the power outage. It can provide additional information about the prevailing weather conditions during the outage.

**'CAUSE.CATEGORY' and 'CAUSE.CATEGORY.DETAIL'**: These features represent the broad and detailed categories of the cause of the power outage. They can capture the specific reasons or events that led to the outage, such as equipment failure, severe weather, or human error.

**'TOTAL.PRICE'**: This feature represents the total price associated with the power outage. It was scaled using the MinMaxScaler to bring it within a similar numerical range as the other features.

### **Modeling Algorithm:**
The **modeling algorithm** chosen for the final model is **RandomForestRegressor**, which is an ensemble method based on decision trees. Random forests can handle complex relationships between features and target variables and are robust against overfitting. The hyperparameters that performed the best were **'n_estimators': 15 and 'max_depth': 1.**

The hyperparameters were tuned using GridSearchCV, which exhaustively searches the specified parameter grid and selects the best combination of hyperparameters based on the R-squared score. The final model with the best hyperparameters achieved an R-squared score of **0.369** on the test set, **indicating that it explains approximately 36.9% of the variance in the target variable.**

Compared to the baseline model, the final model shows a significant improvement in performance. The baseline model achieved an R-squared score of **0.132**, while the final model achieved an R-squared score of 0.369. This improvement indicates that the additional features and the RandomForestRegressor model, along with the tuned hyperparameters, **have better captured the underlying patterns and relationships in the data, resulting in a more accurate prediction of the power outage duration.**

## Fairness Analysis:
**Choice of Group X and Group Y (STATES):**

For the fairness analysis, Group X represents the states ['Connecticut', 'Georgia', 'Missouri','Washington', 'New York', 'Maryland',
       'Pennsylvania', 'Florida', 'Nevada','Vermont', 'Iowa', 'New Mexico', 'Alabama',
        'West Virginia', 'Montana', 'Arizona', 'Oregon', 'Oklahoma', 'Wisconsin',
       'South Dakota', 'North Dakota','Tennessee', 'Arkansas',
       'Maine', 'Massachusetts'] 

Group Y represents the states ['California', 'Texas', 'Michigan', 
       'Minnesota', 'New Hampshire', 'Colorado', 'Kentucky',
       'District of Columbia', 
       'South Carolina', 'Illinois', 'Indiana', 'Ohio', 'Utah',
       'Delaware', 'Louisiana', 'Hawaii', 'Nebraska', 'Mississippi','North Carolina', 'Kansas', 'Wyoming', 'Idaho', 'Virginia', 'New Jersey'] 


**Evaluation Metric:** We used the R-squared metric to evaluate the performance of the model for both Group X and Group Y.

**Null Hypothesis:** Our model is fair. The R-squared values for Group X and Group Y are roughly the same, and any differences observed are due to random chance.

**Alternative Hypothesis:** Our model is unfair. The R-squared value for Group X is lower than the R-squared value for Group Y.

**Test Statistic:** The test statistic is the observed absolute difference in R-squared values between Group X and Group Y.

**Significance Level:** We used a significance level of **0.05**.

**P-value and Conclusion:** The p-value obtained from the permutation test was **0.31**. Since the p-value is greater than the significance level of 0.05, **we fail to reject the null hypothesis**. Therefore, there is not enough evidence to suggest that our model performs worse for Group X compared to Group Y in terms of R-squared values.



