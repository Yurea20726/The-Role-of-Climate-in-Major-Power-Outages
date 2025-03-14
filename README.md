# The Role of Climate in Major Power Outages

By Chang-Yu Lee & Jianrui Zhang

## Introduction

In this project, we want to examine the relationship between climate and massive power outages<sup><small>1</small></sup>.

As shown in Figure 1-1, from January 2001 to July 2016, among the recorded 1,534 major power outages, "severe weather" accounted for nearly 50% of the causes compared to other factors. Therefore, if we can successfully predict the occurrence of severe weather events, both in time and space, that lead to major power outages, and take preventive measures in advance, the frequency of such outages can be significantly reduced.

<center><img src="https://raw.githubusercontent.com/Yurea20726/The-Role-of-Climate-in-Major-Power-Outages/main/pictures/fig_1_1.png" height="400px"></center>
<center><strong>Figure 1-1.</strong> Cause of massive power outage. Copied from Figure 2-1.</center>

The dataset was provided by Laboratory for Advancing Sustainable Critical Infrastructure in Purdue University. You can access the original excel file from [here](https://engineering.purdue.edu/LASCI/research-data/outages). There're 1,534 records of outage, and 57 variables. Table 1<sup><small>2</small></sup> shows all the variables used in this project.

<center><img src="https://raw.githubusercontent.com/Yurea20726/The-Role-of-Climate-in-Major-Power-Outages/main/pictures/fig_1_2.png" height="400px"></center>
<center><strong>Figure 1-2.</strong> Part of the original excel file.</center>

<br>

<center><table>
  <thead>
    <tr>
      <th>Variable Type</th>
      <th>Variable Name</th>
      <th>Description</th>
      <th>Remark</th>
    </tr>
  </thead>
  <tbody>
    <tr><td rowspan="2">General Information</td><td>year</td><td>Indicates the year when the outage event occurred</td><td></td></tr>
    <tr><td>month</td><td>Indicates the month when the outage event occurred</td><td></td></tr>
    <tr><td rowspan="2">Geographic Areas</td><td>u.s._state</td><td>Represents all the states in the continental U.S.</td><td></td></tr>
    <tr><td>postal.code</td><td>Represents the postal code of the U.S. states</td><td></td></tr>
    <tr><td rowspan="3">Regional Climate Information</td><td>climate.region</td><td>U.S. Climate regions as specified by the National Centers for Environmental Information</td><td></td></tr>
    <tr><td>anomaly.level</td><td>Represents the oceanic El Niño/La Niña (ONI) index referring to cold and warm episodes</td><td></td></tr>
    <tr><td>climate.category</td><td>Climate episodes classified as “Warm”, “Cold”, or “Normal” based on ONI thresholds</td><td></td></tr>
    <tr><td rowspan="6">Outage Events Information</td><td>Outage.Start.Date</td><td>Day of the year when the outage event started</td><td>Removed.</td></tr>
    <tr><td>outage.start.time</td><td>Time of the day when the outage event started</td><td>Removed.</td></tr>
    <tr><td>outage.restoration.date</td><td>Day of the year when power was restored</td><td>Removed.</td></tr>
    <tr><td>outage.restoration.time</td><td>Time of the day when power was restored</td><td>Removed.</td></tr>
    <tr><td>outage.start</td><td>Time when the outage event started</td><td>Combined.</td></tr>
    <tr><td>outage.restoration</td><td>Time when power was restored</td><td>Combined.</td></tr>
    <tr><td rowspan="3">Cause of the Event</td><td>cause.category</td><td>Categories of all the events causing major power outages</td><td></td></tr>
    <tr><td>cause.category.detail</td><td>Detailed description of event categories causing major power outages</td><td></td></tr>
    <tr><td>hurricane.names</td><td>Name of the hurricane if the outage was due to a hurricane</td><td></td></tr>
  </tbody>
</table></center>

<center><strong>Table 1</strong> Variable descriptions.</center>

---

<small>1. As defined by the Department of Energy, the major outages refer to those that impacted atleast 50,000 customers or caused an unplanned firm load loss of atleast 300 MW.</small><br>
<small>2. The table is adapted from this [article](https://www.sciencedirect.com/science/article/pii/S2352340918307182).
</small>

## Data Cleaning

### (1) Download dataset

### (2) Remove description 
As shown in Figure 1-2, the original excel file contains some description, which are not needed for the program, so we remove them here.

### (3) Keep relevant variables & Handle data type

Since there're some missing values in the original excel file, the default `pd.read_excel()` can't detect the data type correctly. We explicitly specify the data type of each column. Note that data type `Int64`, `Float64` can hold `NaN` value.

Combine `outage.{start|restoration}.data` and `outage.{start|restoration}.time` into a new Datetime column `outage.{start|restoration}`, then drop the old those.

Since "Alaska" and "Hawaii" are not in the scope of U.S. climate regions (check appendix A). Plus, we don't have the geometry data for "District of Columbia". We exclude all of them from the dataset.

### (4) At a glimpse: Importance of severe weather

Calculate the proportion of severe weather as the cause of major power outages. Figure 2-1 shows that nearly 50% of them were due to severe weather. Therefore, it's definitely worth a in-depth study.

<iframe src="pictures/fig_2_1.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 2-1.</strong> Cause of massive power outage.</center>

Then, remove the record caused by other reasons for the rest of the project. Here's our cleaned data, contained 750 records left in total.

| obs | year | month | u.s._state | postal.code | climate.region     | anomaly.level | climate.category | outage.start          | outage.restoration    | cause.category  | cause.category.detail | hurricane.names |
|-----|------|-------|------------|-------------|--------------------|---------------|------------------|-----------------------|----------------------|----------------|----------------------|----------------|
| 1   | 2011 | 7     | Minnesota  | MN          | East North Central | -0.3          | normal           | 2011-07-01 17:00:00   | 2011-07-03 20:00:00  | severe weather | NaN                  | NaN            |
| 3   | 2010 | 10    | Minnesota  | MN          | East North Central | -1.5          | cold             | 2010-10-26 20:00:00   | 2010-10-28 22:00:00  | severe weather | heavy wind           | NaN            |
| 4   | 2012 | 6     | Minnesota  | MN          | East North Central | -0.1          | normal           | 2012-06-19 04:30:00   | 2012-06-20 23:00:00  | severe weather | thunderstorm         | NaN            |
| 5   | 2015 | 7     | Minnesota  | MN          | East North Central | 1.2           | warm             | 2015-07-18 02:00:00   | 2015-07-19 07:00:00  | severe weather | NaN                  | NaN            |
| 6   | 2010 | 11    | Minnesota  | MN          | East North Central | -1.4          | cold             | 2010-11-13 15:00:00   | 2010-11-14 22:00:00  | severe weather | winter storm         | NaN            |

### (5) At a glimpse: Missing values

Before we dig further for the exploration, we need to be careful of where're missing values.

| column                     | proportaion |
|----------------------------|---------|
| month                      | 0.005333 |
| anomaly.level              | 0.005333 |
| climate.category           | 0.005333 |
| outage.start               | 0.005333 |
| outage.restoration         | 0.025333 |
| cause.category.detail      | 0.245333 |
| hurricane.names            | 0.905333 |

## Exploratory Data Analysis

### (1) Univariate Analysis

1.`year`: Showing long-term variations. A trend of increase followed by a decrease.

<iframe src="pictures/fig_2_2.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 2-2.</strong> Major power outages caused by severe weather. Grouped by year.</center>

2.`month`: Showing seasonal patterns. A peak in summer (6～8) and a secondary peak in winter (12～2).
<iframe src="pictures/fig_2_3.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 2-3.</strong> Major power outages caused by severe weather. Grouped by month</center>

3.`anomaly.level`: Seem can't get the tread easily. Since anomaly.level itself doesn't have a regular pattern, the frequency of "cold", "normal", "warm" category<sup><small>1</small></sup> is not equal too. Check [here](https://origin.cpc.ncep.noaa.gov/products/analysis_monitoring/ensostuff/ONI_v5.php) or [here](https://www.macromicro.me/series/4848/oni-index-ocean-temperture) for ONI from previous year.

<iframe src="pictures/fig_2_4.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 2-4.</strong> Major power outages caused by severe weather. Grouped by anomaly level</center>

4.`cause.category.detail`: There're many similar causes. Like "winter storm", "winter", "ice/snow", "ice/snow storm". Maybe they should be viewed as a single category to better differntiate different weather phenomena.

<iframe src="pictures/fig_2_5.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 2-5.</strong> Major power outages caused by severe weather. Grouped by cause</center>

5.`u.s._state`: The most severely affected states are "Michigan", "California", and "Texas". Overall, the affected areas are in the west, south through northeast, but barely no in the central part.

<iframe src="pictures/fig_2_6.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 2-6.</strong> Major power outages caused by severe weather. Grouped by state.</center>

6.`climate.region`: Almost the same trend as (5).

<iframe src="pictures/fig_2_7.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 2-7.</strong> Major power outages caused by severe weather. Grouped by climate region</center>

### (2) Bivariate Analysis

1.`year/month` and `anomaly.level`: Showing the trend of ENSO. The data in the dataset is not complete. You may check [here](https://origin.cpc.ncep.noaa.gov/products/analysis_monitoring/ensostuff/ONI_v5.php) or [here](https://www.macromicro.me/series/4848/oni-index-ocean-temperture) for complete data. Note that, comparing with Figure 2-2 (Top-Left), we have 2 very low ONI and high peak of major power events in 2008 and 2011.

<iframe src="pictures/fig_2_8.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 2-8.</strong> ONI over time.</center>

2.`u.s._state` and `cause.category`: We can capture the pattern of each weather phenomena. This presentation better represents the numerical magnitude and integrates multiple reasons. Can be compared with Figure 2-6. We leave the same observation in (3).1 to avoid duplication.

### (3) Interesting Aggregates

1.`cause.category.detail` and `u.s._state`: We can capture the pattern of each weather phenomena. Can be compared with Figure 2-5. This presentation better represents the distribution characteristics of geographic regions.

To better figure out the pattern in a single one, and between each other, we plot 4 maps of 4 differnt weather phenomena simultaneously. Here're some observations.
 - Thunderstorm: Basically whole USA, except central part.
 - Hurricanes: South through northeast.
 - Wildfire: California is the most serious.
 - Winter Storm: This surprised us the most. In our impression, California does not fit the image of a blizzard-prone place, but it is the most serious. Probably, there's other factors, such as topography, even the structural integrity of the facilit, need to be concerned.

<iframe src="pictures/fig_2_10.html" width="850" height="850" frameborder="0"></iframe>
<center><strong>Figure 2-10.</strong> Major power outages caused by severe weather. Grouped by state and cause. <br>(Top-Left) Thunderstorm (Top-Right) Hurricanes <br>(Bottom-Left) Wildfire (Bottom-Right) Winter Storm</center>

2.`year` and `month`: We try to find pattern in season overall, and in each region.
  - Overall: Seem no signigicant pattern.
  - Northwest: Concentrated in the winter.
  - West: Pretty averagely spread?
  - South: Concentrated in the summer.

<iframe src="pictures/fig_2_11.html" width="850" height="850" frameborder="0"></iframe>
<center><strong>Figure 2-11.</strong> Major power outages caused by severe weather. Grouped by month and year in different region.</center>

---

<small>1. The categories—“Warm”, “Cold” or “Normal” episodes of the climate are based on a threshold of ± 0.5 °C for the Oceanic Niño Index (ONI).</small>

## Assessment of Missingness

### (1) NMAR Analysis

To better understand the reason of missingness. We use data before filtering out those state in Alaska, Hawaii, District of Columbia, and cause is not severe weather. How are missing values distributed in the dataset?

|       | column                  | count | propotyion | obs                                      |
|-------|-------------------------|-------|---------|------------------------------------------|
| 0     | month                   | 9     | 0.005867 | [240, 340, 366, 767, 888, 1319, 1507, 1531, 1534] |
| 1     | climate.region          | 6     | 0.003911 | [1516, 1517, 1518, 1519, 1520, 1534] |
| 2     | anomaly.level           | 9     | 0.005867 | [240, 340, 366, 767, 888, 1319, 1507, 1531, 1534] |
| 3     | climate.category        | 9     | 0.005867 | [240, 340, 366, 767, 888, 1319, 1507, 1531, 1534] |
| 4     | outage.start            | 9     | 0.005867 | [240, 340, 366, 767, 888, 1319, 1507, 1531, 1534] |
| 5     | outage.restoration      | 58    | 0.037810 | [23, 37, 48, 50, 183, 193, 233, 240, 283, 302, ...] |
| 6     | cause.category.detail   | 471   | 0.307040 | [1, 5, 19, 20, 24, 27, 28, 30, 32, 36, 41, 45, ...] |
| 7     | hurricane.names         | 1462  | 0.953064 | [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, ...] |

Some observations we get:
1. `month`, `anomaly.level`, `climate.category` and `outage.start` are missing simultaneously.
2. (1) is a subset of `outage.restoration`.
3. state of record `obs` 1516～1520 are "Hawaii", 1534 is "Alaska".
4. `hurricane.names` is not empty if and only if `cause.category.detail` is "hurricanes".

Some trivial conclusion we can made directly:
1. `month` is **MAR**: Depends on `outage.start`.
2. `climate.region` is **MD**: Since "Hawaii" and "Alaska" are not included in the classification, it's of course `NA`. Check appendix A for climate region.
3. `anomaly.level` is **MAR**: Depends on `year` and `month`.
4. `climate.category` is **MAR**: Depends on `anomaly.level`. Note that the categories "Warm", "Cold" or "Normal" are based on a threshold ±0.5℃ for ONI value (=`anomaly.level`).
5. `hurricane.names` is **MD**: Since if `cause.category.detail` is not "hurricanes", it's of course `NA`.

Among others, we believe that `cause.category.detail` is the mostly likely NMAR. There're several possible reasons,
- Sensitive Cause: The reason of outages caused by intentional attacks, internal failures, etc, might be hidden to protect company's reputation.
- Unidentified Cause: The scenario is too complex to determine the cause or that the cause cannot be determined after investigation.

If we can accquire the additional data about the precise location of outages. We might be able to conclude that
- Some region, utility provider are more likely to have missing values.
- Maybe rural area has higher probability to have missing values than urban area.

### (2) Missingness Dependency

We test `outage.restoration` against `year` and `month`.

1.`outage.restoration` versus `year`
  - **Null Hypothesis**: The distribution of `year` is the same when `outage.restoration` is missing or not.
  - **Alternate Hypothesis**: The distribution of `year` are different when `outage.restoration` is missing or not.

<iframe src="pictures/fig_3_1.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 3-1.</strong> Year of missingness of outage.restoration.</center>

The observed TVD value is 0.6602. We run 10000 iterations of permutation test, result in the distribution as Figure 3.2 shown, with a p-value of 0.0. Therefore, we reject the null hypothesis in favor of the alternate hypothesis. Indicating that the missingness of `outage.restoration` is dependent on `year`.

<iframe src="pictures/fig_3_2.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 3-2.</strong> Distribution of simulated TVD value.</center>

2.`outage.restoration` versus `month`
  - **Null Hypothesis**: The distribution of `month` is the same when `outage.restoration` is missing or not.
  - **Alternate Hypothesis**: The distribution of `month` are different when `outage.restoration` is missing or not.

<iframe src="pictures/fig_3_3.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 3-3.</strong> Year of missingness of outage.restoration.</center>

The observed TVD value is 0.2227. We run 10000 iterations of permutation test, result in the distribution as Figure 3.4 shown, with a p-value of 0.1216. Therefore, we fail to reject the null hypothesis. Indicating that the missingness of `outage.restoration` is not dependent on `year`.

<iframe src="pictures/fig_3_4.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 3-4.</strong> Distribution of simulated TVD value.</center>

## Hypothesis Testing

We test whether the distribution of outages over month within the time range of the dataset is the same between different climate regions with the following framework.

Given 2 climate regions $A$ and $B$
- **Null Hypothesis**: Under the above assumption, the distribution of $A$ and $B$ are **the same**.
- **Alternate Hypothesis**: Under the above assumption, the distribution of $A$ and $B$ are **different**.
- **Test Statistic**: TVD of each month between $A$ and $B$
- **Significance Level**: 0.001
- **Iterations of Simulation**: 10000

Here's the result of some combinations.

| Pair | p-value |
| :--: | :--: |
| (West, Southeast) | 0.0002 |
| (West, Northeast) | 0.0467 |
| (South, Northeast) | 0.0002 |

<iframe src="pictures/fig_4_1.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 4-1.</strong> Major power outages caused by severe weather. Distribution of different regions over month.</center>

<iframe src="pictures/fig_4_2.html" width="850" height="420" frameborder="0"></iframe>
<center><strong>Figure 4-2.</strong> Distribution of simulated TVD value.</center>

Below is the report with **all first-person singular references changed to first-person plural**, reflecting a group effort.

---

## Framing a Prediction Problem

### (1) Overview of the Prediction Task

Earlier analyses showed that severe weather causes roughly half of all major power outages in the U.S. Our question is:  
**Once an outage starts, can we estimate how long it will take to restore power?**

- **Prediction Type**: Regression  
- **Target Variable**: `outage_duration` (in hours, from `outage.start` to `outage.restoration`)

If we can guess the duration, it might help utility companies plan their responses and possibly reduce downtime.

### (2) Features Used

We picked these features from the cleaned dataset, focusing on data we would likely know at the start of an outage:

1. **year**  
2. **month**  
3. **climate.region** (regional categories from the National Centers for Environmental Information)  
4. **anomaly.level** (numeric indicator for El Niño/La Niña episodes)  
5. **cause.category.detail** (specific weather event, e.g., thunderstorm, winter storm)

We thought these features would capture both temporal and weather aspects, which we assumed matter a lot for outage durations.

### (3) Modeling Workflow

We created a **Random Forest Regressor** pipeline in scikit‐learn. Here’s a quick summary:

1. **Data Split**  
   - We used an 80–20 train–test split to separate training and testing data.

2. **Preprocessing**  
   - **Categorical Features** (`climate.region`, `cause.category.detail`): Encoded with `OneHotEncoder()` so the model can handle them.  
   - **Numeric Features** (`year`, `month`, `anomaly.level`): Scaled by `StandardScaler()` to normalize ranges.

3. **Random Forest Model**  
   - We used `RandomForestRegressor(n_estimators=100, random_state=42)` because we read that random forests can work well even without excessive tuning.

### (4) Model Performance

After training and predicting on the test set, we looked at three main metrics:

- **MAE (Mean Absolute Error)** = 48.35 hours  
- **RMSE (Root Mean Squared Error)** = 86.40 hours  
- **\(R^2\) Score** = −1.043  

We were quite surprised by the **negative \(R^2\)** value. It suggests our model does worse than if we just guessed the average outage duration each time. This is disappointing, but it clearly shows we need more (or better) features, or maybe a different approach to tuning.

---

## 5. Observations and Possible Reasons

1. **Limited Feature Information**  
   - There could be many external factors (e.g., the state of the power grid, how quickly repair crews can be sent out, or local geography) that aren’t in our dataset. Missing such critical info can really hurt performance.

2. **High Variability in Outages**  
   - Some outages might be quick fixes (like minor thunderstorms), while others (like large hurricanes or multiple grid failures) take way longer. A small set of features might not capture these huge differences.

3. **Data Quality**  
   - Handling missing values or grouping similar weather categories might need more careful strategies. Combining categories like “winter,” “ice/snow,” etc. might reduce noise and help the model learn.

### (6) Future Directions

- **Feature Engineering**  
  We might combine or refine `cause.category.detail` to reduce overlap, and maybe gather extra data like wind speeds, rainfall amounts, or more detailed event severity levels.

- **Model Tuning**  
  We are still learning about tuning random forests (e.g., adjusting `max_depth` or other hyperparameters). We also want to try methods like Gradient Boosting or Neural Networks, which might handle complex relationships better.

- **Expert Insights**  
  Consulting utility company engineers or meteorologists might reveal key factors we’re missing—like how certain grid designs are more resilient to specific weather patterns.

---

## Baseline Model

Here, we built a simpler **baseline regression model** to predict outage duration using just two features—enough to get a quick benchmark.

### (1) Features and Their Types

1. **month (Numeric)**  
   - Represents which month (1 = January, 2 = February, etc.).  
   - We didn’t apply any scaling or transformations here to keep it simple.

2. **cause.category.detail (Categorical)**  
   - Includes labels like “thunderstorm,” “winter storm,” “wildfire,” etc.  
   - Treated as a nominal categorical variable via **One‐Hot Encoding**.

### (2) Model Choice and Pipeline

- **Model**: A basic **Linear Regression** served as the baseline.  
- **Preprocessing**:  
  - `cause.category.detail` → OneHotEncoder  
  - `month` → Passed through without changes

So, the pipeline is basically:
1. **OneHotEncode** the weather event category.  
2. **LinearRegression** on the processed features.

### (3) Training and Evaluation

We used the same 80–20 split. After training, we got the following metrics on the test set:

- **MAE** = 44.71 hours  
- **RMSE** = 59.77 hours  
- **R^2** = 0.022

**Interpretation**:
- On average, we’re off by about 45 hours, which is nearly two days.  
- An \(R^2\) of 0.022 means only ~2.2% of the variation in outage duration is explained by these two features.

### (4) Is This Model “Good”?

- **Pros**  
  - Very quick to implement, so it’s a nice starting point.  
  - Hardly any data manipulation is needed.

- **Cons**  
  - The R^2 is very low, suggesting `month` and `cause.category.detail` alone don’t explain much of what’s happening.  
  - The large errors mean we’re not very accurate at predicting how long outages last.

We wouldn’t use this model in a real setting, but it’s a **benchmark** that sets the stage for more complex models (like the Random Forest we tried next).

---

## Final Model

Next, we attempted a **final model** that goes beyond the baseline by:
1. Adding at least **two new features**  
2. **Tuning** certain hyperparameters  
3. Using the same train–test split so comparisons are fair

### (1) Feature Engineering

Originally, we had `month` (numeric) and `cause.category.detail` (categorical) in the baseline. Now we kept those, but we replaced the integer `month` with cyclical transformations and also added:

1. **year** (numeric)  
   - Might capture trends over time (e.g., infrastructure improvements or climate shifts).

2. **Cyclical Encoding of `month`**  
   - `month_sin = sin(2π × month / 12)`  
   - `month_cos = cos(2π × month / 12)`  
   - Because month data is cyclical (after December comes January), a sine/cosine approach can represent seasonal patterns more smoothly than just 1–12.

We also included:
- **anomaly.level** (numeric, standardized)  
- **cause.category.detail** (categorical, one‐hot)

So in total, the **final feature set** has:
- `year`, `month_sin`, `month_cos`, `anomaly.level`, and `cause.category.detail`.

### (2) Model Selection and Hyperparameter Tuning

We chose a **Random Forest Regressor** again to see if it captures more complex relations. We did a **GridSearchCV** over:

- `n_estimators`: [50, 100]  
- `max_depth`: [None, 5, 10]

with 3‐fold cross‐validation, optimizing **neg_mean_squared_error**.  
> **Best Parameters**:  
> - `max_depth = 5`  
> - `n_estimators = 100`

### (3) Final Model Performance

With those parameters, we retrained and tested:

- **MAE** = 46.56 hours  
- **RMSE** = 68.60 hours  
- **R^2** = −0.288

Surprisingly, the result is worse than the baseline (which had MAE ~45, RMSE ~60, \( R^2 \approx 0.022\)). So the final model’s:

- **MAE** got higher,  
- **RMSE** also went up,  
- **R^2** became negative again, meaning performance is even lower than a naive guess.

### (4) Discussion and Possible Reasons

1. **Complex but Missing Factors**  
   Possibly, the cyclical month encoding and the `year` feature don’t address main drivers of outage duration, like local repair crew sizes or the exact path of a storm.

2. **Data Limitations**  
   Even with cross‐validation, the final model might be overfitting in some folds, or we may simply lack enough relevant data to see real improvements.

3. **Mismatch Between Seasonal Features and Real Restoration Times**  
   The time of year (month) might not strongly affect how quickly power lines get fixed, compared to on‐the‐ground conditions like flooding, ice accumulation, or local grid structure.

---

## Fairness Analysis

Finally, we investigated if our **final model** treats different groups of outages “fairly.” Specifically, we wondered if it makes larger errors for hurricanes than for other types of severe weather. To test this, we performed a **permutation test** comparing the **Mean Absolute Errors (MAE)** for two groups in the test set.

### (1) Defining Two Groups

- **Group A**: Outages labeled as “hurricanes.”  
- **Group B**: Outages from all other severe‐weather categories.

### (2) Evaluation Metric

This is still a **regression** problem, so we looked at each test example’s absolute error:

$$
AE_i = |\hat{y}_i - y_i|
$$

Then we computed the average AE for Group A and Group B and took their difference.

### (3) Null & Alternative Hypotheses

- **\(H_0\)**: The model is “fair” regarding hurricane vs. non‐hurricane outages—meaning there is no real difference in average AE between the two groups.  
- **\(H_1\)**: The model is “unfair”—there is a **non‐zero** difference in AE for these groups.

### (4) Observed Difference & Permutation Test

- **Group A (Hurricanes)**: 9 test points  
  - Mean AE ≈ 83.06 hours  
- **Group B (Non‐Hurricanes)**: 103 test points  
  - Mean AE ≈ 43.37 hours  
- **Observed Difference**: about 39.69 hours

We then shuffled the “hurricane vs. non‐hurricane” labels 10,000 times to see if the difference we observed could happen by random chance. The **p‐value** came out to **0.0342**.

### (5) Conclusion

Since p = 0.0342 < 0.05, we reject the idea that the model’s errors are the same for hurricanes and non‐hurricanes. Essentially, **it’s making bigger mistakes** for hurricane outages than for other severe‐weather outages—an indication of a fairness or bias issue. We might need to add more detailed hurricane‐specific data or adjust the model to handle hurricanes differently so that it isn’t so inaccurate for those events.

---

## Apendix

### A. Map of U.S Climate Regions

Through climate analysis, National Centers for Environmental Information scientists have identified nine climatically consistent regions within the contiguous United States which are useful for putting current climate anomalies into a historical perspective (Karl and Koss, 1984).

Adapted from [NCEI](https://www.ncei.noaa.gov/access/monitoring/reference-maps/us-climate-regions).

<center><img src="https://raw.githubusercontent.com/Yurea20726/The-Role-of-Climate-in-Major-Power-Outages/main/pictures/fig_a.png" height="400px"></center>
<center><strong>Figure A.</strong> Map of U.S climate regions.</center>
