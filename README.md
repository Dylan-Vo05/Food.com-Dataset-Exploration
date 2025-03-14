# Exploration of the Healthiness of Recipes
A project at UCSD for the DSC80 course

Author: Dylan Vo

## Introduction

One thing that many people keep in mind when deciding on what dishes to consume is the healthiness of the recipe. Whether it is to lose weight, maintain health, or avoid harmful overindulgence, there are a number of reasons why one's health would be first priority when deciding what recipe to prepare for dinner. The World Health Organization reports that 73.6% of adults aged 20 or older are overweight, and 40.3% are obese. With this in mind, I focused on the healthiness of recipes, how it changed over time, and what factors indicate to a recipe's heartiness.

The dataset I am exploring this focus on is from [food.com](https://food.com), consisting of two CSVs. They contain data on recipes posted on the website and their reviews, with information from 2008 to 2018.

The first of the two CSVs is called `'recipes'`, containing 83,782 rows (unique recipes) and 10 columns. Each row is a unique recipe post, and the columns stored the information listed below:

| Column             | Description                                                                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `'name'`           | Recipe name                                                                                                                                                                                       |
| `'id'`             | Recipe ID                                                                                                                                                                                         |
| `'minutes'`        | Minutes to prepare recipe                                                                                                                                                                         |
| `'contributor_id'` | User ID who submitted this recipe                                                                                                                                                                 |
| `'submitted'`      | Date recipe was submitted                                                                                                                                                                         |
| `'tags'`           | Food.com tags for recipe                                                                                                                                                                          |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe                                                                                                                                                                         |
| `'steps'`          | Text for recipe steps, in order                                                                                                                                                                   |
| `'description'`    | User-provided description                                                                                                                                                                         |
| `'ingredients'`    | Text for recipe ingredients, in order                                                                                                                                                             |
| `'n_ingredients'`  | Number of ingredients in recipe                                                                                                                                                                   |

The second CSVs is named `'interactions'`, containing 731,927 rows and 5 columns. Each row indicates a unique review, and the column information is listed below:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

## Data Cleaning and Exploratory Data Analysis

To ease analysis and facilitate examination, the following Data Cleaning and Processing steps were performed:

1. Left merge the `'recipes'` and 'interactions' datasets on `'id'` and `'recipe_id'`
  - This matches all reviews to their corresponding recipe 

2. Replaced all 0 values in the `'ratings'` column with np.Nan values
  - Because recipes are rated on a scale from 1-5 on food.com, 0 values are invalid; Most likely a result of missing data

3. Added column `'avg_rating'`, containing the average rating of a recipe from its reviews
  - Provides a more comprehensive and encompassing rating of a recipe

4. Converted the `'submitted'` and `'date'` columns to Pandas Timestamp columns
  - Originally stored as strings, converting allows us to compare times much easier. This enables us to analyze changes over time and how time affects other variables

5. Converted `'user_id'`, `'id'`, and `'contributor_id'` columns to strings
  - These columns were originally stored as ints, but it would be better to store these as strings, because they are nominal variables and cannot be compared or computed using arithmetic

6. Split the `'nutrition'` column into `'calories (PDV)'`, `'total fat (PDV)'`, `'sugar (PDV)'`, `'sodium (PDV)'`, `'protein (PDV)'`, `'saturated fat (PDV)'` and, `'carbohydrates (PDV)'`
  - While it seems like it is stored as a list, the column's information is actually a string. The corresponding columns store how much of each macronutrient the recipe contains in Percentage Daily Value (PDV)
  - While this is done, `'calories'` is also converted from integer amount to PDV

7. Converted `'tags'` and `'ingredients'` into lists of strings
  - These columns are similarly stored as the `'nutrition'` column, as a string formatted as a list. Converting them to actual lists makes the information easier to process and work with.

8. Added the `'PDV_deviance'` column
  - The column serves as a summary measure of a recipe's healthiness, weighting its excess macronutrients against beneficial deficients
  - It is a sum of the difference between unhealthy macronutrients and calories, in PDV (unhealthy macros being sugar, sodium, sat. fats, and carbohydrates), minus the difference between caloric PDV and protein PDV
  - The selection of healthy and unhealthy macronutrients was guided by the USDA's [Dietary Guidelines for Americans](https://www.dietaryguidelines.gov/sites/default/files/2020-12/Dietary_Guidelines_for_Americans_2020-2025.pdf), which encouraged consumption of protein from a variety of sources, as well as the limitation and moderation of added sugars, sodium, saturated fats, and refined carbohydrates

9. Dropped unnecessary columns
  - Removed the columns that were either unneeded or redundant for this specific analysis, including  `'recipe_id'`, `'nutrition'`, `'review'`, `'description'`, and `'steps'`

When all steps are completed, the dataset contains 234,429 rows and 21 columns, a small portion of which is shown below. Scroll right to view more columns.

|    | name                                 |     id |   minutes |   contributor_id | submitted           | tags                                                                                                                                                                                                                        |   n_steps | ingredients                                                                                                                                                                    |   n_ingredients |   user_id | date                |   rating |   avg_rating |   calories (PDV) |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) |   PDV_deviance |
|---:|:-------------------------------------|-------:|----------:|-----------------:|:--------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|----------:|:--------------------|---------:|-------------:|-----------------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|---------------:|
|  0 | 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings'] |        10 | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |               9 |    386585 | 2008-11-19 00:00:00 |        4 |            4 |          6.15111 |                10 |            50 |              3 |               3 |                    19 |                     6 |        56.5467 |
|  1 | 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                               |        12 | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                    |              11 |    424680 | 2012-01-26 00:00:00 |        5 |            5 |         26.4489  |                46 |           211 |             22 |              13 |                    51 |                    26 |       217.653  |
|  2 | 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |     29782 | 2008-12-31 00:00:00 |        5 |            5 |          8.65778 |                20 |             6 |             32 |              22 |                    36 |                     3 |        29.0267 |
|  3 | 412 broccoli casserole               | 286009 |        40 |           461724 | 2008-05-30 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |   1196280 | 2009-04-13 00:00:00 |        5 |            5 |          8.65778 |                20 |             6 |             32 |              22 |                    36 |                     3 |        29.0267 |
|  4 | 412 broccoli casserole               | 475785 |        40 |          2202916 | 2008-05-30 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |    768828 | 2013-08-02 00:00:00 |        5 |            5 |          8.65778 |                20 |             6 |             32 |              22 |                    36 |                     3 |        29.0267 |

