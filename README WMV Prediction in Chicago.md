## Context

In the United States, West Nile Virus (WNV) is the leading cause of mosquito-borne disease. It it spread to humans from the bite of an infected mosquito, and usually starts in summer. Since there are no vaccines to prevent or medicines to treat the virus, spraying by pesticides is one of the methods adopted to reduce the risk. WNV results in significant medical impact - 1 in 5 infected persons develop a fever, and 1 in 150 infected persons develop serious illness. [Source: CDC](https://www.cdc.gov/westnile/index.html)


## Problem Statement

We are a team of data scientists at the Disease And Treatment Agency. In the last few years, there have been several episodes of the West Nile Virus (WNV) outbreak in Chicago. The aim of our project is to leverage on weather, location and time data to predict West Nile Virus occurrences in Chicago. By understanding these features and whether they can predict the virus, we will then evaluate whether spraying areas with pesticides will be an effective tool vis-a-vis medical and economic costs.


## Dataset

The dataset, along with description, can be found here: [https://www.kaggle.com/c/predict-west-nile-virus/](https://www.kaggle.com/c/predict-west-nile-virus/).


## Data Cleaning and Feature Engineering

The dataset was cleaned and exploratory data analysis was conducted. 

From the spray dataset, we observed that the current rate of success in eradicating WNV in sprayed areas is 7 / 17 = 41.18%. (Please note that this is a relatively high percentage, considering we are not looking at a decrease in mosquito numbers but eradicating WNV as a whole from that area). However, due to the lack of daily and hourly train data, we were not able to evaluate the immediate effectiveness of spray on WMV.  In addition, there were only 10 days in which data of spray was obtained. Due to these factors, the spray data was not employed in modelling. 

From the weather and train dataset, we feature engineered a few variables (e.g. lagged weather features, relative humidity, categorised trap addresses into 5 risk categories based on WNV occurences) and tested to see if it allowed for a more predictive model. 


## Model Evaluation Metrics

We will evaluate our models on the following criteria:

|Metrics|Description|
|------|------------------------------|
|Accuracy|The number of correct predictions (presence/absence of WMV) over the total number of predictions.|
|Specificity|Specificity measures the model's ability to correctly identify the absence of WMV when there is an absence of WMV.|
|Recall|Recall measures the model's ability to correctly identify the presence of WMV when there is an presence of WMV.|
|Precision|Precision is the ratio of correctly predicted positive observations (presence of WMV) to the total predicted positive observations (correctly predicted presence of WMV and incorrectly predicted presence of WMV)|
|F1 Score|F1 denotes the harmonic mean of the precision and recall. The F1 score is often considered to be a better measure than accuracy score, particularly for datasets with unbalanced classes.  It takes into account both false positives and false negatives. F1 can be obtained through the following formula: 2*(Recall * Precision) / (Recall + Precision)|
|AUC CV|This calculates the cross-validated area under the ROC curve (AUC) esimates. For each fold, the empirical AUC is calculated, and the mean of the fold AUCs is the cross-validated AUC estimate. |
|AUC train score|Measures the AUC train score. The higher the score, the better the model is at distinguishing between the presence or absence of WMV.  A model whose predictions are 100% wrong has an AUC of 0.0; one whose predictions are 100% correct has an AUC of 1.0.|
|AUC test score|Measures the AUC test score. The higher the score, the better the model is at distinguishing between the presence or absence of WMV.  A model whose predictions are 100% wrong has an AUC of 0.0; one whose predictions are 100% correct has an AUC of 1.0.|

The ROC-AUC is the key metric here because it helps us to evaluate how well our models are performing when predicting the probability of a binary outcome (in this case, whether WNV is present or not).
We want to minimise false negatives (type II error). Therefore, evaluating our model on their recall score is also an important factor. Incorrectly predicting ‘no occurrence of WNV’ at an address could potentially result in serious, or even deadly, consequences when we miss out on controlling for the virus there.

To a smaller extent, we are also interested in minimising false positives from a cost perspective. This is in view of our cost-benefit analysis in which we aim to minimise spraying costs by not targetting areas where it will be less effective. Precision score is something that we will look at as well, albeit with less importance than the ROC AUC and recall scores.


## Model Evaluation and Best Model Commentary


|Model |accuracy|specificity|recall|precision|f1_score|AUC_CV|AUC_train|AUC_test|Best Parameters|
|---------|----|---|----|----|----|----|----|----|------------------------------|
|Ada Boost|0.934403|0.981047|0.114035|0.254902|0.157576|0.713697|0.893912|0.830113|{'ada__base_estimator__max_depth': 2, 'ada__learning_rate': 0.3, 'ada__n_estimators': 80, 'sm__k_neighbors': 50}|
|Gradient Boost|0.879188|0.901247|0.491228|0.220472|0.304348|0.837726|0.894464|0.828094|{'gb__learning_rate': 0.1, 'gb__max_depth': 3, 'gb__min_samples_leaf': 10, 'gb__min_samples_split': 15, 'gb__n_estimators': 150}|
|Logistic Regression|0.73714|0.735661|0.763158|0.141005|0.23803|0.833047|0.844653|0.817984|{'lr__C': 1.023292992280754, 'lr__max_iter': 1000, 'lr__solver': 'saga'}|
|XG Boost |0.877773|0.900249|0.482456|0.215686|0.298103|0.833137|0.896163|0.8291|{'xgb__colsample_bytree': 0.75, 'xgb__early_stopping_rounds': 10, 'xgb__eval_metric': 'auc', 'xgb__gamma': 0.05, 'xgb__learning_rate': 0.17, 'xgb__max_depth': 3, 'xgb__min_child_weight': 1, 'xgb__n_estimators': 100, 'xgb__objective': 'binary:logistic', 'xgb__subsample': 0.75, 'xgb__use_label_encoder': False}|
|Random Forest|0.90184|0.931172|0.385965|0.241758|0.297297|0.761421|0.937813|0.814715|{'rf__min_samples_leaf': 5, 'rf__min_samples_split': 3, 'rf__n_estimators': 100}|
|K-Nearest Neighbor|0.746579|0.74813|0.719298|0.139693|0.233951|0.729757|0.904566|0.784709|{'knn__leaf_size': 10, 'knn__n_neighbors': 50, 'knn__p': 1}|

Overall, we chose the Gradient Boost model.
Even though the ADA Boost model provided the best AUC score, it performs poorly in terms of recall and has the lowest F1 score.
On the other hand, the XGBoost and Gradient Boost model have the 2nd and 3rd best AUC score @ ~83%.
We are trying to reduce false negatives (we want to avoid predicting 'no WNV' incorrectly), hence choosing the XGBoost or Gradient Boost over ADA Boost was an obvious decision since it has a much higher recall score (49% vs 11%)
We decided on the Gradient Boost model because it has a slightly better F1 score, and is faster to tune considering it has less hyperparameters for tuning.


## Cost Benefit Analysis

### Current Spray Efforts

Referring back to our EDA on the spray data, we saw that the current rate of success in eradicating WNV in sprayed areas is 7 / 17 = 41.18%. This is quite a significant rate, considering we are not looking at a decrease in mosquito numbers but eradicating WNV as a whole from that area.

### Cost of WMV

We used the cost analysis of WNV in Sacremento County, California. [(Source)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3322011) as a reference when conducting our cost benefit analysis. We will use 2012's data for the economic analysis given that it had the most number of cases, and it will show the most benefit from the sprays. We considered both the medical impact (medical/hospitalisation expense) and cost of productivity loss due to WMV:


#### Medical Impact

* WNV asymptomatic vs symptomatic 4:1 [Illinois Department of Public Health (Source)](https://idph.illinois.gov/envhealth/wnvglance07.htm)
* WNV can result to
    * Mild - WNF (West Nile Fever)
    * Severe - WNND (West Nile Neuroinvasive disease)
    
    
#####  Estimated cost based on WNND cases (187 patients)

|         |Total cost based on WNND cases (187 patients)|
|-----------|----------------|
|Cost due to Productivity Lost|$7,379,020|
|Cost due to Inpatient and Outpatient expenses|$1,755,600|
|Total Cost|$9,134,620|


#### Estimated cost based on WNF cases (103 patients)

|           |Total cost based on WNF cases (103 patients)|
|-----------|----------------|
|Cost due to Productivity Lost|31106|
|Cost due to Inpatient and Outpatient expenses|84175|
|Total Cost|115281|


### Summary: Total Cost and Potential Benefits

The total economic cost of the 2012 WNV epidemic was approximately **\\$9,249,901**, while the total spraying cost on the whole city was approximately **\\$1,439,000**. Cost ratio of was about 7:1, which suggests that for the benefit of spraying to outweigh the the economic cost, the spray would only need to prevent 36 WNND cases.

Through our Gradient boosting model, we are confident to predict **49%** of the WNV (based on the recall score). If we were to deploy our model during the 2012 WNV epidemic to eradicate carrier mosquitos or to notify potential infected citizens for early treatment, we would be able to save ~**$3 million** on initial hospitalization costs.


## Conclusion

We have chosen to deploy the Gradient Boost model as it has achieved an ROC-AUC score of 83% and a recall score of 49%, as it is important to ensure a relatively high recall score that does not compromise the ROC AUC and/or precision score.

Our model determines that WNV is most likely to occur during the month of August. During this period, variables with the highest feature importance such as average temperature, wind speeds, and relative humidity are more optimal. Spray efforts should hence, concentrated during this period.

Location is also a relatively strong predictor, whereby certain traps are identified to have a higher risk of containing WNV.

In conclusion, spraying high risk areas with pesticides during peak months with weather dependencies is an effective tool that reduces medical and economic costs.


## Recommendation 


During our external research, we have uncovered serveral recommendations for our agency and the concerned public to consider:
* **Adult Control**
    * Spray efforts should be concentrated during the months of August, at traps where there is a high risk of ENV present, and also considering weather data when the relative humidity and wind speeds are significant. We should then continue to measure mosquito population size and infection rates before and after spraying to monitor the efficacy of the treatments.
* **Larval Control**
    * To reduce mosquito breeding by eliminating stagnant water, city agency should treat all catch basins with larvicide during the month of June-July (before the adult mosquitoes hatch), to limit mosquito breeding and reduce the adult mosquito density.
* **Public Education**
    * To effectively target areas where there is a density of human residents, we will need to educate the public on active measures they can take to eradicate musquitos within their residencies, and having self protection. For such a scale, we should involve the health agency to spread awareness, drive campaigns and educate the public on actions that inidividuals can take. Personal protection measures can also be subsidised by the government in giving out insect repellent and performing checks around their homes to clear mosquito breeding grounds
* **Bird Surveillance**
    * During our reasearch, we found that dead birds found in the city were tested for WNV and found to be positive. This is significant, as the mosquitoes likely are infected with WNV from feeding from these birds, allowing us to be a step ahead. We should collate this data as an additional feature to further improve our model.


