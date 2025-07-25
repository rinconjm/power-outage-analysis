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
|`U.S._STATE`             |The U.S. state where the outage took place|
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
  height="500"
  frameborder="0"
></iframe>

The histogram shows that most power outages lasted less than 100 hours, with a very steep drop-off in frequency as the duration increases. The distribution is heavily skewed right which indicates the presence of extreme values, with some outages lasting over 1,000 hours.
This suggests that while most power outages are relatively short, the average and variance of overall duration is impacted by a few severe events.

#### Temporal Patterns
<iframe
  src="assets/outages_per_month_dist.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

This bar chart shows that major power outages were most frequent during the summer months, peaking in June and July, with over 180 incidents each. This suggests a seasonal trend, most likely influenced by the increased demand in electricity and weather events (heatwaves, storms, etc.)
In contrast, November and September had the fewest outages, with under 100 incidents. Overall, outages were most common between June and August which suggests that summer presents a greater risk to infrastructure.  

<iframe
  src="assets/outages_by_time.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

Interestingly enough, when visualizing the distribution of outages based on the time of day, it was found that the majority of outages occurred between 1pm and 4pm, peaking around 3pm. Morning to early evening (7am-6pm) consistently shows high outage frequency, indicating that most outages occur during active business hours, not overnight. 


#### Geographic Patterns
<table>
  <tr>
    <td>
      <iframe src="assets/outage_count_map.html" 
      width="450" 
      height="300" 
      frameborder="0"></iframe>
    </td>
    <td>
      <iframe src="assets/avg_duration_map.html" 
      width="450" 
      height="300" 
      frameborder="0"></iframe>
    </td>
  </tr>
  <tr>
    <td align="center">Outage Count by State</td>
    <td align="center">Avg. Outage Duration by State</td>
  </tr>
</table>

The map on the left, **Outage by State** shows that California experienced the highest number of outages. This is likely due to factors such as wildfire risk and population size. Texas, along with parts of the Midwest and the Northeast show a higher outage frequency. However, the South as well as states located in the center of the country reported relatively few major outages, potentially due to lower population density.

The map on the right, **Average Outage Duration by State**, shows that states in the Midwest and Northeast, particularly Michigan and West Virginia, show the longest average durations. Interestingly enough, California shows only moderate average durations, despite having the most reported outages. 


#### Cause Analysis
<iframe
  src="assets/outages_by_cause_hist.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

This chart shows that **severe weather** was the leading cause of major power outages in the U.S. between 2000 and 2016, accounting for nearly 800 incidents. The second most common cause was **intentional attacks**, with over 400 incidents.
All other causes occurred significantly less, with less than 130 incidents each.


### Bivariate Analysis
<iframe
  src="assets/cause_over_time_line.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The line chart shows how major power outages in the U.S. varied by cause from 2000 to 2016. Severe weather was the most dominant and consistent cause, continuously increasing from 2003 and 2012, and peaking around 2011. Intentional attacks spiked in 2011, briefly surpassing all other causes before declining, possibly due to isolated high-impact incidents. Before 2010, however, it showed a quantity of 0 perhaps suggesting a lack of recording.  

Other causes (such as equipment failure, fuel supply emergencies, and system operability disruptions) were relatively low and stable. Overall, the chart highlights that natural and intentional disruptions were the primary causes of major outages.

<iframe
  src="assets/outage_dur_by_cust_affected.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The scatter plot visualizes the relationship between power outage duration (in hours) and the number of customers affected. Most outages last less than 500 hours, and the majority of customers affected is 500k. Interestingly enough, there does not appear to be a clear linear correlation between the two variables, suggesting that longer outages do not necessarily affect more customers. 


### Interesting Aggregates

The table below shows the average outage duration by hour of day the outage occurred and cause category. Severe Weather appears to have the most even distribution, with no severe spikes. In contrast, there appear to be large values for equipment failure and fuel supply emergencies at odd hours. This suggests that the cause of an outage may influence not just how long it lasts, but when it tends to occur.

The first few rows of the aggregate is shown below:

