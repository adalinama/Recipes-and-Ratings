# Recipes-and-Ratings
# Does the cooking time of a recipe affect the average rating?
A UCSD DSC 80 Project testing a dataset to see if cooking times affect the average rating.

Contributors: [Jenna Canicosa](https://www.linkedin.com/in/jenna-canicosa/) and [Adalina Ma](https://www.linkedin.com/in/adalina-ma-bba89b24b/)


## Table of Contents
- [Introduction](#introduction)
    - [Dataset Statistics and Definitions](#dataset-statistics-and-definitions)
- [Cleaning and EDA (Exploratory Data Analysis)](#cleaning-and-eda)
    - [Data Cleaning](#data-cleaning)
    - [Univariate Analysis](#univariate-analysis)
    - [Bivariate Analysis](#bivariate-analysis)
    - [Interesting Aggregates](#interesting-aggregates)
- [Assessment of Missingness](#assessment-of-missingness)
    - [NMAR Analysis](#nmar-analysis)
    - [Missingness Dependency](#missingness-dependency)
- [Hypothesis Testing](#hypothesis-testing)
- [Concluding Note](#concluding-note)

## Introduction
Welcome to Recipe and Ratings Analysis! We used 2 datasets to conduct this analysis, one containing recipes and the other ratings from [Food.com](https://www.food.com/) last updated in 2008. The original recipes dataset held over 83,000 unique recipes and the ratings dataset held over 731,000 recipe reviews. With the large amount of data and information, there were many types of approaches to this project. **We decided to conduct our data analysis on checking if there is a correlation between cooking time and recipe ratings.** 

### Dataset Statistics and Definitions:
As stated above, the original recipes dataset held over 83,000 unique recipes and the ratings dataset held over 731,000 recipe reviews. Below are the definitions of the columns we used from this dataset.
Recipes: Information found on [food.com](food.com)

| **Column Name**     | **Definition**                                    |
| ------------------- | ------------------------------------------------------------ |
| `name’            | Recipe name                                         |
| `id`  | Recipe ID |
| `minutes`          | Minutes to prepare recipe              |
| `contributor_id`             | User ID who submitted this recipe                                      |
| `submitted`            | Date recipe was submitted                                      |
| `tags`           | Food.com tags for recipe                                     |
| `nutrition` | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value”                       |
| `n_steps`         | Number of steps in recipe                                       |
| `steps`          | Text for recipe steps, in order                                |
| `description `          | User-provided description                             |

Ratings

| **Column** | **Description** |
| -------------------------- | ----------- |
| *user_id*                      | User ID         |
| *recipe_id*                      | Recipe ID      |
| *date*                      | Date of interaction      |
| *rating*                      | Rating given      |
| *review*                      | Review text     |


## Cleaning and EDA (Exploratory Data Analysis) 

### Data Cleaning
The first step of our data cleaning process was to merge our two datasets together. We performed a left merge on the recipes and interactions datasets together. 

The second step of our data cleaning process was to fill all ratings of 0 with` np.nan`. We performed this step, because the lowest rating that you can give is one star, so we can automatically assume that if a rating contains zero stars, no rating was given.  

The third step of our data cleaning process was to find the average rating per recipe, as a Series. We grouped by `id` before finding the mean of `ratings`. We made sure to rename the columns to `ratings` and `avg_rating`. After adding this Series containing the average rating per recipe back to the dataset.  

The fourth step of our data cleaning process was to filter out recipes that contained less than 5 reviews. We grouped by `id` before finding the count of `reciepe_name` and then sorted the values and getting all recipe names with 5 or more reviews. We then merged this to our dataset and named it `count`. 

The fifth step of our data cleaning process was to filter out recipes that took over 120 minutes because we noticed that almost 90 percent of the data was within 120 minutes. And this additionally helped filter out any outliers within the data. 

The first five rows of our dataframe are included below:


