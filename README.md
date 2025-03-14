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

##  Framing a Prediction Problem

 ### (1) Overview of the Task of Prediction

 Previous studies revealed that about half of all large U.S. power outages are caused by severe storms.  Our query is: **Once an outage begins, can we project the length of time needed to bring back power? ***

 Regression is the type of prediction; the target variable is **outage_duration** (in hours, from `outage.start` to `outage.restoration`.)

 If we can estimate the length, it would enable power providers to arrange their reactions and maybe lower downtime.

 ### (2) Used Features

 We selected these characteristics from the cleaned data, emphasizing on information we would probably know at the beginning of an outage:

 One **year** here.  
 2: **month**  
 3. **environment** based on climate region  (geographic groups derived from National Centers for Environmental Information)  
 4. **anomaly.level** (numeric El Niño/La Niña episode indication)  
 5. **cause.category. Detail**  (particular meteorological occurrence, as a thunderstorm or winter storm)

 We expected these characteristics would be rather important for outage lengths since they would reflect both temporal and meteorological elements.

 ### (3) Workflow of Modeling

 In scikit-learn we built a **Random Forest Regressor** pipeline.  This is a brief synopsis:

 Training and testing data were split 80–20 using an 80–20 train–test split.

 2. Preprocessing; **Categorical Features**  Encoded with `OneHotEncoder()`, so the model may handle `climate.region`, `cause.category.detail`.  
    ** numerical characteristics**  Scaled using ` StandardScaler()` to normalize ranges, (`year`, `month`, `anomaly.level`.

 3. **Random Forest Model** - We heard that random forests can function effectively even without great tuning, hence we used `RandomForestRegressor(n_estimators=100, random_state=42).

 ### (4) Model Accuracy

 Following training and test set predictions, we examined three primary metrics:

 48.35 hours for **MAE (Mean Absolute Error)**; 86.40 hours for **RMSE ( Root Mean Squared Error);** R^2\) Score** = −1.043.  

 The **negative \(R^2\)** value startled us really a bit.  It implies our model performs worse than if we only estimated the average length of outage every time.  Although this is unsatisfactory, it amply illustrates our need for more—or better—features, or perhaps an alternative tuning technique.

 ___

 ## 5. Notes and Likely Motives

 Our dataset might not contain many external elements (e.g., the condition of the electricity system, how fast repair staff could be dispatched, or local geography).  Such missing of such important data can seriously affect performance.

 2. **Great Variability in Outages** -  While some outages—like light thunderstorms—have speedy repairs, others—like major hurricanes or repeated grid failures—take far more time.  A limited collection of characteristics could not be able to adequately depict these significant variations.

 3. **Data Quality** - Managing missing numbers or grouping like-minded weather patterns could call for more deliberate approaches.  Combining terms like "winter," "ice/snow, etc. might assist the model learn and aid to lower noise.

 ### (6) Future orientations

 **Feature Engineering**  
   To minimize overlap, we can mix or refine `cause.category.detail`. Alternatively, we might compile additional data including wind speeds, rainfall totals, or more exact levels of event intensity.

 Models tuning:  
   Tuning random forests—that is, changing `max_depth` or another hyperparameter—is still under study.  We also wish to test approaches like Gradient Boosting or Neural Networks, which might manage complicated interactions better.

 - **Professional Advice**  
   Speaking with utility company engineers or meteorologists could expose important information we are lacking—such as how particular grid designs are more resistant to particular weather patterns.

 ——

 ## Baseline Model

 Here we developed a simpler **baseline regression model** employing just two features—enough to provide a quick benchmark—to forecast outage length.

 ### (1) Features and Their Forms

 One represents which month (1 = January, 2 = February, etc.). **Month (Numeric)**  
    In particular,  Here to keep it straightforward, we applied no scaling or adjustments.

 2. **cause.category. Detail (Categorical)**  
    Contains names like "thunderstorm, "winter storm," "wildfire, etc."  
    treated using **One‐Hot Encoding** as a nominal categorical variable.

 #2: Model Selection and Pipeline

 The baseline was a fundamental **Linear Regression**.  
 Preprocessing: cause.category.detail → OneHotEncoder; month → passed through without modification

 The pipeline therefore is essentially: **OneHotEncode** the category of weather events.  
 2. **Linear Regression** on processed features.

 ### (3) Examining and Training

 We divided things 80–20 exactly.  On the test set following training, we obtained:

 44.71 hours is **MAE**; 59.77 hours is **RMSE**; 0.227 is **R^2**.

 **Interpretation**: We're off by almost 45 hours on average, over two days.  
 Regarding:  These two factors explain just ~2.2% of the variance in outage length for a \(R^2\) = 0.222.

 (4) ###  Is this model "good?"

 **Pros** -  Excellent beginning point since very quick implementation is involved.  Not too much data modification is required.

 The very low R^2 indicates that `month` and `cause.category.detail` by themselves cannot adequately describe much of what is happening.  
   In particular,  The significant mistakes indicate that our estimate of the duration of interruptions is really poor.

 Although this is a **benchmark** that prepares the ground for more intricate models (such as the Random Forest we investigated next), we would not apply this model in a practical environment.

 —–

 ## Final Model

 Then we tried a **final model** above the baseline by:
 1. Including minimum **two new features**  
 2. **Tuning** several hyperparameters  
 3. The same train-test split guarantees fair comparisons.

 ### (1) Engineer features

 The baseline consisted originally in `month` (numeric) and `cause.category.detail`. (categorical).  We now maintained those, but we also implemented cyclical transformations to replace the integer `month`.

 1. **year** (numeric) - Might show changes over time (such as changes in infrastructure or climate).

 2. **Circular Encoding of `month`**  
    Month sin = sin(2π × month / 12).  
    {month_cos = cos(2π × month / 12}  
    In -  A sine/cosine technique can more gently depict seasonal trends than a simple 1–12 as month data is cyclical (after December comes January).

 We also included: - **anomaly.level** (numeric, standardized)  
 **cause. category. detail** (categorical, one-hot)

 Thus, the **final feature set** has, overall: - {year}, {month_sin}, {month_cos}, {anomaly.level}, and {cause.category.detail}.

 ### 2) Hyperparameter Tuning and Model Selection

 Once more, we selected a **Random Forest Regressor** to test if it picks more intricate relationships.  Over: **GridSearchCV** we did.

 n_estimators=[50, 100]  
 The maximum depth is [None, 5, 10].

 improving **neg_mean_squared_error** with 3‐fold cross‐valuation: **Best Parameters**  
 More  Max depth = 5; n_estimators = 100

 #(3) Final Model Performance

 Under those guidelines, we retrained and tested:

 **MAE** = 46.56' hours; **RMSE** = 68.60' hours; **R^2** = −0.288.

 Oddly, the outcome is worse than the baseline (which had MAE ~45, RMSE ~60, \( R^2 \approx 0.222%).  hence the final model's:

 **MAE** rose; **RMSE** also rose; **R^2** turned negative once more, indicating performance even less than a naive estimate.

 ### (4) Talk about and likely causes

 One has **complex but missing factors**.  
    Maybe the cyclical month encoding and the `year` feature ignore primary factors influencing the length of an outage, such as local repair crew sizes or storm exact course.

 2. **Database Restraints**  
    Cross-valuation notwithstanding, the final model might be overfitting in some folds or we might simply lack sufficient relevant data to show actual changes.

 3. **Match Between Real Restitution Times and Seasonal Features**  
    Comparatively to on-the- ground factors like flooding, ice accumulation, or local grid structure, the time of year (month) may not significantly influence how soon electricity lines get fixed.

 ---

 ## Fairness Analysis

 At last, we looked at whether our **final model** handles various sets of outages "fairly".  We specifically asked whether hurricanes cause more mistakes than other kinds of severe storms.  We tested this by **permutation** comparing the **Mean Absolute Errors (MAE)** for two groups in the test set.

 ### (1) Clarifying Two Groups

 Group A: Outages tagged as "hurricanes."  
 **Group B** : Outages spanning all other severe-weather classification.

 ### (2) Standard of Measurement

 We considered the absolute inaccuracy of every test sample since this is still a **regression** issue:

 $$ AE_i = |\hat{y}_i - y_i| $$

 We then calculated the average AE for Groups A and B and derived their difference.

 ### (3)null and alternative hypotheses

 Regarding hurricane vs. non-hurricane outages, the model is "fair," hence average AE between the two groups is not really different.  
 The model is "unfair"—that is, AE for these groups differs **nonzero**.

 ### (4) observed permutation test and difference

 **Group A ( hurricanes)** : 9 exam points  
   Mean AE = 83.06 hours; **Group B**: 103 test points—Non- hurricanes  
   Mean AE approximates 43.37 hours; observed difference is roughly 39.69 hours.

 We then randomly shuffled the "hurricane vs. non-hurricane" labels 10,000 times to find if the variation we noted could occur by coincidence.  The p-value arrived at **0.0342**.

 ### (5) End

 We reject the notion that the model's mistakes are the same for hurricanes and non-hurricanes since p = 0.0342 < 0.05.  Basically, **it's making bigger mistakes** for storm disruptions than for other severe-weather outages—an indicator of a fairness or bias problem.  We might have to modify the model to handle storms differently or incorporate more thorough hurricane-specific data to make the model less so erroneous for those events.

---

## Apendix

### A. Map of U.S Climate Regions

Through climate analysis, National Centers for Environmental Information scientists have identified nine climatically consistent regions within the contiguous United States which are useful for putting current climate anomalies into a historical perspective (Karl and Koss, 1984).

Adapted from [NCEI](https://www.ncei.noaa.gov/access/monitoring/reference-maps/us-climate-regions).

<center><img src="https://raw.githubusercontent.com/Yurea20726/The-Role-of-Climate-in-Major-Power-Outages/main/pictures/fig_a.png" height="400px"></center>
<center><strong>Figure A.</strong> Map of U.S climate regions.</center>
