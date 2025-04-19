# Recipes and Ratings Analysis  
By: Aayush Sharma  
Contact: *(aayushsh@umich.edu)*

## Introduction  
This project explores recipe and user interaction data from Food.com, a major recipe sharing platform. The dataset includes thousands of recipes along with user-submitted ratings and reviews dating back to 2008. Our investigative question is: **How can we predict the number of calories for a recipe in the dataset?**

## Why This Matters  
Understanding how recipe characteristics influence calorie count is important for:

- People managing their dietary intake  
- Home cooks aiming to prepare healthier meals  
- Nutritionists and dietitians creating meal plans  
- Developers building tools for calorie estimation or recipe recommendation  

By modeling calorie content based on recipe features, we can gain insight into which types of recipes and preparation methods contribute most to total caloric value.

## Dataset Overview  
We use two datasets from Food.com:

- `RAW_recipes.csv` — Contains **83,782** recipes with information on ingredients, preparation steps, and nutrition

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                                                                                               | nutrition                                     |   n_steps | steps                                                                                                                       | description                                                                                                                                                                                                 | ingredients                                                                                                                                                                                                                             |   n_ingredients |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------|----------:|:----------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings']                                                                        | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish with aluminum foil',... | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to... | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour']                                                          |               9 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                                                                                                      | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 | ['pre-heat oven the 350 degrees f', 'in a mixing bowl , sift together the flours and baking powder', 'set aside', 'in an... | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don't have margarine or don't like it, then just ... | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                                                                             |              11 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                                                                                               | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix toget... | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this... | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']                                                                   |               9 |
| millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12  | ['time-to-make', 'course', 'cuisine', 'preparation', 'occasion', 'north-american', 'desserts', 'american', 'southern-united-states', 'dinner-party', 'holiday-event', 'cakes', 'dietary', 'christmas', 'thanksgiving', 'low-sodium', 'low-in-something', 'taste-mood', 'sweet', '4-hours-or-less'] | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 | ['freheat the oven to 300 degrees', 'grease a 10-inch tube pan with butter , dust the bottom and sides with flour , and ... | why a millionaire pound cake?  because it's super rich!  this scrumptious cake is the pride of an elderly belle from jackson, mississippi.  the recipe comes from "the glory of southern cooking" by jam... | ['butter', 'sugar', 'eggs', 'all-purpose flour', 'whole milk', 'pure vanilla extract', 'almond extract']                                                                                                                                |               7 |
| 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06  | ['time-to-make', 'course', 'main-ingredient', 'preparation', 'main-dish', 'potatoes', 'vegetables', '4-hours-or-less', 'meatloaf', 'simply-potatoes2']                                                                                                                                             | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 | ['pan fry bacon , and set aside on a paper towel to absorb excess grease', 'mince yellow onion , red bell pepper , and a... | ready, set, cook! special edition contest entry: a mediterranean flavor inspired meatloaf dish. featuring: simply potatoes - shredded hash browns, egg, bacon, spinach, red bell pepper, and goat cheese... | ['meatloaf mixture', 'unsmoked bacon', 'goat cheese', 'unsalted butter', 'eggs', 'baby spinach', 'yellow onion', 'red bell pepper', 'simply potatoes shredded hash browns', 'fresh garlic', 'kosher salt', 'white pepper', 'olive oil'] |              13 |

- `RAW_interactions.csv` — Contains **731,927** user reviews and ratings

<div align="center">
  <iframe
   src="assets/interactions-head.html"
   width="800"
   height="400"
   frameborder="0">
  </iframe>
  <p><em>Table 2: First five rows of the RAW_interactions.csv dataset.</em></p>
</div>

While the second dataset helps understand user preferences, the primary focus for this analysis is on the first dataset, where the nutritional content is available.

## Relevant Columns for Our Analysis  

From `RAW_recipes.csv`:
- `calories`: Number of calories per serving (**target variable**)  
- `total_fat`: Total fat content (percentage of daily value)  
- `sugar`: Sugar content (percentage of daily value)  
- `sodium`: Sodium content (percentage of daily value)  
- `protein`: Protein content (percentage of daily value)  
- `saturated_fat`: Saturated fat content (percentage of daily value)  
- `n_ingredients`: Number of ingredients used in the recipe  
- `n_steps`: Number of preparation steps 

From `RAW_interactions.csv`:
- `user_id`: Identifier for the user submitting the rating  
- `recipe_id`: Identifier linking the rating to a specific recipe  
- `rating`: User’s rating of the recipe (typically on a scale of 1 to 5)  
- `date`: Date the rating or review was submitted  
- `review`: Optional written review by the user 

These features will be used to build a model capable of predicting the calorie content of a recipe based on its nutritional profile and complexity.
