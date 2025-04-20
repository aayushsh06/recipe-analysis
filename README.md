# Recipes and Ratings Analysis  
**By:** Aayush Sharma  
**Contact:** *aayushsh@umich.edu*

---

## Introduction  
This project explores recipe and user interaction data from **Food.com**, a major recipe-sharing platform. The dataset includes thousands of recipes, user-submitted ratings, and reviews dating back to 2008.

**Investigative Question:**  
**How can we predict the number of calories for a recipe in the dataset?**

---

## Why This Matters  
Understanding how recipe characteristics influence calorie count is important for:

- People managing their dietary intake  
- Home cooks aiming to prepare healthier meals  
- Nutritionists and dietitians creating meal plans  
- Developers building tools for calorie estimation or recipe recommendation  

By modeling calorie content based on recipe features, we can gain insight into which types of recipes and preparation methods contribute most to total caloric value.

---

## Dataset Overview  

We use two datasets from Food.com.

### Dataset 1: `RAW_recipes.csv`  
**Description:** Contains 83,782 recipes with ingredients, steps, and nutrition information.

**Table 1: Sample from `RAW_recipes.csv`**

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                    | nutrition                                     |   n_steps | steps                                                                                                   | description                                                                                             | ingredients                                                                                                                                                                                                                             |   n_ingredients |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:--------------------------------------------------------------------------------------------------------|:----------------------------------------------|----------:|:--------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups... | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish ... | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.... | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour']                                                          |               9 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups... | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 | ['pre-heat oven the 350 degrees f', 'in a mixing bowl , sift together the flours and baking powder',... | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the b... | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                                                                             |              11 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'v... | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a ... | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this ... | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']                                                                   |               9 |
| millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12  | ['time-to-make', 'course', 'cuisine', 'preparation', 'occasion', 'north-american', 'desserts', 'amer... | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 | ['freheat the oven to 300 degrees', 'grease a 10-inch tube pan with butter , dust the bottom and sid... | why a millionaire pound cake?  because it's super rich!  this scrumptious cake is the pride of an el... | ['butter', 'sugar', 'eggs', 'all-purpose flour', 'whole milk', 'pure vanilla extract', 'almond extract']                                                                                                                                |               7 |
| 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06  | ['time-to-make', 'course', 'main-ingredient', 'preparation', 'main-dish', 'potatoes', 'vegetables', ... | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 | ['pan fry bacon , and set aside on a paper towel to absorb excess grease', 'mince yellow onion , red... | ready, set, cook! special edition contest entry: a mediterranean flavor inspired meatloaf dish. feat... | ['meatloaf mixture', 'unsmoked bacon', 'goat cheese', 'unsalted butter', 'eggs', 'baby spinach', 'yellow onion', 'red bell pepper', 'simply potatoes shredded hash browns', 'fresh garlic', 'kosher salt', 'white pepper', 'olive oil'] |              13 |

### Dataset 2: `RAW_interactions.csv`  
**Description:** Contains 731,927 user reviews and ratings.

**Table 2: Sample from `RAW_interactions.csv`**

| user_id   | recipe_id | date       | rating | review                                                                                                                                                                                                      |
|-----------|-----------|------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1293707   | 40893     | 2011-12-21 | 5      | So simple, so delicious! Great for chilly fall evening. Should have doubled it ;)<br/><br/>Second time around, forgot the remaining cumin. We usually love cumin, but didn't notice the missing 1/2 teaspoon. |
| 126440    | 85009     | 2010-02-27 | 5      | I made the Mexican topping and took it to bunko. Everyone loved it.                                                                                                                                        |
| 57222     | 85009     | 2011-10-01 | 5      | Made the cheddar bacon topping, adding a sprinkling of black pepper. Yum!                                                                                                                                   |
| 124416    | 120345    | 2011-08-06 | 0      | Just an observation, so I will not rate. I followed this procedure with strawberries instead of raspberries. Perhaps this is the reason it did not work well. Sorry to report that the strawberries I used were disappointing. |
| 2000192946| 120345    | 2015-05-10 | 2      | This recipe was OVERLY too sweet. I would start out with 1/3 or 1/4 cup of sugar and just add on from there. Just 2 cups was way too much and I had to go back to the grocery store to buy more raspberries. |

Note: While this dataset helps understand user preferences, the primary focus for this analysis is on the first dataset, where the nutritional content is available.

---

## Relevant Columns for Our Analysis  

### From `RAW_recipes.csv`:

| Column           | Description                                      |
|------------------|--------------------------------------------------|
| `calories`       | Number of calories per serving (target variable) |
| `total_fat`      | Total fat content (% Daily Value)                |
| `sugar`          | Sugar content (% Daily Value)                    |
| `sodium`         | Sodium content (% Daily Value)                   |
| `protein`        | Protein content (% Daily Value)                  |
| `saturated_fat`  | Saturated fat content (% Daily Value)            |
| `n_ingredients`  | Number of ingredients                            |
| `n_steps`        | Number of preparation steps                      |

### From `RAW_interactions.csv`:

| Column     | Description                                       |
|------------|---------------------------------------------------|
| `user_id`  | Identifier for the user submitting the rating     |
| `recipe_id`| Identifier linking the rating to a specific recipe|
| `rating`   | User’s rating (typically on a scale of 1 to 5)    |
| `date`     | Date the rating or review was submitted           |
| `review`   | Optional written review                           |

These features will be used to build a model capable of predicting the calorie content of a recipe based on its nutritional profile and complexity.

## Step 2: Data Cleaning and Exploratory Data Analysis

To prepare the dataset for analysis, I carried out a series of data cleaning steps aimed at ensuring the quality and reliability of the information. Below is a breakdown of each step taken.

---

### 1. Merging Datasets

I began by merging the two datasets. I performed a **left join** using the recipe ID to combine these datasets, ensuring that all recipe entries were preserved even if some did not have associated user feedback. After the merge, I removed the redundant `recipe_id` column that was duplicated in the merged result.

---

### 2. Cleaning the Rating Column

Some recipes had a rating value of `0`, which is not a valid rating in the context of the platform (ratings typically range from 1 to 5). These zeroes likely represented missing or incorrect data. To avoid skewing the results, I replaced all zero values in the `rating` column with `NaN` to mark them as missing.

---

### 3. Calculating Average Rating per Recipe

I grouped the data by `recipe ID` and computed the mean of the valid (non-missing) ratings. This provided a more accurate picture of how each recipe was rated by users on average.

---

### 4. Merging Average Ratings into the Main Dataset

I merged the calculated average rating back into the main dataset so that each row, which represents a user interaction or review, also included the average rating for the corresponding recipe. This enriched the dataset and allowed for more detailed comparisons and filtering.

---

### 5. Expanding and Cleaning Nutrition Data

The dataset included a `nutrition` column that stored nutritional data as a stringified list. I parsed this list and split it into individual columns, including:
- `calories`
- `total_fat`
- `sugar`
- `sodium`
- `protein`
- `saturated_fat`
- `carbohydrates`

Once these values were extracted and structured, I removed the original `nutrition` column to eliminate redundancy.

---

### Final Output

After these cleaning steps, the dataset was significantly more structured, with invalid data removed and additional features such as average ratings and detailed nutrition values included to support deeper analysis.

---

### Preview of the Cleaned DataFrame

| name                                 | id      | minutes | contributor_id | submitted   | tags                                                                                                                                     | n_steps | steps                                                                                                                             | description                                                                                                                                          | ingredients                                                                                                                           | n_ingredients | user_id  | date       | rating | review                                                                                                                                          | rating_average | calories | total_fat | sugar | sodium | protein | saturated_fat | carbohydrates |
|--------------------------------------|---------|---------|----------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------|---------|------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|---------------|----------|------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------|----------------|----------|------------|-------|--------|---------|----------------|----------------|
| 1 brownies in the world best ever    | 333281  | 40      | 985201         | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups']                                   | 10      | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish...']                      | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make...                             | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder'...]                           | 9             | 387000   | 2008-11-19 | 4.0    | These were pretty good, but took forever to bake. I would send it ended up being almost an hour...                                  | 4.0            | 138.4    | 10.0       | 50.0  | 3.0    | 3.0     | 19.0           | 6.0            |
| 1 in canada chocolate chip cookies   | 453467  | 45      | 1848091        | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups']                                   | 12      | ['pre-heat oven the 350 degrees f', 'in a mixing bowl , sift together the flours and baking powder...']                     | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best...                        | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour'...]                          | 11            | 425000   | 2012-01-26 | 5.0    | Originally I was gonna cut the recipe in half (just the 2 of us here), but then we had a park-wide cookie craving...              | 5.0            | 595.1    | 46.0       | 211.0 | 22.0   | 13.0    | 51.0           | 26.0           |
| 412 broccoli casserole               | 306168  | 40      | 50969          | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes']                                       | 6       | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix...']       | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one 412 broccoli casserole... | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground pepper'...]                   | 9             | 29800    | 2008-12-31 | 5.0    | This was one of the best broccoli casseroles that I have ever made. I made my own chicken soup and it turned out delicious...    | 5.0            | 194.8    | 20.0       | 6.0   | 32.0   | 22.0    | 36.0           | 3.0            |
| 412 broccoli casserole               | 306168  | 40      | 50969          | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes']                                       | 6       | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix...']       | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one 412 broccoli casserole... | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground pepper'...]                   | 9             | 1200000  | 2009-04-13 | 5.0    | I made this for my son's first birthday party this weekend. Our guests INHALED it! Everyone kept asking for the recipe...         | 5.0            | 194.8    | 20.0       | 6.0   | 32.0   | 22.0    | 36.0           | 3.0            |
| 412 broccoli casserole               | 306168  | 40      | 50969          | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes']                                       | 6       | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix...']       | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one 412 broccoli casserole... | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground pepper'...]                   | 9             | 769000   | 2013-08-02 | 5.0    | Loved this. Be sure to completely thaw the broccoli. I didn’t and it didn’t get done...                                            | 5.0            | 194.8    | 20.0       | 6.0   | 32.0   | 22.0    | 36.0           | 3.0            |

