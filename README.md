# How Many Calories Are Really in That Recipe?
**By:** Aayush Sharma  
**Contact:** *aayushsh@umich.edu*

---

## Introduction  
This project explores recipe and user interaction data from **Food.com**, a major recipe-sharing platform. The dataset includes thousands of recipes, their nutrition, user-submitted ratings, and reviews.

**Investigative Question:**  
How can we predict the number of calories for a recipe?

---

## Why This Matters  
Understanding how recipe characteristics influence calorie count is important for:

- People managing their dietary intake  
- Home cooks aiming to prepare healthier meals  
- Nutritionists and dietitians creating meal plans  

By modeling calorie content based on recipe features, we can gain insight into which types of recipes and preparation methods contribute most to total caloric value.

---

## Dataset Overview  

We use two datasets from Food.com.

### Dataset 1: `RAW_recipes.csv`  
**Description:** Contains 83,782 recipes with ingredients, steps, and nutrition information.

###### *Only a subset of columns from the datasets are being displayed

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

###### *While the Raw Interactions Dataset helps understand user preferences, the primary focus for this analysis is on the Raw Recipes dataset, where the nutritional content is available.

---

## Relevant Columns for Our Analysis  

### From Both Datasets:

| Column           | Description                                      |
|------------------|--------------------------------------------------|
| `calories`       | Number of calories per serving (target variable) |
| `total_fat`      | Total fat content (grams)                        |
| `sugar`          | Sugar content (% Daily Value)                    |
| `sodium`         | Sodium content (% Daily Value)                   |
| `protein`        | Protein content (% Daily Value)                  |
| `saturated_fat`  | Saturated fat content (% Daily Value)            |
| `n_ingredients`  | Number of ingredients                            |
| `n_steps`        | Number of preparation steps                      |
| `rating`         |Rating between 1 - 5                              |


These features will be used to build a model capable of predicting the calorie content of a recipe based on its nutritional profile and complexity.

---
---

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
 height="450"
 ></iframe>

The violin plot above visualizes the distribution of calorie values across all recipes, with outliers removed for clarity. The shape of the plot highlights the density of data points — showing that most recipes cluster around the 175–450 calorie range. The thickest part of the distribution, near the median, gives a quick sense of central tendency, while the tails show a right skew on the data.

This visualization is particularly useful for the calorie prediction task ahead. It helps identify:
- The skewness of the distribution, which could impact model selection or preprocessing.
- Natural calorie clusters that could hint at underlying recipe types.

Understanding this distribution ensures the model is trained on realistic, well-ordered data.

### Distribution of Sugar per Recipe

<iframe
 src="assets/sugar_histogram.html"
 frameborder="0"
  width="800"
 height="450"
></iframe>

This histogram shows the distribution of sugar content per recipe, with outliers removed. Most recipes contain less than 20% of the daily value of sugar, with a sharp peak around the very low end — possibly indicating savory dishes or low-sugar meals. There is a long right tail, which suggests a minority of recipes (e.g. desserts or drinks) with much higher sugar content.

Understanding this distribution is crucial for building a calorie prediction model. Since sugar is often a strong contributor to calorie count, observing its skew and overall spread helps in:
- Identifying whether transformations are needed to correct skewness (e.g., QuantileTransformer).
- Spotting imbalances in recipe types (sweet vs. savory).
- Assessing the influence of sugar on calorie predictions.

This insight can guide feature engineering pertaining to sugar for the final model.

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

These grouped boxplots show how calorie count varies across different levels of total fat content, with outliers removed. There's a clear upward trend — as total fat increases, so do the calories. The distribution also widens, indicating more variability in high-fat recipes. This supports the idea that total fat is a strong predictor of calories and will likely be an important feature in the calorie prediction model. The consistent increase in medians across bins shows a strong positive correlation between total fat and calorie count. Additionally, the widening interquartile ranges at higher fat levels suggest that calorie content becomes more dispersed, possibly due to the inclusion of richer, more energy-dense foods.

This plot directly addresses the question of whether fat content can help predict calories, and the visible trends strongly support that relationship.

## Aggregate Analysis

### Average Nutrient Values by Steps Group

| Steps Group | Calories | Protein(PDV) | Sugar(PDV) | Total Fat(PDV) |
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

---
---

## Framing a Prediction Problem

### Problem Statement:  
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
- MSE is a good indicator of overfitting as well 

---

### Evaluation Metric  
The model is evaluated using **Mean Squared Error (MSE)**.

---

### Why MSE?  
MSE is chosen because it:

- Quantifies the average squared difference between predicted and actual calorie values  
- Penalizes larger errors more heavily, encouraging more precise predictions  
- Is a widely accepted standard for regression problems, enabling clear model comparisons  

---
---

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

---
---

## Final Model

The final model uses a `RandomForestRegressor` inside a `Pipeline`, optimized with randomized hyperparameter search. The pipeline includes both preprocessing and modeling steps to ensure consistency and reproducibility.

