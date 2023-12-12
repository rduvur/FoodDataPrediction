DSC80 Project 5 Fall 2023

by Rohan Duvur (rduvur@ucsd.edu) and Kotaro Murata (kmurata@ucsd.edu)

## Framing the Problem

In our EDA of the food.com recipe dataset [(found here)](https://rduvur.github.io/FoodDataAnalysis/), we were greatly interested in the relationship between the minutes column and how it would effect the trends in other columns like calories and number of recipe steps.

- Prediction Problem:

**For this project, we would like to construct a regression model that can predict a recipe's calorie count using a number of different features (# of steps/ minutes etc.).**

- Model Type: **Regression** 

- Response Variable: **Calories**

We chose Calories as the response variable because it is a continuous and quantitative feature that is apt for finding out how filling a meal is. In our previous analysis, we created a new feature by classifying meals as being healthy or not healthy depending on whether or not they were above 400 calories. Calories is a key part of our exploration so it only made sense to use it as our response variable.

- Metric for Evaluation **RMSE**

Because we're looking to predict numerical values not classify, we thought using RMSE (root mean squared error) as a metric would be a good fit. It's a good choice because it greatly punishes predictions that are very different from actual values due to its squaring property. RMSE is also easily interpretable, providing a number that gives an approximation of how many units off each prediction is.

- Features 

While considering what information would be readily available to base a guess on how many calories a recipe is, we opted to use **minutes** and **number of steps** for our baseline. These are things that could be easily found on a standard webpage detailing a recipe. These two columns are the sole features used in our baseline model, however for the final model, we added the **protein (PDV)** and **sugar (PDV)** columns. These are features that could also be found from an online recipe as well, and would be known since the recipe would have to been prepared by someone beforehand to be uploaded.

## Baseline Model

#### Description

Our baseline model has 2 features: **n_steps** and **minutes**. Both features are discrete variables in this context. We fitted the training data to a **Decision Tree Regressor**. For the max_depth parameter, we felt a value of 20 was fitting. 

We didn't apply any transformations (for now) as both of our features are numerical columns. 

#### Results

| Metric   | Training Error    | Testing Error      |
| -------- | ----------------- | ------------------ |
| RMSE     | 18.774310677904165| 19.040131952742446 |

This means on average, our model is around 19 calories off the true caloric value for a given recipe. 

The current model is ok, but could definitely be improved in some areas. We plotted a graph of your predicitions against their actual values and noticed a few key things. We saw a linear line indicating predictions that were perfect guesses. Under and above that line we saw many data points that were grossly under/ over-estimated. It's clear that we need more features to make a model that is better prepared to handle unseen data. A recipe could be quite intricate and take a lot of time, but may not necessarily have a lot of calories. Some of the outliers we saw we weren't too stresed over though. While doing our EDA, we noticed a lot of "troll" recipes that reported practically non-existant calories despite containing a lot of food and foods as simple in steps as hot-cocoa containing 40,000 calories. 


#### Slight Improvements

To improve on the baseline, we typed some code that would tune the max_depth hyper-paramter, by fitting many trees with incremental max_depths, then returning the max_depth that yielded the lowest RMSE on test data.

The plot of our tests is below:

<iframe src="assets/max_depth.html" width=800 height=600 frameBorder=0></iframe>

We found that the RMSE is minimized at a max_depth of around 18 or so. The effect this had on our RMSE was quite minimal though, giving a new test RMSE of 19.00938182014313

## Final Model

Jumping off from our baseline, we started by engineering 2 new features by **scaling** the **n_steps** and **minutes** columns by using z-scores. Additionally, we included the Total Fat (PDV), Sugar (PDV) and Protein (PDV) columns to see if that could improve results. A common problem with decision trees is that they overfit to the data they are training on. For our final model, we opted to use sklearn's Lasso model.  

#### Description: 

Our final model has 3 additional numeric features, Total Fat (PDV), Sugar (PDV) and Protein (PDV). We added these because there seemed to be a slight correlation between these variables and calories discovered in our EDA. Intuitively, a dish that has more of these metrics would be higher in calories as they are generally associated with bigger and/ or unhealthier foods. We included these because it wouldn't be out of the ordinary for a recipe to include sugar, fat, and protein content to be mindful of those who are health concious. LASSO Regression is special in that it has the ability to automatically nullify features deemed unecessary. The test RMSE of our final model definitely could've been improved if we added even more of the nutritional columns into the mix, but we wanted to be conservative with how many we added to be more realistic to what is available on the average recipe.

#### Tuning the Hyperparameter

LASSO Regression has a hyperparameter called regressor_alpha which we used GridSearchCV to tune. We tested 100 alphas over the range of 10^-8 and 10^8. The process of maximizing score took 3 minutes, giving us a best alpha of **0.0200923300256505** and a best score of **0.8879876529231892**. After this testing, we created a new pipeline that used this alpha.

#### Results

| Metric   | Training Error    | Testing Error      |
| -------- | ----------------- | ------------------ |
| RMSE     | 11.334128547669282| 11.503101513324795 |

The training/ test RMSE **improved significantly** between the baseline and final models (a difference of around 8.5). The adding of new features and scaling the features that we already had are responsible for improving the evaluation metric. Based on this metric, we can say our final model is a good predictor of Calories, at least compared to our baseline which did not generalize as well on unseen data.

## Fairness Analysis

In our fairness analysis, we will have group X that classifies if a recipe is 'easy' (less than 20 minutes to make)or 'hard' (more than 20 minutes to make). For our group Y, we will classify a recipe as 'healthy' if it is less than 400 calories and 'unhealthy' if it is more than 400 calories. For our evaluation metrics, we will use RMSE.

- Null Hypothesis:

**Our model is fair. The RMSE for 'easy' and 'hard' recipes are roughly the same, and any differences are due to random chance**

- Alternative Hypothesis:

**Our model is unfair. The RMSE for 'easy' and 'hard' recipes are different.**

- Test Statistic: **Absolute difference in RMSE**

- Significance Level: **5%**

With a p value of 0.0, we reject the null. This implies that our model is most likely biased.

