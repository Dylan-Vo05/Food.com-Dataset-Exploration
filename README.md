# Exploration of the Healthiness of Recipes
A project at UCSD for the DSC80 course

Author: Dylan Vo

## Introduction

One thing that many people keep in mind when deciding on what dishes to consume is the healthiness of the recipe. Whether it is to lose weight, maintain health, or avoid harmful overindulgence, there are a number of reasons why one's health would be first priority when deciding what recipe to prepare for dinner. The World Health Organization reports that 73.6% of adults aged 20 or older are overweight, and 40.3% are obese. With this in mind, I focused on the healthiness of recipes, how it changed over time, and what factors indicate to a recipe's healthiness.

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
  - The column serves as a general measure of a recipe's healthiness
  - It is a sum of the difference between unhealthy macronutrients and calories, in PDV (unhealthy macros being sugar, sodium, sat. fats, and carbohydrates), minus the difference between caloric PDV and protein PDV

9. Dropped unnecessary columns
  - Removed the columns that were either unneeded or redundant for this specific analysis, including  `'recipe_id'`, `'nutrition'`, `'review'`, `'description'`, and `'steps'`

When all steps are completed, the dataset contains 234,429 rows and 21 columns, a small portion of which is shown below.

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

This shows a relatively normal distribution, with more weight on the right. It indicates that, on average, recipes are generally slightly more unhealthy on food.com, with a few largely unhealthy outliers.

### Bivariate Analysis

### Interesting Aggregates

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

## Baseline Model

## Final Model

## Fairness Analysis