### Features Used
Total: 10 features  
- **Quantitative Features (9)**:
  - `total_fat`
  - `carbohydrates`
  - `sodium`
  - `sugar`
  - `protein`
  - `fat_carb_ratio` (engineered)
  - `macro_total` (engineered)
  - `protein_ratio` (engineered)
  - `n_steps`
- **Nominal Feature (1)**:
  - `rating` (one-hot encoded)

### Why Each Feature Was Used & Transformed

- `total_fat`: Major contributor to calories (9 cal/g); normalized due to skew.
- `carbohydrates`: Key macronutrient (4 cal/g); transformed to stabilize variance.
- `sodium`: Correlated with processed foods and caloric density; transformed to handle outliers.
- `sugar`: High sugar usually implies high calories; normalized for consistency.
- `protein`: Another caloric macro (4 cal/g); normalized to align scales.
- `fat_carb_ratio`: Captures balance between two major macros and can give insight on calorie density.
- `macro_total`: Sum of all macros; strong linear signal for calories.
- `protein_ratio`: Measures how protein-dense a food is relative to other macronutrients. Including this feature helps the model distinguish between lean/high-protein foods and carb- or fat-dominant ones, even if total protein content is similar. It introduces a relative perspective that improves the model’s understanding of food composition.
- `n_steps`: Indicates how complex a recipe is to prepare. Recipes with more steps are often richer or more elaborate (e.g., multi-layered meals, sauces), which tend to be higher in calories. Including this feature provides context about the likely richness or simplicity of a dish that raw nutritional values don’t capture.
- `rating`: A user-generated rating that can reflect how enjoyable or satisfying a food is. Highly rated foods are often indulgent or rich, which can align with higher calorie content. By including this feature, the model incorporates human preference signals that may correlate with calorie density.


### Preprocessing

- **Quantitative features** were transformed using `QuantileTransformer` with `n_quantiles=50`, mapping skewed distributions to a normal shape.
- **Categorical feature** (`rating`) was encoded using `OneHotEncoder` with the first category dropped to prevent redundancy.

### Model and Hyperparameter Tuning

To improve model performance and prevent overfitting, I used **RandomizedSearchCV** to search for the best combination of hyperparameters for the `RandomForestRegressor`. This method randomly samples a fixed number of combinations from a predefined grid of possible values, rather than exhaustively evaluating all options like `GridSearchCV`. This makes it much more efficient, especially when the search space is large.

The randomized search was run over **20 different combinations**, using **5-fold cross-validation** to ensure reliable validation metrics and avoid depending on a single train/validation split.

**Scoring metric**:  
- I used **negative mean squared error (neg_MSE)** as the scoring function, since minimizing MSE is the main objective for this regression task.

**Parameters that were tuned**:
- `model__n_estimators`: Number of trees in the forest  
- `model__max_depth`: Maximum depth of each tree  
- `model__min_samples_split`: Minimum number of samples required to split a node  
- `model__min_samples_leaf`: Minimum number of samples required to be at a leaf node  
- `model__max_features`: Number of features considered when looking for the best split  
- `preprocessor__quantile_features__n_quantiles`: Number of quantiles for the `QuantileTransformer`, which affects how distributions are normalized

After tuning, the best combination of parameters was selected based on the lowest validation MSE across the 5 folds.


**Best Parameters Found**:
- `preprocessor__quantile_features__n_quantiles`: 50  
- `model__n_estimators`: 200  
- `model__max_depth`: None  
- `model__min_samples_split`: 2  
- `model__min_samples_leaf`: 2  
- `model__max_features`: None  

### Model Evaluation

| Metric         | Value       |
|----------------|-------------|
| Training MSE   | 1892.21     |
| Validation MSE | 5090.05     |
| Testing MSE    | 8680.47     |

The final model demonstrates stronger performance, especially in reducing training and validation error compared to the baseline. However, the large gap between training and validations shows potential signs of overfitting. 

<iframe
src="assets/models-comparison.html"
frameborder="0"
 width="800"
 height="450"
></iframe>

### Improvement?

Yes — the final model is a clear improvement over the baseline, especially where it matters most: **on unseen data**.

The baseline model had a **test MSE of approximately 10,275**, while the tuned final model achieved a significantly lower **test MSE of around 8680.47**. This drop of over 1,400 in error demonstrates that the final model generalizes better and makes more accurate predictions when faced with new data.

This improvement is due to several key changes:
- A much **richer set of features**, including meaningful engineered ones like `macro_total` and `protein_ratio`.
- Inclusion of contextual variables like `rating` and `n_steps`, which capture patterns that raw nutrition data may miss.
- Use of a **non-linear model (Random Forest)**, which can model complex interactions that a linear regression simply cannot.
- Application of **quantile-based scaling** to handle right-skewed distributions with nutrition.
- **Hyperparameter tuning** via `RandomizedSearchCV` to fine-tune model complexity.

While training and validation MSEs also improved significantly, the ultimate goal is a model that performs well on new, real-world data — and this final model does just that.


