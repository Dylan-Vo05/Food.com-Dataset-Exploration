# Exploration of the Healthiness of Recipes
A project at UCSD for the DSC80 course

Author: Dylan Vo

## Introduction

One thing that many people keep in mind when deciding on what dishes to consume is the healthiness of the recipe. Whether it is to lose weight, maintain health, or avoid harmful overindulgence, there are a number of reasons why one's health would be first priority when deciding what recipe to prepare for dinner. The World Health Organization reports that 73.6% of adults aged 20 or older are overweight, and 40.3% are obese. With this in mind, I focused on the healthiness of recipes, and how it impacted other statistics.

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

### Bivariate Analysis

### Interesting Aggregates

## Assessment of Missingness

## Hypothesis Testing

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis
