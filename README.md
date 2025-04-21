# Recipes and Ratings Analysis  
**By:** Aayush Sharma  
**Contact:** *aayushsh@umich.edu*

---

## Introduction  
This project explores recipe and user interaction data from **Food.com**, a major recipe-sharing platform. The dataset includes thousands of recipes, user-submitted ratings, and reviews dating back to 2008.

**Investigative Question:**  
How can we predict the number of calories for a recipe in the dataset?

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

###### *Only relevant columns to the analysis are being displayed

**Table 1: Sample from `RAW_recipes.csv`**

| name                                 |     id |   minutes | nutrition                                     |   n_steps |
|:-------------------------------------|-------:|----------:|:----------------------------------------------|----------:|
| 1 brownies in the world    best ever | 333281 |        40 | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 |
| 1 in canada chocolate chip cookies   | 453467 |        45 | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 |
| 412 broccoli casserole               | 306168 |        40 | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 |
| millionaire pound cake               | 286009 |       120 | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 |
| 2000 meatloaf                        | 475785 |        90 | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 |

### Dataset 2: `RAW_interactions.csv`  
**Description:** Contains 731,927 user reviews and ratings.

**Table 2: Sample from `RAW_interactions.csv`**

|   recipe_id |   rating | date       |    user_id |
|------------:|---------:|:-----------|-----------:|
|       40893 |        5 | 2011-12-21 |    1293707 |
|       85009 |        5 | 2010-02-27 |     126440 |
|       85009 |        5 | 2011-10-01 |      57222 |
|      120345 |        0 | 2011-08-06 |     124416 |
|      120345 |        2 | 2015-05-10 | 2000192946 |

Note: While this dataset helps understand user preferences, the primary focus for this analysis is on the first dataset, where the nutritional content is available.

---

## Relevant Columns for Our Analysis  

### From Both Datasets:

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
| `rating`         |Rating between 1 - 5                              |


These features will be used to build a model capable of predicting the calorie content of a recipe based on its nutritional profile and complexity.

## Data Cleaning and Exploratory Data Analysis

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

I merged the calculated average rating back into the main dataset so that each row, which represents a user interaction or review, also included the average rating for the corresponding recipe.

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

| name                                 |   total_fat |   carbohydrates |   sodium |   sugar |   protein |   rating_average |   n_steps |
|:-------------------------------------|------------:|----------------:|---------:|--------:|----------:|-----------------:|----------:|
| 1 brownies in the world    best ever |          10 |               6 |        3 |      50 |         3 |                4 |        10 |
| 1 in canada chocolate chip cookies   |          46 |              26 |       22 |     211 |        13 |                5 |        12 |
| 412 broccoli casserole               |          20 |               3 |       32 |       6 |        22 |                5 |         6 |
| 412 broccoli casserole               |          20 |               3 |       32 |       6 |        22 |                5 |         6 |
| 412 broccoli casserole               |          20 |               3 |       32 |       6 |        22 |                5 |         6 |

## Univariate Analysis

### Distribution of Calories per Recipe

 <iframe
 src="assets/calories_boxplot.html"
 frameborder="0"
  width="800"
 height="600"
 ></iframe>

The violin plot above visualizes the distribution of calorie values across all recipes, with outliers removed for clarity. The shape of the plot highlights the density of data points — showing that most recipes cluster around the 175–450 calorie range. The thickest part of the distribution, near the median, gives a quick sense of central tendency, while the tails show the spread of higher and lower calorie recipes.

This visualization is particularly useful for the calorie prediction task ahead. It helps identify:
- The skewness of the distribution, which could impact model selection or preprocessing.
- Natural calorie clusters that could hint at underlying recipe types.
- Any residual outliers or irregular patterns that may need special handling.

Understanding this distribution ensures the model is trained on realistic, well-ordered data.

### Distribution of Sugar per Recipe

<iframe
 src="assets/sugar_histogram.html"
 frameborder="0"
  width="800"
 height="450"
></iframe>

This histogram shows the distribution of sugar content per recipe, with outliers removed. Most recipes contain less than 20 grams of sugar, with a sharp peak around the very low end — possibly indicating savory dishes or low-sugar meals. There is a long right tail, which suggests a minority of recipes (e.g. desserts or drinks) with much higher sugar content.

Understanding this distribution is crucial for building a calorie prediction model. Since sugar is often a strong contributor to calorie count, observing its skew and overall spread helps in:
- Identifying whether transformations are needed to correct skewness (e.g., QuantileTransformer).
- Spotting imbalances in recipe types (sweet vs. savory).
- Assessing the influence of sugar on calorie predictions.

This insight can guide feature engineering and help avoid bias in the model’s output.

## Bivariate Analysis

### Calories vs. Total Fat

<div style="margin: 0; padding: 0;">
  <iframe
    src="assets/fat_vs_calories.html"
    frameborder="0"
     width="800"
     height="450"
    style="display: block; margin: 0 auto; padding: 0;"
  ></iframe>
</div>

This boxplot shows how calorie count varies across different levels of total fat content, with outliers removed. There's a clear upward trend — as total fat increases, so do the calories. The distribution also widens, indicating more variability in high-fat recipes. This supports the idea that total fat is a strong predictor of calories, and will likely be an important feature in the calorie prediction model.

## Aggregate Analysis

### Average Nutrient Values by Steps Group

| Steps Group | Calories | Protein | Sugar | Total Fat |
|-------------|----------|---------|-------|-----------|
| Low         | 316.7    | 21.6    | 25.4  | 20.4      |
| Medium      | 423.8    | 29.2    | 30.4  | 29.4      |
| High        | 508.2    | 40.9    | 71.2  | 39.3      |

<iframe
src="assets/nutrient_bar_chart.html"
frameborder="0"
 width="800"
 height="450"
></iframe>

This pivot table groups recipes by the number of steps and shows the average nutritional values for each group. There's a clear upward trend: recipes with more steps tend to have more calories, protein, sugar, and fat.

This is significant because it suggests that **step count could be a useful feature when predicting calories**. It captures a pattern in the data that could help a model better estimate calorie content based on how complex a recipe is.

## Missing Values

The only imputation performed was shown in the Data Cleaning section. 

In this context, imputing nutritional values (like calories, protein, sugar, etc.) isn’t appropriate — these features are specific to the ingredients in each recipe. Filling them with averages or estimates would distort the data and potentially mislead any predictive models. As a result, **dropna()** was used to drop any null rows.

Out of 234,429 original recipes, 15,198 were removed due to missing values — a **6.48% decrease** in dataset size. This was an acceptable trade-off to preserve data integrity without introducing noise.

## Framing a Prediction Problem

### Problem Statement  
**How can we predict the number of calories in a recipe?**

---

### Type of Prediction Problem  
**Regression**

This is a regression problem because the target variable, **calories**, is a continuous numerical value. The goal is to predict an exact number rather than assign the input to a predefined category. Since the output can span a wide range of values, this aligns with the nature of regression tasks. If the target were categorical, such as classifying recipes into calorie ranges, it would be a classification problem.

---

### Response Variable  
The target variable in this prediction task is **calories**, representing the total caloric content of a recipe.

---

### Why This Variable?  
Calories are a fundamental measure of a recipe's nutritional profile. Estimating calories provides value in several ways:

- Helps users make informed dietary decisions  
- Enhances the accuracy of recipe recommendation tools  
- Supports nutrition-focused applications in delivering better insights  

---

### Evaluation Metric  
The model is evaluated using **Mean Squared Error (MSE)**.

---

### Why MSE?  
MSE is chosen because it:

- Quantifies the average squared difference between predicted and actual calorie values  
- Penalizes larger errors more heavily, encouraging more precise predictions  
- Is a widely accepted standard for regression problems, enabling clear model comparisons  

## Baseline Model

For the baseline regression model, I used fat and carbohydrates as baseline features because they’re key macronutrients that directly impact a food item's nutritional value, especially calorie content. Since both are numerical, they required no encoding and were a simple, effective starting point for evaluating model performance.

- **Quantitative Features**: `fat`, `carbohydrates`  
- **Ordinal Features**: _None_  
- **Nominal (Categorical) Features**: _None_  

I didn’t perform any encodings since all selected features were already numerical.

The entire modeling process was implemented using `sklearn`'s `Pipeline` to keep preprocessing and model training streamlined. The model used was `LinearRegression`.

---

## Model Evaluation

Model performance was evaluated using **Mean Squared Error (MSE)**:

- **Training Set**: `8,906.089`  
- **Validation Set (via K-Fold CV)**: `8,915.768`  
- **Testing Set**: `10,274.837`

The training and validation errors are very close, suggesting that the model generalizes reasonably well to unseen data and is not overfitting. However, the relatively high error values indicate that there’s room for improvement — likely by incorporating more informative features.