|   HOUR |   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
|-------:|--------------------:|------------------------:|---------------------:|------------:|----------------:|-----------------:|--------------------------------:|
|      0 |               14.75 |                 1810.88 |                 4.5  |        3.25 |             nan |            71.99 |                           53    |
|      1 |              nan    |                    0.02 |                12.07 |       17.25 |             nan |           102.05 |                           19.62 |
|      2 |              nan    |                  nan    |                21.67 |        3.22 |             nan |            47.81 |                            5.59 |
|      3 |                6.66 |                  nan    |                 6.34 |        0.58 |             nan |            58.28 |                           15.52 |
|      4 |              nan    |                   72    |                14.59 |        0.83 |             nan |            80.5  |                           13.6  |
|      5 |               11.95 |                   48    |                17.08 |        0.25 |             nan |            62.98 |                           28.28 |
|      6 |              445.28 |                  nan    |                 3.84 |      nan    |              78 |           106.3  |                            9.13 |

---

## Assessment of Missingness

### NMAR Analysis

Of all the columns that have missing data, the column most likely to be NMAR is `OUTAGE.LOST.MW`. When demand loss is very low, utilities may consider it negligible and choose to not report it. On the other hand, when demand loss is very high, utilities may choose not to report to as to not damage their reputation or for political reasons.

### Missingness Dependency
In order to test missingness dependency, I will focus on the column of `DEMAND.LOSS.MW` which has a significant/non-trivial missingness. This will be tested against the columns `CAUSE.CATEGORY` and `HOUR`.

#### Dependency on `CAUSE.CATEGORY`
The distribution of `CAUSE.CATEGORY` is plotted when `DEMAND.LOSS.MW` is *missing* and *not missing*.

- **Null Hypothesis:** There is no change in the distributions of `CAUSE.CATEGORY` when `DEMAND.LOSS.MW` is *missing* and *not missing*.
- **Alternative Hypothesis:** The distributions of `CAUSE.CATEGORY` when `DEMAND.LOSS.MW` is *missing* and *not missing* are different.
- **Test Stat:** Total Variation Distance (TVD).

<iframe
  src="assets/cause_by_missing_MW.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The observed TVD was **0.18**, with a p-value of **0.0**. Given this result, we reject the null hypothesis in favor of the alternative. This suggests that the mechanism behind missing values in `DEMAND.LOSS.MW` is not random, and is likely dependent on `CAUSE.CATEGORY`.

The empirical distribution of TVD values across 500 permutations is shown below:

<iframe
  src="assets/tvd_dist_cause_cat.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>



#### Dependency on `HOUR`
The distribution of `HOUR` is plotted when `DEMAND.LOSS.MW` is missing and not missing.

- **Null Hypothesis:** There is no change in the distributions of `HOUR` when `DEMAND.LOSS.MW` is *missing* and *not missing*.
- **Alternative Hypothesis:** The distributions of `HOUR` when `DEMAND.LOSS.MW` is *missing* and *not missing* are different.
- **Test Stat:** Total Variation Distance (TVD).

<iframe
  src="assets/cause_by_missing_hour.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The observed TVD was **0.1**, with a p-value of **0.302**. Given this result, we do not reject the null hypothesis in favor of the alternative. This suggests that the distribution of `HOUR` is not significantly different when `DEMAND.LOSS.MW` is missing/not missing. In other words, missingness in `DEMAND.LOSS.MW` does not appear to depend on the time of day when the outage occurred.

The empirical distribution of TVD values across 500 permutations is shown below:

<iframe
  src="assets/tvd_dist_cause_hour.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>
---

## Hypothesis Testing
To better understand how the cause of an outage relates to its severity, we compare the average duration of power outages caused by severe weather versus those caused by equipment failure. This comparison helps identify whether weather-related events tend to be more disruptive in terms of recovery time. 

- **Null Hypothesis:** On average, the duration of power outages is the same for severe weather and equipment failure.
- **Alternative Hypothesis:** The average duration of power outages caused by severe weather is greater than the average duration of outages caused by equipment failure. 
- **Test Statistic:** Difference of Group Means