### Univariate Analysis

For the single-variate analysis, I chose to examine the distribution of PDV_deviance, my measure of a recipe's healthiness. The graph is shown below.

<iframe
  src="assets/univariate1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

As you can see, there are a few outliers that skew the graph. Once these are removed, we have this graph, shown below.

<iframe
  src="assets/univariate2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This shows a relatively normal distribution, slightly more skewed to the right. It indicates that, on average, recipes are generally slightly more unhealthy on food.com, with a few largely unhealthy outliers. It also shows that most of the recipes fall between -100 and 200 PDV_deviance.

### Bivariate Analysis

For bivariate analysis, I chose to examine a 'bad' nutrient, sugar, and a 'good' nutrient, protein, to see how they correlated with PDV_deviance.

<iframe
  src="assets/multivariate1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

For this graph, while there is much grouping around the origin, there is also a clustered group that points upwards. This indicates positive correlation between sugar content and higher PDV_deviance, as sugar in food increases, the unhealthier it is likely to be.

<iframe
  src="assets/multivariate2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

As shown here, protein has an opposite trend to sugar. Apart from the clustering around the origin, the graph shows negative correlation between protein and PDV. As protein content increases, PDV is more likely to be lower, and the food healthier.

### Interesting Aggregates

An interesting aggregate I chose to highlight was how the different macronutrients roughly affected PDV, and how it related to the healthiness of a recipe. A recipe was considered high in a particular nutrient if its PDV exceeded caloric PDV, and healthy if it was below average PDV. `'high_fat'` was excluded for readability.

<iframe
  src="assets/aggregate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
For example, the difference between the PDV of foods high in all nutrients versus the ones high in all but protein is about 80 PDV if the food is unhealthy. But for a food with those characteristics to be healthy, its average PDV is much, much lower. The graph helps illustrate the effect the individual nutrients have on PDV, and how healthy recipes and unhealthy recipes compare. It also provides some idea on the relationship between all of the nutrients, and how they ultimately come together into a food's `'PDV_deviance'`

## Assessment of Missingness

There are 3 columns with nontrivial missingness from the original dataset, being `'id'`, `'contributor_id'`, and `'rating'`.

### NMAR Analysis

I believe that the `'rating'` column is NMAR, because of situations where users may accidentally submit ratings without inputting information or do so to remove notifications. Food.com will often prompt users to rate recipe pages they have recently visited, and if they wish to remove these prompts quickly, some may just submit ratings with no inputted data. Users may also do this on accident, leading to ratings being entered without the most critical data point. A way to cut down on this missingness may be to expunge reviews with entirely missing or useless data.

