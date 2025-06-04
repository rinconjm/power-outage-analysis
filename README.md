<!Doc>


# Understanding Power Outages Across the U.S.

**Name**: Janice Rincon

**Website Link**: https://rinconjm.github.io/power-outage-analysis/

---

## Introduction

In this project I examined a dataset of major power outages across the continental United States from January 2000 to July 2016. This comprehensive dataset includes 1,534 records detailing major outages, which are defined by the U.S. Department of Energy to be outages affecting at least 50,000 customers or resulting in a loss of 300 megawatts or greater. The [dataset](https://engineering.purdue.edu/LASCI/research-data/outages') can be accessed from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure.

Included in the dataset is information regarding the geographical location of the outages, regional climatic information, land-use characteristics, electricity consumption patterns and economic characteristics of the states affected by the outages.

The central question I am exploring is: <b>How have the characteristic of major U.S. power outages changed over time, and is there a clear trend in their frequency or severity?</b> I aim to use data analysis techniques to examine temporal patterns in outage events, focusing on geographic, demographic, and environmental factors such as climate region, population density, and anomaly levels. 

Building on this analysis, I will develop a <b>predictive model</b> to estimate the characteristics of future outages based on historical trends and regional attributes. This model can help inform policy decisions, infrastructure planning, and climate resilience strategies by anticipating the likelihood or severity of outages under regional conditions. 

### Introduction of Columns
The original dataset includes 1534 rows and 57 columns. For the sake of my analysis, I will focus only a subset of these. 

|Column                   |Description|
|:------------------------|:----------|
|`YEAR`                   |The year when a power outage occurred |
|`MONTH`                  |The month when a power outage occurred |
|`U.S.-STATE`             |The U.S. state where the outage took place|
|`NERC.REGION`            |Indicates the North American Electric Reliability Corporation (NERC) region associated with the outage event|
|`CLIMATE.REGION`         |U.S. climate regions, as defined by the National Centers for Environmental Information, where the power outage took place  |
|`ANOMALY.LEVEL`          |Indicates the oceanic El Niño/La Niña Index (ONI) value, representing sea surface temperature (SST) anomalies in the  Niño 3.4 region  |
|`CLIMATE.CATEGORY`       |Categorization of climate episodes based on the corresponding year and ONI value: "Warm" (El Niño), "Cold" (La Niña), or "Normal"|
|`OUTAGE.START.DATE`      |Indicates the day of the year when the outage event began (as reported by the corresponding Utility in the region) |
|`OUTAGE.START.TIME`      |Indicates the time of day when the outage event began (as reported by the corresponding Utility in the region) |
|`OUTAGE.RESTORATION.DATE`|Indicates the day of the year when power was restored to all affected customers (as reported by the corresponding Utility in the region) |
|`OUTAGE.RESTORATION.TIME`|Indicates the time of the day when power was restored to all affected customers (as reported by the corresponding Utility in the region) |
|`CAUSE.CATEGORY`         |Categories of all primary causes of the power outage|
|`OUTAGE.DURATION`        |The duration of the outage event (in minutes) |
|`DEMAND.LOSS.MW`         |Amount of peak demand or total demand lost during an outage event (in Megawatt)|
|`CUSTOMERS.AFFECTED`     |The number of customers affected by the power outage |
|`TOTAL.CUSTOMERS`        |Annual number of total customers served in the U.S. state |

---

## Data Cleaning and EDA

### Data Cleaning

I took the following steps to clean the data:

1. **Resolved Formatting Issues:** During the conversion from `.xlsx` to `.csv`, the file contained several formatting issues, specifically, the first few rows included `NaN` values, and the actual column headers were located on the fifth row. These rows were removed and the correct headers were reassigned to the dataset.
2. **Keep Relevant Columns:** The columns that were not relevant to my research question where dropped from the data frame, keeping only the following columns of interest: `YEAR`, `MONTH`, `U.S._STATE`, `NERC.REGION`, `CLIMATE.REGION`, `ANOMALY.LEVEL`, `CLIMATE.CATEGORY`, `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, `OUTAGE.RESTORATION.TIME`, `CAUSE.CATEGORY`, `OUTAGE.DURATION`, `DEMAND.LOSS.MW`, `CUSTOMERS.AFFECTED`, `TOTAL.CUSTOMERS`.
3. **Combinations:** The columns `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME` were merged into two columns: `OUTAGE.START` and `OUTAGE.RESTORATION`, respectively. Both columns are of `pd.Timestamp` format.
4. **Type Conversions**: ??
5

### Univariate Analysis


### Bivariate Analysis


### Interesting Aggregates


---

## Assessment of Missingness

### NMAR Analysis

### Missingness Dependency

---

## Hypothesis Testing


---

## Problem Identification


---
## Baseline Model


---
## Final Model


---
## Fairness Analysis


---