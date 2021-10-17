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


## Model Evaluation

We will evaluate our models on the following criteria:

|Metrics|Description|
|------|------------------------------|
|Accuracy|The number of correct predictions (presence/absence of WMV) over the total number of predictions.|
|Specificity|Specificity measures the model's ability to correctly identify the absence of WMV when there is an absence of WMV.|
|Recall|Recall measures the model's ability to correctly identify the presence of WMV when there is an presence of WMV.|
|Precision|Precision is the ratio of correctly predicted positive observations (presence of WMV) to the total predicted positive observations (correctly predicted presence of WMV and incorrectly predicted presence of WMV)|
"|F1 Score|F1 denotes the harmonic mean of the precision and recall. The F1 score is often considered to be a better measure than accuracy score, particularly for datasets with unbalanced classes.  It takes into account both false positives and false negatives. 
F1 can be obtained through the following formula: 2*(Recall * Precision) / (Recall + Precision)|"
|AUC CV|This calculates the cross-validated area under the ROC curve (AUC) esimates. For each fold, the empirical AUC is calculated, and the mean of the fold AUCs is the cross-validated AUC estimate. |
|AUC train score|Measures the AUC train score. The higher the score, the better the model is at distinguishing between the presence or absence of WMV.  A model whose predictions are 100% wrong has an AUC of 0.0; one whose predictions are 100% correct has an AUC of 1.0.|
|AUC test score|Measures the AUC test score. The higher the score, the better the model is at distinguishing between the presence or absence of WMV.  A model whose predictions are 100% wrong has an AUC of 0.0; one whose predictions are 100% correct has an AUC of 1.0.|

In particular, as we are keen in evaluating the model's ability to distinguish between the presence of WMV when there is WMV and the ability to distinguish between presence and absence of WMV, we will evaluate the best tuned models on the ROC AUC score, Recall and Precision scores (F1 score). 

|Model |accuracy|specificity|recall|precision|f1_score|AUC_CV|AUC_train|AUC_test|Best Parameters|
|---------|----|---|----|----|----|----|----|----|------------------------------|
|Ada Boost|0.934403|0.981047|0.114035|0.254902|0.157576|0.713697|0.893912|0.830113|{'ada__base_estimator__max_depth': 2, 'ada__learning_rate': 0.3, 'ada__n_estimators': 80, 'sm__k_neighbors': 50}|
|Gradient Boost|0.879188|0.901247|0.491228|0.220472|0.304348|0.837726|0.894464|0.828094|{'gb__learning_rate': 0.1, 'gb__max_depth': 3, 'gb__min_samples_leaf': 10, 'gb__min_samples_split': 15, 'gb__n_estimators': 150}|
|Logistic Regression|0.73714|0.735661|0.763158|0.141005|0.23803|0.833047|0.844653|0.817984|{'lr__C': 1.023292992280754, 'lr__max_iter': 1000, 'lr__solver': 'saga'}|
|Random Forest|0.90184|0.931172|0.385965|0.241758|0.297297|0.761421|0.937813|0.814715|{'rf__min_samples_leaf': 5, 'rf__min_samples_split': 3, 'rf__n_estimators': 100}|
|K-Nearest Neighbor|0.746579|0.74813|0.719298|0.139693|0.233951|0.729757|0.904566|0.784709|{'knn__leaf_size': 10, 'knn__n_neighbors': 50, 'knn__p': 1}|


## Best Model Commentary

Overall, we chose the Gradient Boost model.
Even though the ADA Boost model provided the best AUC score, it performs poorly in terms of recall and has the lowest F1 score.
On the other hand, the Gradient Boost model has the 2nd best AUC score @ 82.8% and have the best F1 score. This is represented by fairly balanced recall and precision scores.
In addition, we are trying to reduce false negatives (we want to avoid predicting 'no WNV' incorrectly), hence choosing the Gradient Boost over ADA Boost was an obvious decision since it has a much higher recall score (49% vs 11%)


## Conclusion

We have chosen to deploy the Gradient Boost model - a model that achieved a fairly balanced recall and precision score, attained one of the highest AUC test scores and had the best F1 score.


### Cost Benefit Analysis




## Recommendation 

Recommendations to eradicate WMV as stated below:

Adult Control: Spray during (month/location/weather)
Larval Control: To prevent emergence of adult mosquitoes, deploy larvicide at known catch basins during (month when still at larva stage)
Public Education: Utilize media channels to broadcast information on personal protection from mosquitoes and eliminating breeding sites in their personal property
Bird Surveillance: Enhance our model by feeding it more relevant data. As mosquitoes are known to become first infected by feeding on birds that carry WNV, dead birds can be collected by the authorities and tested for WNV. 