## MAR Analysis

The column I decided to examine missingness dependency on was `'id'`, determining if it was dependent on either `'minutes'` required to prepare the recipe or the `'submitted'` date of the recipe's publishing.

> Minutes

  **Null Hypothesis:**
  - The missingness of 'id' does not depend on the 'minutes' required to prepare the recipe, as listed on food.com 

  **Alternate Hypothesis:**
  - The missingness of 'id' does depend on the 'minutes' required to prepare the recipe, as listed on food.com 

  **Test Statistic (Significance Level = 0.05):**
  - The absolute difference in means of the 'minutes' between the groups where 'id' is missing and 'id' is present

I performed a permutation test by shuffling the values of `'id'` 1,000 times to obtain 1,000 test statistics, simulated under the null hypothesis.

<iframe
  src="assets/missingness1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The observed test statistic, 29.2894, is shown by the vertical line on the graph. The p value we found is 0.019, which falls under the 0.05 threshold. Therefore we reject the null hypothesis and find that the missingness of `'id'` depends on the `'minutes'` of the recipe

> Submitted

  **Null Hypothesis:**
  - The missingness of 'id' does not depend on the date 'submitted', as listed on food.com 

  **Alternate Hypothesis:**
  - The missingness of 'id' does depend on the date 'submitted', as listed on food.com 

  **Test Statistic (Significance Level = 0.05):**
  - The absolute difference in means of the 'submitted' date between the groups where 'id' is missing and 'id' is present

I performed a permutation test by shuffling the values of `'id'` 1,000 times to obtain 1,000 test statistics, simulated under the null hypothesis.

<iframe
  src="assets/missingness2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The observed test statistic, 127,127.5489, is shown by the vertical line on the graph. The p value we found is 0.601, which exceeds the 0.05 threshold. Therefore we fail to reject the null hypothesis and find that the missingness of `'id'` does not depend on the date the recipe was `'submitted'`

## Hypothesis Testing

As mentioned previously in  the introduction, I would like to examine how the healthiness, roughly aproximated by `'PDV_deviance'`, changed over time. To facilitate this question, I ran a permutation test based on the `'submitted'` date of the recipe. I chose do use a permutation test because the dataset has no information on the population, and question simply poses if the two groups originate from the same distribution.

**Null Hypothesis:** 
  - Recipes published before July, 2013 (midpoint of the date range) are as healthy as recipes published later, as determined by PDV_deviance 

**Alternate Hypothesis:** 
  - Recipes published before July, 2013 are healthier compared to recipes published later, as determined by PDV_deviance 

**Test Statistic (0.05 Significance Level):**
  - The difference in group means of new recipes' PDV and old recipes' PDV

I chose this alternate hypothesis, because I believe that food trends towards unhealthy as time continues, and recipes published would reflect this trend. Because the hypothesis is directional, the test statistic is also directional, using the direct difference instead of absolute. A higher test statistic indicates recent recipes are more unhealthy than older recipes, and vice versa. As listed above, the data was split into two groups depending on if it was published in the earlier or later half of the dataset's date range.

The observed test statistic is 23.7487.

To perform the permutation test, the `'submitted'` column was shuffled 1,000 times, and 1,000 simulated mean differences were produced under the null hypothesis. The result was a p value of 0.0; results visualized below

<iframe
  src="assets/hypothesis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Since the p value that we found is lower than the significance level of 0.05, we reject the null hypothesis. Recipes have not stayed the same level or improved in healthiness as time has progressed. A possible explanation is as I said above, that food becomes more unhealthy overtime, likely through increases in processed ingredients and additives. This could reflect in the kinds of recipes posted to food.com.

## Framing a Prediction Problem

For the prediction problem, the goal is to predict the `'PDV_deviance'` of a recipe, which falls under the scope of a regression problem. This is due to PDV_deviance being a continous quantitative variable. It was chosen as it is this exploration's primary assessment of recipe healthiness, and predicting its value can be substantiated to predicting how healthy a recipe is.

One limitation of the prediction is that, because `'PDV_deviance'` is an engineered column, the information used in its calculation cannot be used in the predictive model, as the model would simply recreate the formula. As such `'calories (PDV)'`, `'sodium (PDV'`, `'sugar (PDV)'`, `'saturated fats (PDV)'`, `'carbohydrates (PDV)'`, and `'protein (PDV)'` are not viable features for the model.