#### Results
A permutation test with 10,000 simulations was conducted in order to generate an empirical distribution of the group mean differences under the null hypothesis. 
The resulting **p-value was 0.0001**, which indicates strong evidence against the null hypothesis. At a significance level of α = 0.05, we reject the null hypothesis. There is a statistically significant difference in average outage duration between severe weather and equipment failure outages. The evidence suggests that on average, power outages caused by severe weather tend to last longer than those caused  by equipment failure. 

<iframe
  src="assets/mean_diff_dist.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

---

## Problem Identification

My model will aims to predict the duration of a major power outage, making this a regression problem rather than a classification task. The target variable, `OUTAGE.DURATION.HRS` is continuous and represents the total number of hours an outage lasted.
To evaluate this model, I use both **Root Mean Squared Error(RMSE)** and **R^2** score. RMSE measures the average prediction errors in hours, while R-squared measures how well the model explains the variance in outage duration. 
At prediction time, we assume access to information such as the state, climate region, NERC region, anomaly level, month, hour, total customers affected, and cause category. These features provide a picture of the conditions surrounding each outage.

---
## Baseline Model

The base regression model is a Random Forest Reggressor which uses the features `CLIMATE.REGION`, `CAUSE.CATEGORY`, and `MONTH` in order to predict the length of a power outage.
- `CLIMATE.REGION` (nominal) was selected because it provides geographic context which may reflect weather patterns and regional infrastructure.
- `CAUSE.CATEGORY`(nominal) was included as this was the underlying reason for the power outage, which is likely to affect duration.  
- `MONTH` (ordinal) was chosen to capture seasonal pattern changes which may affect outage duration, such as summer heatwaves or winter snow storms.

To prepare the data for modeling, **One Hot Encoding** was applied to the nominal features (`CLIMATE.REGION` and `CAUSE.CATEGORY`) to convert them into machine-readable format. 

The model achieved an R-squared score of **0.21** and a RMSE of **116.35** hours. This means the model explains about 21% of the variance in outage duration, with an average error of over 115 hours. 

While the model does capture some relationship between the features and the target, the low R-squared score and high RMSE shows that it is not very effective. There is significant room for improvement!
Some features not captured in the current set, such as population, time of day, may provide a more comprehensive "image" of pre-outage zconditions, resulting in better model performance. 

---

## Final Model

For the final model, `HOUR`, `CLIMATE.CATEGORY`, `TOTAL.CUSTOMERS`, were added as features.
- `HOUR` (Ordinal): During EDA, it was found that there was a higher frequency of power outages during certain times of the day, perhaps due to increased demands on the power grid.
- `CLIMATE.CATEGORY`(Nominal): This feature provides information on the presence of El Niño/La Niña events for the current year. 
- `TOTAL.CUSTOMERS` (Quantitative): The total number of customers serviced by the utility in that year.

**GridCVSearch** was performed and the best hyperparameters were found to be:
- max_depth: 20
- max_features: "sqrt"
- min_samples_split: 5
- n_estimators: 100

With these, the model was able to improve performance and achieve an R-squared score of **0.301** and a RMSE of **109.17** hours. Compared to the baseline model, R-squared improved by approximately 43.33% and RMSE lowered  by 6.17%. 


---

## Fairness Analysis
In order to the final model for fairness, two groups were created based on the year recorded for the power outage: `2000-2009` and `2010-2016`. 
"Year" was selected as a metric to create the groups because it was not included as a feature for the model and because it can test if the model generalizes equally well across time. The year 2010 was chosen specifically as there was a spike in the frequency of reports in 2011 as well as the fact that it represents a new decade. The metric used to evaluate fairness will be RMSE. 

<iframe
  src="assets/num_out_per_year.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

- **Null Hypothesis:** There is no difference in the model's performance on the data from 2000-2009 and 2010-2016. Any difference in performance is due to random chance.
- **Alternative Hypothesis:** There is a difference in the model's performance on the data from 2000-2009 and 2010-2016, suggesting that it does not generalize equally over time. 

A permutation test with 10,000 repetitions was performed. The p-value for this test was found to be **0.8981**, significantly higher than the conventional significance level of α = 0.05. Therefore, we fail to reject the null hypothesis which suggests that the model performs equally for both groups.

<iframe
  src="assets/abs_rmse_diff_fairness.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

---
