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

| name                                 | id     | minutes | contributor_id | submitted   | tags                                                                                                                                                                                                                                                                                               | nutrition                                     | n_steps | steps                                                                                                                                                                                                                                 | description                                                                                                                                                                                                                                         | ingredients                                                                                                                                                                                                                             | n_ingredients |
|--------------------------------------|--------|---------|----------------|-------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------|---------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------|
| 1 brownies in the world    best ever | 333281 | 40      | 985201         | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings']                                | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]       | 10      | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish with aluminum foil', 'melt the chocolate and butter in a double boiler', 'whisk in the sugar and eggs', 'add the vanilla and espresso', 'stir in flour and salt', 'pour into the pan', 'bake for 25 minutes', 'cool before cutting', 'serve'] | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....seriously! there's no doubt that these will be your fav brownies ever for you can add things to...                                         | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour']                                                          | 9              |
| 1 in canada chocolate chip cookies   | 453467 | 45      | 1848091        | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                                                             | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]   | 12      | ['pre-heat oven the 350 degrees f', 'in a mixing bowl , sift together the flours and baking powder', 'set aside', 'in another large bowl , cream together the margarine , brown sugar and white sugar until light and fluffy', 'beat in the eggs one at a time , then stir in the vanilla and water', 'gradually blend in the sifted ingredients', 'stir in the chocolate chips', 'drop dough by tablespoonfuls onto ungreased cookie sheets', 'bake for 10 to 12 minutes in the preheated oven', 'allow cookies to cool on baking sheet for 5 minutes', 'remove to a wire rack to cool completely'] | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don't have margarine or don't like it, then just ... | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                                                                             | 11             |

---

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