The metrics I will use to evaluate both models are Mean Absolute Error (MAE) and R^2 score. MSE gives a good aproximation of the average error across the predicted values, while also being robust to outliers. This is an important consideration because, as seen in the first univariate graph, the dataset has a few outliers that could greatly skew MSE error calculations. R^2 is used as a way to measure how well the model captures the variance in `'PDV_deviance'`, an important factor in discussing the overall population of recipes.

## Baseline Model

The baseline model uses a RandomForestRegressor and incorporates the features `'submitted'` and `'avg_rating'`. The dataset was split into training and test data, with a 75/25 split respectively.

`'avg_rating'` is a continous quantative variable, which the model is able to process natively. However, because a small portion of the values of this column are Nan, some preprocessing has to be done to remove those columns, as the RandomForestRegressor does not accept Nan values. There is slight correlation between the average rating and PDV_deviance of a recipe, which is why it was chosen.

`'submitted'` is also a continous quantative variable, stored in pd.Timestamp objects. Because of this, preprocessing has to be done, using `'FunctionTransformer'` to convert the column into floats, a datatype RandomForestRegressor can natively use. Additionally, I subtracted the earliest submitted recipe date from all the timestamp values, in order to reduce the size of the large numbers conversion would result in. It creates more precise prediction, as the values now start from zero. This variable was chosen due to the demonstrated relationship shown in the hypothesis section above.

This model resulted in an MAE of 62.9146 and an R^2 value of 0.05955. This means that the average error on the model was quite large, and it was unable to capture the variance of the dataset much better than simply predicting the mean

## Final Model

The final model also used a `'RandomForestRegressor'` and additionally included the features `'ingredients'`, `'tags'`, and `'minutes'`.

`'avg_rating'` and `'submitted'` were processed the same as above.

`'minutes'` is a discrete quantative variable that requires no preprocessing. It was chosen due to a slight correlation with `'PDV_deviance'`. Recipes with higher cooking times are typically baked dishes or centerpiece foods, typically high in sugars and carbohydrates, while healthy meals are typically quicker to prepare. So there is reasoning that longer cooking times indicate lower healthiness.

`'ingredients'` and `'tags'` are both very similar, and thus, processed in the same way. They a list of items for each recipe, representing the ingredients used in the dish and the tags given to the recipe on food.com. To process this, I used `'MultiLabelBinarizer'` to create a binarized feature column for each unique item that appears in both columns. This requires a few extra steps to work properly. A list of all unique items that appear in each column in the entire dataset has to be prepared and given to the binarizer. This has to be done outside, because the training-test split means that there are different amounts of unique tags and ingredients that appear, causing a difference in feature amount. Additionally, because the list of unique items is so large, the binarizer has to use a sparse transformer to efficiently store the feature matrix and avoid overflow errors.

The `'ingredients'` included in a recipe is a critical factor in determining how healthy a recipe is. More fruits and vegetables will indicate a healthier recipe, and sugars, fats, and oils could indicate unhealthy recipes. While the list doesn't take into account the portions of these ingredients, it is still a feature that could greatly contribute in predicting `'PDV_deviance'`.

The `'tags'` attached to a recipe can also signal important information about a recipe's healthiness. Certain tags indicate what cultures foods come from, and because certain culture's cuisines are healthier than others, it can help predict healthiness. There are also tags for if a food is for holidays or events, often meaning the food is unhealthier. It is reasonable to believe there is a link between what tags are attached to a recipe, and its `'PDV_deviance'`.

`'GridSearchCV'` was used to tune the `'n_estimators'` and `'max_depth'` hyperparameters of the `'RandomForestRegressor'`. Due to the high variance inherent in decision trees, optimizing these variables is key to avoiding overfitting. However, because of how large the feature set is for this model, I have to avoid hyperparameters that are too large, for both overflow avoidance and runtime efficiency. The best combination of hyperparameters that the Grid Search outputted was

The MAE of the final model was ... and the R^2 was ...

## Fairness Analysis

For the fairness analysis, the data was split into lower steps and higher steps, according to `'n_steps'`. The median number of steps is 9, and recipes were considered lower steps if they were less than 9, higher if they were equal or greater. Median was chosen to evenly split. A permutation test was then performed to determine if the model was fair, using the difference in MSE as the test statistic

**Null Hypothesis:** The model is fair. The accuracy for long and short recipes is roughly similar, pulled from the same distribution.

**Alternate Hypothesis:** The model is not fair. The accuracy for long and short recipes is dissimilar enough to not be a result of random chance.

**Test Statistic (Significance Level = 0.05):** The difference in MAE between longer and shorter recipes.
