# Examining Factors Associated with Power Outage Duration in the United States

## Introduction

**Power Outage Dataset:** [Purdue University LASCI Power Outages Dataset](https://engineering.purdue.edu/LASCI/research-data/outages)

The dataset used in this analysis covers major power outages that occurred in the continental United States from January 2000 to July 2016. It includes a wide range of data associated with each outage, including geographic information, climate information, demographic patterns at the location, and electricity consumption patterns.

I chose to examine the question: **What characteristics are associated with how long a power outage lasts? Specifically, how do power outage durations vary with population characteristics (urban vs. rural), cause of the outage, climate, and geographic region?**

This question is important because understanding the factors associated with outage duration can help identify potential weaknesses in power infrastructure and provide customers experiencing blackouts with better estimates of how long their outages may last. This dataset is particularly useful because its wide range of variables allows us to determine which features are most strongly associated with outage duration, including factors that may not initially appear to have an association.

The dataset contains **1,534 rows**, with each row representing a major power outage, and **57 columns** representing different variables. The most relevant columns for my analysis are:

| Column Name | Description |
| --- | --- |
| `OUTAGE.DURATION` | Duration of outage events (in minutes) |
| `CAUSE.CATEGORY` | Categories of all the events causing the major power outages |
| `POPPCT_URBAN` | Percentage of the total population of the U.S. state represented by the urban population (in %) |
| `CLIMATE.CATEGORY` | This represents the climate episodes corresponding to the years. The categories—“Warm”, “Cold” or “Normal” episodes of the climate are based on a threshold of ±0.5°C for the Oceanic Niño Index (ONI) |
| `POPDEN_RURAL` | Population density of the rural areas (persons per square mile) |

*Descriptions pulled directly from the Data Description listed in: [Data on major power outage events in the continental U.S.](https://www.sciencedirect.com/science/article/pii/S2352340918307182).*
