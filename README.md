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
