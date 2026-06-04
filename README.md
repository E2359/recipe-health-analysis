# Health on the Menu: A Recipe Nutrition Analysis

By: Ye Teng 

This project explores the Recipes and Ratings dataset from Food.com. I focus on what types of recipes tend to be healthier, especially recipes with more protein and fewer carbohydrates. I also build a prediction model to predict the protein content of recipes based on recipe information like calories, carbs, fat, cooking time, and number of steps.

## Introduction

This project uses the Recipes and Ratings dataset from Food.com. The dataset includes recipe information, such as recipe name, cooking time, nutrition values, tags, number of steps, and user ratings. I chose this dataset because I am interested in food and nutrition, and I wanted to explore what makes a recipe healthier.

The main question my project focuses on is:

**What types of recipes tend to be healthier, meaning recipes with more protein and fewer carbohydrates?**

This question matters because people often care about whether a recipe is healthy, but “healthy” can mean different things. In this project, I focus on protein and carbohydrates because higher-protein and lower-carb recipes are commonly seen as more nutritionally balanced. By looking at recipe features like calories, cooking time, number of steps, and nutrition values, I can better understand what kinds of recipes tend to have more protein and fewer carbs.

The original recipes dataset has 83,782 rows, and the interactions dataset has 731,927 rows. After cleaning and merging the datasets, my merged dataset has 234,429 rows.

The columns most relevant to my analysis are:

- name: the name of the recipe
- minutes: how long the recipe takes to make
- n_steps: the number of steps in the recipe
- calories: the number of calories in the recipe
- protein: the protein percentage of daily value
- carbohydrates: the carbohydrate percentage of daily value
- total_fat: the total fat percentage of daily value
- sugar: the sugar percentage of daily value
- sodium: the sodium percentage of daily value
- saturated_fat: the saturated fat percentage of daily value
- avg_rating: the average rating for each recipe
- health_score: a column I created using protein minus carbohydrates, where a higher value means the recipe has more protein compared to carbs

## Data Cleaning and Exploratory Data Analysis

To clean the data, I first replaced all ratings of 0 with NaN because a rating of 0 does not seem to represent an actual user rating. Then I calculated the average rating for each recipe and merged that information back into the recipe dataset. I also merged the recipes dataset with the interactions dataset so that recipe information and user rating information could be analyzed together.

The nutrition column originally stored values as one long string that looked like a list. To make the nutrition information easier to analyze, I split this column into separate columns: calories, total_fat, sugar, sodium, protein, saturated_fat, and carbohydrates. This was important because my project focuses on recipe health, especially protein and carbohydrates. I also created a new column called health_score, which is protein minus carbohydrates. A higher health_score means the recipe has more protein compared to carbs.

Here is the head of my cleaned DataFrame:
| name                                 |   minutes |   n_steps |   calories |   protein |   carbohydrates |   avg_rating |   health_score |
|:-------------------------------------|----------:|----------:|-----------:|----------:|----------------:|-------------:|---------------:|
| 1 brownies in the world    best ever |        40 |        10 |      138.4 |         3 |               6 |            4 |             -3 |
| 1 in canada chocolate chip cookies   |        45 |        12 |      595.1 |        13 |              26 |            5 |            -13 |
| 412 broccoli casserole               |        40 |         6 |      194.8 |        22 |               3 |            5 |             19 |
| millionaire pound cake               |       120 |         7 |      878.3 |        20 |              39 |            5 |            -19 |
| 2000 meatloaf                        |        90 |        17 |      267   |        29 |               2 |            5 |             27 |

### Univariate Analysis

This plot shows the distribution of protein values across recipes. Most recipes have relatively low protein values, while a smaller number of recipes have much higher protein levels. This shows that high-protein recipes exist, but they are not the majority of the dataset.

<iframe
 src="assets/protein_distribution.html"
 width="800"
 height="600"
 frameborder="0"
></iframe>

### Bivariate Analysis

This scatter plot compares carbohydrates and protein for recipes. It helps show which recipes are higher in protein and lower in carbs, which connects directly to my main question about healthier recipes. The color represents calories, so I can also see how calorie level relates to the nutrition values.