## Univariate Analysis

### Distribution of Calories per Recipe

 <iframe
 src="assets/calories_boxplot.html"
 width="800"
 height="600"
 frameborder="0"
 ></iframe>

The violin plot above visualizes the distribution of calorie values across all recipes, with outliers removed for clarity. The shape of the plot highlights the density of data points — showing that most recipes cluster around the 250–450 calorie range. The thickest part of the distribution, near the median, gives a quick sense of central tendency, while the tails show the spread of higher and lower calorie recipes.

This visualization is particularly useful for the calorie prediction task ahead. It helps identify:
- The skewness of the distribution, which could impact model selection or preprocessing.
- Natural calorie clusters that could hint at underlying recipe types.
- Any residual outliers or irregular patterns that may need special handling.

Understanding this distribution ensures the model is trained on realistic, well-ordered data.

### Distribution of Sugar per Recipe

<iframe
 src="assets/sugar_histogram.html"
 width="800"
 height="600"
 frameborder="0"
></iframe>

This histogram shows the distribution of sugar content per recipe, with outliers removed. Most recipes contain less than 20 grams of sugar, with a sharp peak around the very low end — possibly indicating savory dishes or low-sugar meals. There is a long right tail, which suggests a minority of recipes (e.g. desserts or drinks) with much higher sugar content.

Understanding this distribution is crucial for building a calorie prediction model. Since sugar is often a strong contributor to calorie count, observing its skew and overall spread helps in:
- Identifying whether transformations are needed (e.g., log-scaling).
- Spotting imbalances in recipe types (sweet vs. savory).
- Assessing the influence of sugar on calorie predictions.

This insight can guide feature engineering and help avoid bias in the model’s output.
