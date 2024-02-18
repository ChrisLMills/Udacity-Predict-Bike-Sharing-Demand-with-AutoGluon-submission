# Report: Predict Bike Sharing Demand with AutoGluon Solution
Chris Mills

## Initial Training
### What did you realize when you tried to submit your predictions? What changes were needed to the output of the predictor to submit your results?

The 'casual' and 'registered' columns needed to be removed from the training data. These are subsets of the 'count' target, and are therefore not contained within the test data. By including them in the training, they appear as features. This causes the prediction on the test data to fail.

### What was the top ranked model that performed?


fileName                     date                 description           status    publicScore  privateScore  
---------------------------  -------------------  --------------------  --------  -----------  ------------  
submission_new_hpo.csv       2024-02-17 22:51:25  new features + hpo    complete  0.49332      0.49332
submission_new_hpo.csv       2024-02-17 21:53:37  new features + hpo    complete  0.55209      0.55209
submission_new_features.csv  2024-02-17 20:46:29  new features          complete  0.55295      0.55295             
submission.csv               2024-02-17 19:56:42  first raw submission  complete  1.79111      1.79111

## Exploratory data analysis and feature creation
### What did the exploratory analysis find and how did you add additional features?

Datetime was removed and replaced by hour, day_of_the_week and month.
The correlation matrix showed that 'temp' and 'atemp' were highly correlated, so I removed 'atemp' from my last hyperparamter tuning model fit.
However, this resulted ina  slighly worse score: 0.49332 -> 0.49816

### How much better did your model perform after adding additional features and why do you think that is?

Remarkably better. 
'datetime' burries relevant features by no two values being the same. This means the models cannot extract patterns from this features. 
By splitting it into hours, days of the week and months, the models are ablke to make connections between counts and common associations between the values for these three features. 

## Hyper parameter tuning
### How much better did your model preform after trying different hyper parameters?

Slightly better.
More details provided in the summary below. 

### If you were given more time with this dataset, where do you think you would spend more time?

I would look at feature importance. 
I would normalize the float values.
I would run the models for a longer period of time. 
Explore more hyperparamter tuning spaces.
Finding outliers and truncating values that are too many std devs from the mean. 

### Create a table with the models you ran, the hyperparameters modified, and the kaggle score.


    model	       hpo1	          hpo2	         hpo3	        score
0	initial	       default vals	  default vals	 default vals	1.79111
1	add_features   default vals	  default vals	 default vals	0.55295
2	hpo	           XGB	          CAT	         RF	            0.49332


### Create a line plot showing the top model score for the three (or more) training runs during the project.

![model_train_score.png](img/model_train_score.png)

### Create a line plot showing the top kaggle score for the three (or more) prediction submissions during the project.

![model_test_score.png](img/model_test_score.png)

## Summary

I tried the auto_stack = True parameter in isolation to see what impact it had. It took a score of 
0.55295 to 0.55209, which is negligible, so I removed it from further tuning. 


The top performing model is WeightedEnsemble_L3. 
Not knowing what the ensemble is made up of, I chose XGBoost to have the broadest hyperparamter search space. This is based on its general good performance. 
Second to this is CatBoost_BAG_L2, so I added tuning for this.
Beside another enseble and catboost model, the next top performing model is RandomForestMSE_BAG_L2. As such I added one parameter to tune on this. 

After training on these parameters, I was able to get an additional 0.06 drop in error. 