# Understanding Power Outages Across the U.S.

**Name**: Janice Rincon

**Website Link**: https://rinconjm.github.io/power-outage-analysis/

---

## Introduction

In this project I examined a dataset of major power outages across the continental United States from January 2000 to July 2016. This comprehensive dataset includes 1,534 records detailing major outages, which are defined by the U.S. Department of Energy to be outages affecting at least 50,000 customers or resulting in a loss of 300 megawatts or greater. The [dataset](https://engineering.purdue.edu/LASCI/research-data/outages') can be accessed from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure.

Included in the dataset is information regarding the geographical location of the outages, regional climatic information, land-use characteristics, electricity consumption patterns and economic characteristics of the states affected by the outages.

The central question I am exploring is: <b>what are are the characteristics and causes of major power outages? </b> I aim to use data analysis techniques to examine temporal patterns in outage events, as well as patterns in demographic, and environmental factors such as climate region and anomaly levels. 
Building on this analysis, I will develop a <b>predictive model</b> to estimate the length(duration) of major power outage given a selection of main characteristics. 

This analysis is important because it can help inform policy decisions and climate resilience plans by anticipating the severity of outages under regional conditions. 

### Introduction of Columns
The original dataset includes 1534 rows and 57 columns. For the sake of my analysis, I will focus only a subset of these. Below are the chosen columns and their descriptions:

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

The following steps were performed to clean the data:

1. **Resolved Formatting Issues:** 
    - During the conversion from `.xlsx` to `.csv`, the file contained several formatting issues, specifically, the first few rows included `NaN` values, and the actual column headers were located on the fifth row. These rows were removed and the correct headers were reassigned to the dataset.
2. **Keep Relevant Columns:** 
    - The columns that were not relevant to my research question where dropped from the data frame, keeping only the following columns of interest: `YEAR`, `MONTH`, `U.S._STATE`, `NERC.REGION`, `CLIMATE.REGION`, `ANOMALY.LEVEL`, `CLIMATE.CATEGORY`, `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, `OUTAGE.RESTORATION.TIME`, `CAUSE.CATEGORY`, `OUTAGE.DURATION`, `DEMAND.LOSS.MW`, `CUSTOMERS.AFFECTED`, `TOTAL.CUSTOMERS`.
3. **Combinations:** 
    - The columns `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME` were merged into two columns: `OUTAGE.START` and `OUTAGE.RESTORATION`, respectively. Both columns are of `pd.Timestamp` format. 
    - The original columns were dropped from the data frame after merging
4. **Type Conversions**:
    - Several quantitative columns were originally stored as strings. These were converted to the appropriate numeric type to be compatible with analysis and future modeling. 
5. **Feature Engineering:**
    - A new column, `OUTAGE.DURATION.HRS`, was created by converting outage duration from minutes to hours for easier interpretation.

The cleaned DataFrame contains 1,534 rows and 15 columns.
Below are the first few rows of the processed dataset:

|   YEAR |   MONTH | U.S._STATE   | NERC.REGION   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | CAUSE.CATEGORY     | OUTAGE.START        | OUTAGE.RESTORATION   |   OUTAGE.DURATION |   OUTAGE.DURATION.HRS | DEMAND.LOSS.MW   | CUSTOMERS.AFFECTED   |   TOTAL.CUSTOMERS |   HOUR | HOUR_LABEL   |
|-------:|--------:|:-------------|:--------------|:-------------------|----------------:|:-------------------|:-------------------|:--------------------|:---------------------|------------------:|----------------------:|:-----------------|:---------------------|------------------:|-------:|:-------------|
|   2011 |       7 | Minnesota    | MRO           | East North Central |            -0.3 | normal             | severe weather     | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |              3060 |            51         | <NA>             | 70000                |           2595696 |     17 | 5 PM         |
|   2014 |       5 | Minnesota    | MRO           | East North Central |            -0.1 | normal             | intentional attack | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |                 1 |             0.0166667 | <NA>             | <NA>                 |           2640737 |     18 | 6 PM         |
|   2010 |      10 | Minnesota    | MRO           | East North Central |            -1.5 | cold               | severe weather     | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |              3000 |            50         | <NA>             | 70000                |           2586905 |     20 | 8 PM         |
|   2012 |       6 | Minnesota    | MRO           | East North Central |            -0.1 | normal             | severe weather     | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |              2550 |            42.5       | <NA>             | 68200                |           2606813 |      4 | 4 AM         |
|   2015 |       7 | Minnesota    | MRO           | East North Central |             1.2 | warm               | severe weather     | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |              1740 |            29         | 250              | 250000               |           2673531 |      2 | 2 AM         |


### Univariate Analysis

#### Distribution of Severity

<iframe
  src="assets/outage_duration_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The histogram shows that most power outages lasted less than 100 hours, with a very steep drop-off in frequency as the duration increases. The distribution is heavily skewed right which indicates the presence of extreme values, with some outages lasting over 1,000 hours.
This suggests that while most power outages are relatively short, the average and variance of overall duration is impacted by a few severe events.

#### Temporal Patterns
<iframe
  src="assets/outages_per_month_dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This bar chart shows that major power outages were most frequent during the summer months, peaking in June and July, with over 180 incidents each. This suggests a seasonal trend, most likely influenced by the increased demand in electricity and weather events (heatwaves, storms, etc.)
In contrast, November and September had the fewest outages, with under 100 incidents. Overall, outages were most common between June and August which suggests that summer presents a greater risk to infrastructure.  

#### Geographic Patterns
<table>
  <tr>
    <td>
      <iframe src="assets/outage_count_map.html" width="450" height="300" style="border:none;"></iframe>
    </td>
    <td>
      <iframe src="assets/avg_duration_map.html" width="450" height="300" style="border:none;"></iframe>
    </td>
  </tr>
  <tr>
    <td align="center">Outage Count by State</td>
    <td align="center">Avg. Outage Duration by State</td>
  </tr>
</table>


#### Cause Analysis


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