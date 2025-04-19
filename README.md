# Recipes and Ratings Analysis  
By: Aayush Sharma  
Contact: *(your website link or email)*

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
- `RAW_interactions.csv` — Contains **731,927** user reviews and ratings  

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

