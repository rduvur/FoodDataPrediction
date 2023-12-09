DSC80 Project 5 Fall 2023

by Rohan Duvur (rduvur@ucsd.edu) and Kotaro Murata (kmurata@ucsd.edu)

## Framing the Problem

In our EDA of the food.com recipe dataset [(found here)](https://rduvur.github.io/FoodDataAnalysis/), we were greatly interested in the relationship between the minutes column and how it would effect the trends in other columns like calories and number of recipe steps.

**For this project, we would like to construct a regression model that can predict a recipes calories using a number of different features.**

Model Type: Regression
Response Variable: Calories

Because we're looking to predict numerical values not classify, we thought using **RMSE** (root mean squared error) as a metric would be a good fit. It's a good choice because it greatly punishes predictions that are very different from actual values due to its squaring property. RMSE is also easily interpretable, providing a number that gives an approximation of how many units off each prediction is.

While considering what information would be readily available to base a guess on how many calories a recipe is, we opted to use **minutes** and **number of steps**. These are things that could be easily found on a standard webpage detailing a recipe. These two columns are the sole features used in our baseline model, however for the final model, we added the **protein (PDV)** and **sugar (PDV)** columns. These are features that could also be found from an online recipe as well.

#### Baseline Model