<iframe
 src="assets/protein_carbs_plot.html"
 width="800"
 height="600"
 frameborder="0"
></iframe>

### Interesting Aggregates

I grouped recipes by health_score level to compare average nutrition and rating values. This helps show whether recipes with higher health scores also differ in calories, ratings, cooking time, or number of steps.

| health_group          |   protein |   carbohydrates |   calories |   avg_rating |   minutes |   n_steps |
|:----------------------|----------:|----------------:|-----------:|-------------:|----------:|----------:|
| Lowest Health Score   |     12.29 |           24.89 |     500.94 |         4.64 |    125.18 |     10.13 |
| Low-Mid Health Score  |      8.88 |            7.2  |     211.66 |         4.63 |    150.2  |      8.94 |
| High-Mid Health Score |     29.99 |           11.43 |     384.98 |         4.62 |     75.7  |     10.14 |
| Highest Health Score  |     82.69 |           11.44 |     625.87 |         4.61 |    105.59 |     11.25 |



## Assessment of Missingness

For the missingness section, I focused on the avg_rating column. This column is missing when a recipe does not have a valid rating after ratings of 0 were replaced with NaN.

I do not think avg_rating is necessarily NMAR. The missingness probably depends more on whether users interacted with or rated the recipe, not only on the missing rating value itself. For example, recipes that are less popular, take longer to make, or have fewer people viewing them may be less likely to receive ratings. Extra information like page views, number of saves, number of clicks, or how often the recipe was shown to users could help explain the missingness. If those extra variables explained why avg_rating is missing, then the missingness would be MAR instead of NMAR.

To test whether the missingness of avg_rating depends on other columns, I ran permutation tests. I compared the distribution of another column between recipes where avg_rating was missing and recipes where avg_rating was not missing. The test statistic was the absolute difference in group means.

For one test, I checked whether the missingness of avg_rating depends on calories. For another test, I checked whether it depends on protein. These tests help show whether recipes with missing ratings are systematically different from recipes with non-missing ratings.

<iframe
 src="assets/missingness_plot.html"
 width="800"
 height="600"
 frameborder="0"
></iframe>

Based on the permutation tests, the missingness of avg_rating seemed to depend on calories because the p-value was small. This means recipes with missing average ratings had a noticeably different calorie distribution compared to recipes with non-missing ratings. However, the missingness of avg_rating did not seem to depend on protein because the p-value was larger. This means I do not have strong evidence that recipes with missing ratings have different protein values compared to recipes with non-missing ratings.

## Hypothesis Testing

For my hypothesis test, I wanted to see whether healthier recipes tend to get higher average ratings. I defined healthier recipes as recipes with a health_score above the median, where health_score is calculated as protein minus carbohydrates.

Null Hypothesis: Healthier recipes and less healthy recipes have about the same average rating. Any difference between the two groups is due to random chance.

Alternative Hypothesis: Healthier recipes have higher average ratings than less healthy recipes.

Test Statistic: Difference in mean average rating between healthier recipes and less healthy recipes.

The test statistic is calculated as:

mean rating of healthier recipes - mean rating of less healthy recipes

I used a permutation test because I am comparing the average rating between two groups. I used a significance level of 0.05.

The observed difference in mean average rating was about -0.022. This means that healthier recipes actually had a slightly lower average rating than less healthy recipes in my dataset. The p-value was 1.0, which is much larger than 0.05. Because of this, I failed to reject the null hypothesis.

Overall, I do not have enough evidence to say that healthier recipes get higher average ratings than less healthy recipes. This result suggests that recipes with more protein and fewer carbohydrates are not necessarily rated higher by users.

<iframe
 src="assets/health_rating_plot.html"
 width="800"
 height="600"
 frameborder="0"
></iframe>

## Framing a Prediction Problem

My prediction problem is predicting the protein value of a recipe. This is a regression problem because protein is a numerical value, not a category.

I chose protein as my response variable because my project focuses on healthier recipes, and protein is one of the main nutrition values I use to measure healthiness. Predicting protein can help show whether other recipe features, like calories, carbohydrates, fat, sugar, sodium, cooking time, and number of steps, are useful for estimating how much protein a recipe has.

