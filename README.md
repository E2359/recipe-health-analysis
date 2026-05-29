# recipe-health-analysis

By: Ye Teng 

This project explores the Recipes and Ratings dataset from Food.com. I focus on what types of recipes tend to be healthier, especially recipes with more protein and fewer carbohydrates. I also build a prediction model to predict the protein content of recipes based on recipe information like calories, carbs, fat, cooking time, and number of steps.

## Introduction

The Recipes and Ratings dataset contains recipe information and user ratings from Food.com. The recipe dataset includes columns such as recipe name, cooking time, tags, nutrition information, number of steps, and descriptions. The interactions dataset includes user ratings and reviews.

The main question I am investigating is:

**What types of recipes tend to be healthier, meaning recipes with more protein and fewer carbohydrates?**

The columns most relevant to my question are `protein`, `carbohydrates`, `calories`, `minutes`, `n_steps`, `avg_rating`, and the other nutrition columns.

## Data Cleaning and Exploratory Data Analysis

I cleaned the data by replacing ratings of 0 with `NaN`, since a rating of 0 does not represent a real rating. Then I calculated the average rating for each recipe and merged it back into the recipe dataset.

I also split the `nutrition` column into separate columns: `calories`, `total_fat`, `sugar`, `sodium`, `protein`, `saturated_fat`, and `carbohydrates`. This was important because my project focuses on protein and carbohydrates.

I created a `health_score` column:

`health_score = protein - carbohydrates`

A higher health score means the recipe has more protein compared to carbs.

<!-- Add cleaned dataframe head here -->

<!-- Add univariate plot iframe here -->

<!-- Add bivariate plot iframe here -->

<!-- Add aggregate table here -->
<iframe
 src="assets/protein_carbs_plot.html"
 width="800"
 height="600"
 frameborder="0"
></iframe>
<iframe
 src="assets/fairness_plot.html"
 width="800"
 height="600"
 frameborder="0"
></iframe>

## Assessment of Missingness

I analyzed the missingness of `avg_rating`. This column is missing when a recipe does not have a valid rating after ratings of 0 were changed to missing values.

I do not think `avg_rating` is necessarily NMAR. The missingness probably depends on whether users rated or interacted with the recipe, not just on the rating value itself. Extra data like page views, number of saves, or how often the recipe was shown to users could help explain why some recipes are missing ratings.

For the missingness permutation tests, I tested whether the missingness of `avg_rating` depends on other recipe columns such as calories and protein.

<!-- Add missingness plot iframe here -->

## Hypothesis Testing

For my hypothesis test, I tested whether healthier recipes tend to have higher average ratings. I defined healthier recipes as recipes with a health score above the median.

**Null Hypothesis:** Healthier recipes and less healthy recipes have the same average rating. Any difference is due to random chance.

**Alternative Hypothesis:** Healthier recipes have higher average ratings than less healthy recipes.

**Test Statistic:** Difference in mean average rating between healthier recipes and less healthy recipes.

The observed difference in mean average rating was about -0.022. This means healthier recipes actually had a slightly lower average rating than less healthy recipes in this dataset. The p-value was 1.0, which is much larger than 0.05. Because of this, I fail to reject the null hypothesis. I do not have evidence that healthier recipes have higher average ratings.

## Framing a Prediction Problem

My prediction problem is predicting the `protein` value of a recipe. This is a regression problem because protein is numerical.

I chose protein because my project focuses on healthier recipes, and protein is one way to measure nutrition. At the time of prediction, I would know recipe information like calories, carbohydrates, fat, sugar, sodium, cooking time, and number of steps. I would not use user feedback columns like `avg_rating`.

I evaluate my model using RMSE because this is a regression problem. RMSE tells me how far off my predicted protein values are from the true protein values on average.

## Baseline Model

My baseline model is a linear regression model that predicts `protein`.

The baseline model uses 4 quantitative features:

- `minutes`
- `n_steps`
- `calories`
- `carbohydrates`

It uses 0 ordinal features and 0 nominal features. Since all features are numerical, I did not need one-hot encoding. I used median imputation in the pipeline to handle missing values.

The baseline model had an RMSE of about 31.79 and an R² value of about 0.50. This means the model’s predicted protein values were off by about 31.79 protein units on average. This is okay for a baseline, but there is still room to improve.

## Final Model

For my final model, I used a Random Forest Regressor. I added more nutrition features and engineered new features.

The final model used features such as:

- `minutes`
- `n_steps`
- `calories`
- `carbohydrates`
- `total_fat`
- `sugar`
- `sodium`
- `saturated_fat`

I also engineered new features:

- `log_minutes`
- `steps_per_minute`
- `carbs_per_calorie`
- `fat_per_calorie`

These features make sense because protein is related to the overall nutrition profile of a recipe. Ratios like carbs per calorie and fat per calorie help describe what kind of nutrients make up the recipe.

The final Random Forest model had an RMSE of about 21.32 and an R² value of about 0.77. This improved over the baseline model, which had an RMSE of about 31.79 and an R² value of about 0.50.

## Fairness Analysis

For fairness analysis, I tested whether my final model performs worse for high-calorie recipes than low-calorie recipes.

Since this is a regression model, I used RMSE as the evaluation metric.

**Group X:** High-calorie recipes  
**Group Y:** Low-calorie recipes  

**Null Hypothesis:** My model is fair. The RMSE for high-calorie recipes and low-calorie recipes is roughly the same, and any difference is due to random chance.

**Alternative Hypothesis:** My model is unfair. The RMSE for high-calorie recipes is higher than the RMSE for low-calorie recipes.

**Test Statistic:** RMSE for high-calorie recipes minus RMSE for low-calorie recipes.

The p-value was 0.0, which is less than 0.05. Therefore, I reject the null hypothesis. This suggests that my final model does not perform equally across the two calorie groups. Based on this test, there is evidence that the model performs worse for high-calorie recipes than for low-calorie recipes.
