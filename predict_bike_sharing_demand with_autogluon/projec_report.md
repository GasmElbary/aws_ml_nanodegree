# Report: Predict Bike Sharing Demand with AutoGluon Solution
#### Gasm Elbary M.E

## Initial Training
### What did you realize when you tried to submit your predictions? What changes were needed to the output of the predictor to submit your results?

One can't submit a file with negative values as Kaggle will not accept them. Also, The negative values need to be clarified, as you can't have a negative count for the number of people. So, to fix that, one has to set all negative values, if present, to 0.

### What was the top ranked model that performed?
The top-ranked model for my case was a model I trained after editing the features available, scoring '0.47140'. The changes I made to the features are as follows:
- Split 'datetime' into 'year', 'month', 'day', and 'hour', then drop the 'datetime' column.
- Change the type of 'season' and 'weather' columns to Category.

However another model scored close score (0.47821) and it has better chances of scoring better with more adjustments. The changes I made to the features are as follows:
- Drop the 'windspeed' feature as it had a low correlation with the 'count.'
- Drop the 'atemp' column as it is highly correlated to the 'temp' column.
- Split 'datetime' into 'year', 'month', 'day', 'weekday', and 'hour', then drop the 'datetime' column.
- Change the type of 'year', 'month', 'day', 'weekday', 'hour', 'season', 'workinday', 'holiday', and 'weather' columns to Category.

## Exploratory data analysis and feature creation
### What did the exploratory analysis find and how did you add additional features?
During the EDA, I found that:
- Many features like 'season' and 'weather' were treated as integers instead of categories, so I had to change their type.
- It is harder for the models to get information from the 'datetime' column in its current format. That's why it was better to try splitting it into only hours and try, then split it into 'year', 'month', 'day', 'weekday', and 'hour'.
- 'temp' and 'atemp' are highly correlated, so we will drop one of them as they provide the same information. Having two highly correlated columns will act as a weighted feature.
- 'windspeed' has a low correlation with 'count', so we can drop it as it will not add value.

![model_test_score.png](img/corr_matrix.png)

### How much better did your model preform after adding additional features and why do you think that is?
The original model, without new features, scored 1.80676. Then, when I added some features, it performed much better and scored 0.47140. Finally, when I added more features, it performed slightly worse and scored 0.47821. Feature engineering is well-known for improving the quality of models for many reasons, including:
- Features in their current format might be harder to interpret and gain information from.
- Usually, the more features we have, the more information and options a model will have to make predictions.
- Sometimes we can derive more informative features from current columns.

## Hyper parameter tuning
### How much better did your model preform after trying different hyper parameters?
After hyperparameter tuning, my model scored 0.54402, which is much better than the original model, but not better than the model without hyperparameter tuning. The reason is that there are so many options for hyperparameters to tune and many values to accept, making it much more difficult to achieve improvement without many trials and a long time.


The hyperparameters I tuned are 'auto_stack', 'holdout_frac', 'hyperparameter_tune_kwargs', 'refit_full', and 'time_limit'.

The default for 'auto_stack' is 'False'; setting it to 'true' should give better accuracy but will require more training time. 'holdout_frac' specify the percentage of data held for validation; by default, it is set automatically, but I decided that 15% to be held for parameters tuning. 'hyperparameter_tune_kwargs' by default is 'None' and is used to specify the hyperparameter tuning strategy, but to not get into a lot of details I set it to 'auto'. 'refit_full' by default is 'False' and I set it to 'best'. This will make the model retrain the best model again on the full data after finishing. Since some of the hyperparameters I changed will make the training take a longer time I increased the 'time_limit' to 1200 seconds (20 minutes).

### If you were given more time with this dataset, where do you think you would spend more time?
I would spend more time manipulating and trying to engineer more features. Then, as I see that I reached the best score with the added features, I'll review the model that scored the highest and tune its parameters.

### Create a table with the models you ran, the hyperparameters modified, and the kaggle score.
|model|hpo1|hpo2|hpo3|score|
|--|--|--|--|--|
|initial|time_limit|eval_metric|presets|1.80676|
|add_features|time_limit|eval_metric|presets|0.47140|
|hpo|auto_stack|holdout_frac|presets|0.54402|
|add_featuresV2|time_limit|eval_metric|presets|0.47821|
|add_featuresV3|time_limit|eval_metric|presets|0.48025|
    
### Create a line plot showing the top model score for the three (or more) training runs during the project.
![model_train_score.png](img/model_train_score.png)

### Create a line plot showing the top kaggle score for the three (or more) prediction submissions during the project.
![model_test_score.png](img/model_test_score.png)

## Summary
In the beginning, I trained the Autogluon model using the nine available features. Autogluon trained 15 models, and the model 'WeightedEnsemble_L3' ranked the best, which scored '1.80676' on Kaggle. Then for better dataset formatting, I changed the type of the columns that are supposed to be categorical but treated as integers to represent what they actually are. And, split the datetime column into 'year', 'month', 'day', and 'hour' as the original column format needs to be more informative for the model. Autogluon trained 16 models this time, but the best model was the same 'WeightedEnsemble_L3'. This time, the score improved significantly, scoring '0.47140'. 

After that, I tried tuning 5 of the hyperparameters and trained over 20 minutes instead of 10 minutes, but unfortunately, the score didn't improve, and I scored '0.54402'. This time, Autogluon trained 27 models, and the best model was "WeightedEnsemble_L2" followed by "WeightedEnsemble_L3" by a very small difference. I tuned the hyperparameters 'auto_stack', 'holdout_frac', 'hyperparameter_tune_kwargs', 'refit_full', and 'time_limit'. These changes might have caused the models to overfit. There are plenty of hyperparameters to tune, and it is harder to tune more than one simultaneously; therefore, I spent more time adding features and manipulating the data. I added a 'weekday' feature using the datetime column, and after looking at the correlation matrix of the features, I found that 'windspeed' can be dropped as it doesn't correlate with the target. Also, I found that 'atemp' and 'temp' are highly correlated, so I can drop one of them. Applying these changes, the score didn't improve and scored '0.47821'. Autogluon trained 14 models this time, and the best model was 'WeightedEnsemble_L3'.

Finally, I decided to normalize the 'temp' and 'humidity' columns as it is a good practice to have the values of a column small and closer to each other. This will help avoid the differences in a model when large and small values are multiplied with the same weight. Autogluon trained 14 models and scored '0.48025' on Kaggle's data. The model was again 'WeightedEnsemble_L3'.

In the end, to improve the score more, I would spend more time on the EDA and engineering more features, then tune the hyperparameters of the specific best-performance algorithm to improve the score. So far, the best model has always been 'WeightedEnsemble_L3', but with more features, it might change.


## References:
https://auto.gluon.ai/stable/api/autogluon.predictor.html