At the time of prediction, I would know information about the recipe itself, such as minutes, n_steps, calories, total_fat, sugar, sodium, saturated_fat, and carbohydrates. I would not use user feedback columns like avg_rating because ratings happen after users interact with the recipe, so they would not always be available when predicting the protein content of a recipe.

I used RMSE to evaluate my model because this is a regression problem. RMSE tells me how far off my predicted protein values are from the actual protein values on average. I chose RMSE because larger mistakes are penalized more, which is useful when predicting nutrition values.

## Baseline Model

For my baseline model, I used a Linear Regression model to predict protein. This is a regression model because protein is a numerical value.

The model used four features: minutes, n_steps, calories, and carbohydrates. All four of these features are quantitative. I used 0 ordinal features and 0 nominal features. Since all of the features were numerical, I did not need to use one-hot encoding. I used median imputation in the pipeline to handle any missing values.

The baseline model had an RMSE of about 31.79 and an R² value of about 0.50. This means that, on average, the model’s predicted protein values were off by about 31.79 protein units. The R² value shows that the model explained about 50% of the variation in protein.

Overall, I think this baseline model is okay as a starting point, but it is not very strong yet. It only uses a few basic recipe features, so there is still room to improve by adding more nutrition-related features and using a more flexible model.

## Final Model

For my final model, I used a Random Forest Regressor to predict protein. I chose this model because it can capture more complicated and nonlinear relationships between recipe features, while the baseline linear regression model only fits a straight-line relationship.

Compared to the baseline model, I added more nutrition-related features, including total_fat, sugar, sodium, and saturated_fat. I also engineered new features: log_minutes, steps_per_minute, carbs_per_calorie, and fat_per_calorie. These features make sense for this prediction task because protein is part of the overall nutrition profile of a recipe. For example, carbs_per_calorie and fat_per_calorie help describe what kind of nutrients make up the recipe, while steps_per_minute gives a rough idea of recipe complexity.

I used GridSearchCV to tune the Random Forest hyperparameters. The hyperparameters I searched over were n_estimators, max_depth, and min_samples_leaf. I tuned these because n_estimators controls the number of trees in the forest, max_depth controls how complex each tree can become, and min_samples_leaf controls how many samples must be in each leaf. These settings help balance model flexibility and overfitting. The best hyperparameters selected by GridSearchCV were max_depth = None, min_samples_leaf = 1, and n_estimators = 100.

The final Random Forest model had an RMSE of about 21.32 and an R² value of about 0.77. This is an improvement over the baseline model, which had an RMSE of about 31.79 and an R² value of about 0.50. Since the final model has a lower RMSE, its protein predictions are closer to the true protein values on average. The higher R² also means the final model explains more of the variation in protein.

## Fairness Analysis

For my fairness analysis, I tested whether my final model performs worse for high-calorie recipes than low-calorie recipes. Since my model is a regression model, I used RMSE as the evaluation metric. A higher RMSE means the model makes larger prediction errors.

Group X is high-calorie recipes, which are recipes with calories above the median. Group Y is low-calorie recipes, which are recipes with calories at or below the median.

Null Hypothesis: My model is fair. The RMSE for high-calorie recipes and low-calorie recipes is roughly the same, and any difference is due to random chance.

Alternative Hypothesis: My model is unfair. The RMSE for high-calorie recipes is higher than the RMSE for low-calorie recipes.

Test Statistic: RMSE for high-calorie recipes minus RMSE for low-calorie recipes.

I used a permutation test with a significance level of 0.05. In the test, I shuffled the calorie group labels while keeping the final model’s predictions the same. The p-value was less than 0.001, which is less than 0.05, so I rejected the null hypothesis.

Based on this test, there is evidence that my final model performs worse for high-calorie recipes than for low-calorie recipes. In other words, the model does not seem to be equally accurate across both calorie groups.

<iframe
 src="assets/fairness_plot.html"
 width="800"
 height="600"
 frameborder="0"
></iframe>
