# Examining Factors Associated with Power Outage Duration in the United States

## Introduction

**Power Outage Dataset:** [Purdue University LASCI Power Outages Dataset](https://engineering.purdue.edu/LASCI/research-data/outages)

The dataset used in this analysis covers major power outages that occurred in the continental United States from January 2000 to July 2016. It includes a wide range of data associated with each outage, including geographic information, climate information, demographic patterns at the location, and electricity consumption patterns.

I chose to examine the question: **What characteristics are associated with how long a power outage lasts? Specifically, how do power outage durations vary with population characteristics (urban vs. rural), cause of the outage, climate, and geographic region?**

This question is important because understanding the factors associated with outage duration can help identify potential weaknesses in power infrastructure and provide customers experiencing blackouts with better estimates of how long their outages may last. This dataset is particularly useful because its wide range of variables allows us to determine which features are most strongly associated with outage duration, including factors that may not initially appear to have an association.

The dataset contains 1,534 rows, with each row representing a major power outage, and 57 columns representing different variables. The most relevant columns for my analysis are:

| Column Name | Description |
| --- | --- |
| `OUTAGE.DURATION` | Duration of outage events (in minutes) |
| `CAUSE.CATEGORY` | Categories of all the events causing the major power outages |
| `POPPCT_URBAN` | Percentage of the total population of the U.S. state represented by the urban population (in %) |
| `CLIMATE.CATEGORY` | This represents the climate episodes corresponding to the years. The categories—“Warm”, “Cold” or “Normal” episodes of the climate are based on a threshold of ±0.5°C for the Oceanic Niño Index (ONI) |
| `POPDEN_RURAL` | Population density of the rural areas (persons per square mile) |

*Descriptions pulled directly from the Data Description listed in: Data on major power outage events in the continental U.S. - sciencedirect. (n.d.).[(https://www.sciencedirect.com/science/article/pii/S2352340918307182)].*

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

I first dropped the first row of the dataset because it just contained information on the type of variable in each column rather than information about an actual power outage. I then reset the index so that the DataFrame started at index 0 as opposed to index 1.

Next, I combined `OUTAGE.START.DATE` and `OUTAGE.START.TIME` into one `OUTAGE.START` column. I did the same for `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME`, combining them into an `OUTAGE.RESTORATION` column as recommended. Since the original date and time columns became redundant after creating these new columns, I dropped them along with the `variables` column, which was entirely filled with null values and was not related to the outages.

After examining the data types, I discovered that many of the columns that were supposed to contain numerical data were stored as objects. I converted these columns to numeric data types so that they could properly be used in numerical calculations and analyses. During this conversion, any non-numeric placeholders were converted to null values. This was especially important for columns such as `OUTAGE.DURATION`, `POPPCT_URBAN`, and `POPDEN_RURAL`, which were used frequently throughout my analysis and in creating the prediction model.

Finally, after examining the data using `value_counts`, I noticed inconsistent spacing in some of the values in `CAUSE.CATEGORY.DETAIL`. I stripped away the excessive whitespace so that values representing the same cause would be treated consistently rather than as separate categories.

The first five rows of the cleaned DataFrame are shown below:

|   | OBS | YEAR | MONTH | U.S._STATE | ... | PCT_WATER_TOT | PCT_WATER_INLAND | OUTAGE.START | OUTAGE.RESTORATION |
|---|---:|---:|---:|---|---|---:|---:|---|---|
| 0 | 1.0 | 2011.0 | 7.0 | Minnesota | ... | 8.41 | 5.48 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00 |
| 1 | 2.0 | 2014.0 | 5.0 | Minnesota | ... | 8.41 | 5.48 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00 |
| 2 | 3.0 | 2010.0 | 10.0 | Minnesota | ... | 8.41 | 5.48 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00 |
| 3 | 4.0 | 2012.0 | 6.0 | Minnesota | ... | 8.41 | 5.48 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00 |
| 4 | 5.0 | 2015.0 | 7.0 | Minnesota | ... | 8.41 | 5.48 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00 |

### Univariate Analysis

<iframe
    src="assets/univariate1.html"
    width="100%"
    height="500"
    frameborder="0"
></iframe>
This histogram shows the distribution of power outage durations. It shows that the distribution is extremely right-skewed, meaning that there are a few extremely high outliers that would drag the mean upward. Therefore, it would be best to use the median as the center point when conducting future analyses and making predictions.

### Bivariate Analysis

<iframe
    src="assets/bivariate1.html"
    width="100%"
    height="500"
    frameborder="0"
></iframe>
The outage duration was converted to a log scale to make trends in outage duration easier to see, as it is very right-skewed. This graph shows that fuel supply emergencies tend to lead to longer outages, while intentional attacks tend to lead to the shortest outages. Intentional attacks also have the largest IQR, showing that there is a large amount of variation in outage duration within this category.

### Interesting Aggregates

<iframe
    src="assets/pivot1.html"
    width="100%"
    height="500"
    frameborder="0"
></iframe>
This pivot table compares the median outage duration and the number of outages for each outage cause across cold, normal, and warm climate categories. The table shows that fuel supply emergencies generally have the longest median outage durations, while intentional attacks tend to have the shortest. Severe weather also has consistently high median outage durations across all three climate categories and has the largest number of outages overall. This suggests that outage cause is strongly associated with outage duration, while climate category may also affect the duration for some causes.

## Assessment of Missingness

### MNAR Analysis

It is likely that DEMAND.LOSS.MW is MNAR, or missing not at random, which means that the chances of a value being missing in this column could depend on the amount of demand lost. This variable measures the amount of peak demand lost during an outage, and if the demand loss is very low, it may be less likely to be measured or reported, causing these values to be missing more often. Additional data I could collect to determine if DEMAND.LOSS.MW is MAR is the individual electric company for each outage. I could then conduct an analysis to see whether the missingness of DEMAND.LOSS.MW is dependent on the company reporting the outage. If certain companies are more likely to have missing demand loss values, then the missingness could be explained by the reporting company and considered MAR rather than MNAR.

### Missingness Dependency

<iframe
    src="assets/missingness_plot.html"
    width="100%"
    height="500"
    frameborder="0"
></iframe>

I ran two permutation tests to examine whether the missingness of outage duration depends on other variables.
The first permutation test shows that the missingness of OUTAGE.DURATION is not dependent on POPPCT_URBAN. The observed difference in means was about 0.57 and the p-value was 0.688. Since the p-value is greater than 0.05, I fail to reject the null hypothesis that there is no difference in POPPCT_URBAN between outages where duration is missing and where it is not missing.

The second permutation test shows that the missingness of OUTAGE.DURATION is dependent on POPDEN_RURAL. The observed difference in means was about 12.10, and none of the 500 simulated differences were as large as the observed difference, giving a simulated p-value of 0. Since this is less than 0.05, I reject the null hypothesis and conclude that there is evidence that the missingness of OUTAGE.DURATION depends on rural population density. This suggests that the missingness of OUTAGE.DURATION may be MAR (Missing At Random), since its missingness is associated with another observed variable in the dataset, POPDEN_RURAL.

The histogram also shows a difference between the distribution of rural population density when OUTAGE.DURATION is missing versus when it is not missing. Missing outage durations are more concentrated at lower rural population densities, showing an association between the variables, while non-missing outage durations are more spread out across higher values.

This is important to my research question because I am examining how outage duration varies with urban and rural population characteristics. Since outage duration is more likely to be missing for certain rural population densities, this missingness could affect the relationships between population characteristics and outage duration that I observe in my analysis.

## Hypothesis Testing

Null Hypothesis: Power outages caused by severe weather and power outages caused by other causes have the same median outage duration. Any difference we see between the two groups is due to random chance.

Alternative Hypothesis: Power outages caused by severe weather have a longer median outage duration than power outages caused by other causes.

Test Statistic: Median outage duration for severe weather outages minus median outage duration for non-severe weather outages. I chose to use the median because outage duration is extremely right-skewed and has many large outliers, so the median better represents a typical outage duration. I used a one-sided test because I am specifically testing whether severe weather outages have a longer median duration than other outages, rather than just testing whether the two groups have different median durations. This is appropriate to test because it was discovered in the bivariate plot that severe weather appears to have higher
median durations.

Significance Level: I used a significance level of 0.05 because it is a commonly used significance level for hypothesis tests.

The observed difference in medians was 2317.5 minutes, meaning that severe weather outages had a median outage duration 2317.5 minutes longer than non-severe weather outages. The permutation test gave a simulated p-value of 0.0 which is below the significance level of 0.05, meaning I reject the null hypothesis and conclude that there is evidence that power outages caused by severe weather have a longer median outage duration than power outages caused by other causes. These choices make sense for my question because I am trying to determine what characteristics are associated with outage duration, and one of the characteristics I am examining is the cause of the outage. Comparing severe weather outages to other outages allows me to see whether severe weather is associated with longer outage durations.

## Framing a Prediction Model

With this model, I want to predict the duration of a major power outage using its cause, climate conditions, geographic characteristics, and urban/rural population characteristics. This is a regression problem because duration is a numerical value. I want to predict OUTAGE.DURATION because it would give valuable information to customers in cases of outages so they know if they should prepare or postpone tasks. The metric I am using is RMSE (Root Mean Squared Error) because it measures how far the predicted outage durations are from the actual outage durations in minutes. I chose RMSE over MAE (Mean Absolute Error) because RMSE gives more weight to large prediction errors, which is important because being extremely inaccurate when predicting how long an outage will last could be problematic for those who expected a much shorter duration and prepared as such. At the time of prediction, shortly after the outage occurs and its cause has been identified, climate conditions, geographic characteristics, and urban/rural population characteristics would already be known from previous data and surveys. The cause of the outage would also be known at this point, so these are all viable features to use when trying to predict outage duration.

## Baseline Model

The model is a linear regression model and uses CAUSE.CATEGORY, a nominal feature, and POPPCT_URBAN, a quantitative feature. CAUSE.CATEGORY was one-hot encoded because it contains categorical values that cannot be directly used by the linear regression model, and there is no natural ordering between the different causes. POPPCT_URBAN was left unchanged because it is already quantitative. The model had a test RMSE of 7015.32 minutes, meaning that its predictions are typically off from the actual outage durations by roughly 7015 minutes, or 4.9 days. This RMSE was calculated on the testing data, which was not used to train the model, so it measures how well the model generalizes to unseen data. I do not believe this is a particularly good model because being off by around 4.9 days is a pretty significant error when trying to predict how long an outage will last and could affect someone’s actions and preparation during an outage.

## Final Model

I used a Random Forest regression model and ended up using the hyperparameters max_depth = 10 and min_samples_split = 50. The Random Forest regression model seemed most appropriate because it can capture more complex and nonlinear relationships between the different features and outage duration than the linear regression model used for the baseline. It also works well with the combination of categorical and quantitative features I included in the model.

The hyperparameters were chosen using five-fold cross-validation because this allowed me to compare different combinations of max_depth and min_samples_split based on their performance across multiple portions of the training data, rather than choosing the parameters based on a single split. I tested multiple values for both hyperparameters using GridSearchCV and selected the combination that resulted in the lowest cross-validation RMSE. I also compared multiple versions of the Random Forest model with different sets of features and chose the final model based on which had the lowest testing RMSE and the lowest cross-validation RMSE. 

The final model had an RMSE of 6962.31 minutes, which is about 4.84 days off from reality. It is about 53 minutes more accurate at predicting outage duration than the baseline linear regression model, which had an RMSE of 7015.32 minutes.

## Fairness Analysis

Group X: Areas with higher rural population density, meaning observations where POPDEN_RURAL is greater than or equal to the median rural population density.

Group Y: Areas with lower rural population density, meaning observations where POPDEN_RURAL is below the median rural population density.

Evaluation Metric: RMSE (Root Mean Squared Error), which measures prediction error in minutes.

Null Hypothesis: The model has approximately the same RMSE for areas with higher and lower rural population density, and any difference in RMSE is due to random chance.

Alternative Hypothesis: The model has a statistically higher RMSE for areas with higher rural population density than areas with lower rural population density.

Test Statistic: I used the difference in RMSE between the higher rural population density group and the lower rural population density group: Higher Rural Population Density RMSE - Lower Rural Population Density RMSE.
Significance Level: I used a significance level of 0.05 because it is a commonly used cutoff for determining statistical significance.

The observed difference in RMSE was 4635.43 minutes, with the model having a higher RMSE for areas with higher rural population density. The permutation test resulted in a p-value of 0.281, which is greater than the significance level of 0.05, so I fail to reject the null hypothesis. There is not enough evidence to conclude that the final model performs worse for areas with higher rural population density. Although the observed RMSE was higher for the higher rural population density group, the permutation test suggests that this difference could be due to random chance.


