# Recipes and Ratings
# Does the cooking time of a recipe affect the average rating?
A UCSD DSC 80 Project testing a dataset to see if cooking times affect the average rating.

Contributors: Jenna Canicosa and Adalina Ma


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
- [Conclusion](#conclusion)

## Introduction
Welcome to Recipe and Ratings Analysis! We used 2 datasets to conduct this analysis, one containing recipes and the other ratings from [Food.com](https://www.food.com/) which was last updated in 2008. Bodhisattwa Prasad Majumder, Shuyang Li, Jianmo Ni, and Julian McAuley originally scraped and used this data. The original recipes dataset held over 83,000 unique recipes and the ratings dataset held over 731,000 recipe reviews. With the large amount of data and information, there were many types of approaches to this project. **We decided to conduct our data analysis on checking if there is a correlation between cooking time and recipe ratings.** 

### Dataset Statistics and Definitions:
As stated above, the original recipes dataset has a total of 83,782 unique recipes and the ratings dataset has a total of 731,927 recipe reviews. We merged the two datasets and cleaned it so it only includes the necessary columns for our analysis. Below are the definitions of the columns we used from this dataset.

Recipes: Information found on [food.com](food.com)

| **Column Name**     | **Definition**                                    |
| ------------------- | ------------------------------------------------------------ |
| `name`            | Recipe name                                         |
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

The second step of our data cleaning process was to fill all ratings of 0 with `np.nan`. We performed this step, because the lowest rating that you can give is one star, so we can automatically assume that if a rating contains zero stars, no rating was given.  

The third step of our data cleaning process was to find the average rating per recipe, as a Series. We grouped by `id` before finding the mean of `ratings`. We made sure to rename the columns to `ratings` and `avg_rating`. After adding this Series containing the average rating per recipe back to the dataset.  

The fourth step of our data cleaning process was to filter out recipes that contained less than 5 reviews. We grouped by `id` before finding the count of `reciepe_name` and then sorted the values and getting all recipe names with 5 or more reviews. We then merged this to our dataset and named it `count`. 

The fifth step of our data cleaning process was to filter out recipes that took over 120 minutes because we noticed that almost 90 percent of the data was within 120 minutes. And this additionally helped filter out any outliers within the data. 

The first five rows of our dataframe are included below:

| name                                             |     id | user_id                                                                                                          | ratings                                                 |   n_steps |   avg_rating |   minutes |   contributor_id |
|:-------------------------------------------------|-------:|:-----------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------|----------:|-------------:|----------:|-----------------:|
| 0 carb   0 cal gummy worms                       | 283618 | [824257.0, 871801.0, 2000269452.0, 2001637319.0, 1803681343.0]                                                   | [5.0, 4.0, 5.0, nan, 5.0]                               |        15 |      4.75    |        45 |           134414 |
| 0 point soup   ww                                | 391705 | [166642.0, 1535.0, 228458.0, 464080.0, 4470.0, 1754326.0, 204024.0, 37779.0, 2000225933.0]                       | [5.0, 5.0, 5.0, 5.0, 5.0, 4.0, 5.0, 4.0, 5.0]           |         5 |      4.77778 |        55 |            39835 |
| 1 minute cake                                    | 290187 | [227652.0, 693373.0, 646093.0, 765161.0, 879045.0, 872425.0, 213139.0, 299046.0, 1383715.0, 308765.0, 1993336.0] | [4.0, 4.0, 4.0, 4.0, 4.0, 5.0, 4.0, 5.0, 1.0, 4.0, 5.0] |        20 |      4       |         2 |           584365 |
| 10 calorie chocolate miracle noodle cookies      | 478546 | [2249984.0, 1802657711.0, 2000301575.0, 2000920973.0, 2001773359.0]                                              | [3.0, 3.0, 4.0, nan, nan]                               |        26 |      3.33333 |        30 |          2247203 |
| 10 minute baked halibut with garlic butter sauce | 359203 | [4470.0, 90633.0, 653438.0, 369715.0, 242188.0, 1800042302.0, 1803231273.0]                                      | [5.0, 5.0, 5.0, 5.0, 5.0, 5.0, 5.0]                     |        16 |      5       |        35 |            37779 |


### Univariate Analysis
For our univariate analysis, we decided to focus on investigating the distribution of cooking time. We grouped by `minutes` to see the distribution within our dataset:

<p float = 'left'> 
    <iframe src="assets/uni-fig-all.html" width='100%' height= 435 align='center' frameBorder=0></iframe>
</p>

This illustrates how recipes distribute cooking time. We can comprehend the frequency of various cooking time intervals and their distribution throughout the dataset by developing a histogram of cooking time. For example, our original dataset before cleaning informed us of the potential ouliers as shown above.

This analysis is significant since it sheds light on how the recipes' total cooking time is distributed. It enables us to spot any patterns or outliers and understand the normal time of recipes. Planning meals, predicting preparation times, or figuring out which dishes take more or less time are just a few uses for this information. Here is our updated distribution.

<p float = 'left'> 
    <iframe src="assets/uni-fig-time.html" width='100%' height= 435 align='center' frameBorder=0></iframe>
</p>

We then were able to make the decision of filtering out recipes that exceeded 120 minutes for a more workable dataset. We can see the distribution's form, the range of cooking durations, and the most typical cooking time intervals from the histogram. It enables us to determine whether the majority of recipes have short or lengthy cooking durations, whether the distribution has any notable peaks or gaps, and whether it is biased towards particular time periods.

### Bivariate Analysis
<p style="text-align:center"><iframe src="assets/bivariate-plot-time.html" width='100%' height= 435 align='center' frameBorder=0></iframe></p>

Explanation: The scatter plot displays the relationship between cooking time and the average rating of recipes. It helps identify any correlation or patterns between these variables.

<p style="text-align:center"><iframe src="assets/bivariate-plot-steps.html" width='100%' height=435 align='center' frameBorder=0></iframe></p>



### Interesting Aggregates


|   count |   unique | top                       |   freq |
|--------:|---------:|:--------------------------|-------:|
|    2730 |     1379 | [5.0, 5.0, 5.0, 5.0, 5.0] |    265 |
|    3154 |     1771 | [5.0, 5.0, 5.0, 5.0, 5.0] |    270 |
|    2637 |     1611 | [5.0, 5.0, 5.0, 5.0, 5.0] |    204 |
|    2380 |     1511 | [5.0, 5.0, 5.0, 5.0, 5.0] |    196 |

Description:
The interesting aggregate presented in the pivot table calculates descriptive statistics on the ratings based on quartiles of the cooking time (`minutes`) of recipes that are under 120 `minutes`. The data is divided into four equal-sized groups based on the cooking time quartiles, and then descriptive statistics such as count, unique values, top rating, and frequency are calculated for each group.

This analysis is significant because it enables us to look at how recipe `ratings` change when cooking times are varied. We can spot any patterns or trends in the ratings linked with various time intervals by splitting the recipes into quartiles based on cooking time. These details can shed light on how customers feel and what they prefer about recipes with different cooking times.

From the output, we can observe several important pieces of information:

1. `Count`: The distribution of recipes over various cooking time ranges is shown by the number of recipes in each quartile. It aids in our comprehension of the relative frequency of each group's recipes.

2. `Unique`: The variety of ratings is indicated by the number of unique rating values inside each quartile. It indicates the range of ratings that consumers have given to recipes for each range of cooking times.


3. `Top`: The highest-rated value recorded in each quartile is represented by the top rating value within that quartile. It offers information on the highest rating attained during each range of cooking times.

4. `Frequency`: The frequency column shows how many times the most prevalent rating occurred within each quadrant. It enables us to get the most typical grade for each cooking time for a given recipe.

We can learn a lot about the connection between cooking time and ratings by looking at these descriptive statistics. We can determine the diversity of ratings within each range, the rating values most frequently linked with particular cooking time intervals, and whether particular cooking time ranges tend to obtain higher or lower ratings.

In conclusion, the intriguing aggregates explore the descriptive statistics inside quartiles to give a deeper insight of the relationship between cooking time and ratings. This study offers useful information for recipe selection and user preferences by revealing potential correlations or trends between these factors.

## Assessment of Missingness

### NMAR Analysis
Using a permutation test, we can assume that the missing values in `reviews` are not missing at random (NMAR). There is no apparent pattern as to why the values are missing, the missingness is dependent on the missing value itself. All recipes should have description and the cleaned dataset had a total of 0.02% missing values in the `reviews` column. This means the missing data depends on the actual value of the missing point, making the missingness to be NMAR. A potential explanation as to why this column contains missing values could be a user leaves a rating but not a review. It is easy to make a rating from 1-5 but one can be lazy and not elaborate on the rating. To make the values of `reviews` MAR, collecting more data related to the missingness mechanism such as obtaining the age of a user could be a factor that explains the missingness in the `rating` column. By collecting additional variables, the missingness could depend on older folks leaving reviews but the younger generation only leaving ratings without reviews.

### Missingness Dependency
When taking a further look at the missing values, we noticed that the columns `description`, `rating`, and `review` all contain missing values. We decided to investigate whether the missingness of `rating` corresponds to `n_steps` in the case a user stray away from recipes with more steps.

Null Hypothesis: Distribution of `n_steps` when `rating` is missing is the same as the distribution of `n_steps` when `rating` is not missing.

Alternative Hypothesis: Distribution of `n_steps` when `rating` is missing is *not* the same as the distribution of `n_steps` when `rating` is not missing.

The bar chart below displays the distribution of `n_steps` when `rating` is missing true vs. when missing is false sorted by bins.

<p style="text-align:center"><iframe src="assets/observed_missing.html" width='100%' height=425 align='center' frameBorder=0></iframe></p>

We performed a permutation test to determine whether or not the missingness of `rating` was dependent on `n_steps` using total variation distance (TVD) as the test statistic because `n_steps` is qualitative. By the graphs below, the number of steps most recipes falls within 1-23, meaning any recipe with a value over 23 is an outlier. To have an even distribution through bins, we separated the number of steps into the bins  ['(0, 5]', '(5, 10]', '(10, 20]', '(20, 100]' so each bin contains a similar number of ratings. 

<p style="text-align:center"><iframe src="assets/n_steps_probability_density.html" width='100%' height=425 align='center' frameBorder=0></iframe></p>


<p style="text-align:center"><iframe src="assets/n_step_box.html" width='100%' height=425 align='center' frameBorder=0></iframe></p>

The pivot table below illustrates the observed values.

|   bins  |   False  | True     |
|--------:|---------:|---------:|
|  (0, 5] | 0.236033 | 0.192338 |   
| (5, 10] | 0.398135 | 0.380819 |   
| (10,20] | 0.308405 | 0.336792 |   
|(20,100] | 0.057427 | 0.090051 |   

The plot below displays the empirical distribution of the TVDs with our observed TVD marked with red vertical line.  

<p style="text-align:center"><iframe src="assets/empirical_tvd.html" width='100%' height=425 align='center' frameBorder=0></iframe></p>

By running a permutation test by comparing the observed TVD, the p-value result was 0.0. Since our p-value is less than the significance level of 0.05, it is likely that we reject the null hypothesis. We can assume that the missingness in `recipe` is dependent on `n_steps`.

## Hypothesis Testing
After conducting our exploratory data analysis, does the cooking time of a recipe affect the average rating?

**Null hypothesis:** There is no significant correlation between cooking time and average rating of recipes.

**Alternative hypothesis:** There is a significant correlation between cooking time and average rating of recipes.
- Test statistic: Correlation Coefficient
- Significance level: 5% (0.05)
- Impact variables: Minutes, Rating

The degree and direction of the linear link between two variables are measured by the correlation coefficient. In this instance, we're looking to see if there's a meaningful relationship between cooking time and the overall rating of recipes. We can quantify the level of relationship between these two variables by utilizing the correlation coefficient as the test statistic.

The test statistic for a hypothesis test needs to be sensitive to the alternative hypothesis. The alternate theory in this case contends that there is a strong relationship between cooking time and average rating. We may evaluate the significance and direction of this association by utilizing the correlation coefficient as the test statistic.

The correlation coefficient is also a commonly accepted metric for assessing the relationship between continuous variables. It can be anything between -1 and +1, with -1 denoting a perfect negative correlation, +1 a perfect positive correlation, and 0 denoting no connection at all. The correlation coefficient's magnitude and sign both reveal the strength and direction of the link, respectively.

We may ascertain whether there is evidence to support the alternative hypothesis that there is a substantial correlation between cooking time and average rating by computing the correlation coefficient and comparing it to the null hypothesis of no association.

<p style="text-align:center"><iframe src="assets/hypothesis.html" width='100%' height=435 align='center' frameBorder=0></iframe></p>

**Results** 
- P-value: 0.0
- Correlation Coefficient: -0.0449
- Conclusion: The findings of the hypothesis test might be interpreted as follows: the null hypothesis is rejected because the obtained p-value (0.0000) is less than the significance level of 0.05. This suggests that the amount of time spent cooking and the average rating of recipes are significantly correlated. The two variables also have a weak negative link, as shown by the correlation coefficient of -0.0449, which means that as cooking time goes up, recipes' average ratings tend to go down a little.

It is important to remember that correlation does not indicate causation, and that factors other than cooking time may have an impact on how well a recipe is rated. Therefore, more research would be required to establish the causal link between cooking time and recipe average ratings.

## Conclusion
Through this project, we took 2 datasets, merged them, cleaned them and analyzed them. We explored what we learned in lecture by performing univariate analyses and bivariate analyses and aggression, explored NMAR values, and tested for missingness. To answer our main question, (does the cooking time of a recipe affect the average rating?) we performed a hypothesis test. We came conclusion to reject the null hypothesis, meaning there is a significant correlation between cooking time and average rating of recipes.
