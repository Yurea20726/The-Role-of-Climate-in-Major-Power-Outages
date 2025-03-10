# The Role of Climate in Major Power Outages

By Chang-Yu Lee & Jianrui Zhang

## Step 1: Introduction

In this project, we want to examine the relationship between climate and massive power outages<sup><small>1</small></sup>.

As shown in Figure 1-1, from January 2001 to July 2016, among the recorded 1,534 major power outages, "severe weather" accounted for nearly 50% of the causes compared to other factors. Therefore, if we can successfully predict the occurrence of severe weather events, both in time and space, that lead to major power outages, and take preventive measures in advance, the frequency of such outages can be significantly reduced.

<center><img src="https://i.imgur.com/51u8Cyo.png" height="400px"></center>
<center><strong>Figure 1-1.</strong> Cause of massive power outage. Copied from Figure 2-1.</center>

The dataset was provided by Laboratory for Advancing Sustainable Critical Infrastructure in Purdue University. You can access the original excel file from [here](https://engineering.purdue.edu/LASCI/research-data/outages). Table 1<sup><small>2</small></sup> shows the description of each variable used in this project.

<center><img src="https://i.imgur.com/2ZRNYV5.png" height="400px"></center>
<center><strong>Figure 1-2.</strong> Part of the original excel file.</center>

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
    <tr><td rowspan="6">Outage Events Information</td><td>Outage.Start.Date</td><td>Day of the year when the outage event started</td><td>Removed</td></tr>
    <tr><td>outage.start.time</td><td>Time of the day when the outage event started</td><td>Removed</td></tr>
    <tr><td>outage.restoration.date</td><td>Day of the year when power was restored</td><td>Removed</td></tr>
    <tr><td>outage.restoration.time</td><td>Time of the day when power was restored</td><td>Removed</td></tr>
    <tr><td>outage.start</td><td>Time when the outage event started</td><td>Added. Combined from <code>outage.restoration.date</code> and <code>outage.start.time</code></td></tr>
    <tr><td>outage.restoration</td><td>Time when power was restored</td><td>Added. Combined from <code>outage.restoration.date</code> and <code>outage.restoration.time</code></td></tr>
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


<iframe src="dataframe/df_2_4.html" width="800" height="600" frameborder="0"></iframe>

<iframe src="pictures/fig_2_1.html" width="800" height="600" frameborder="0"></iframe>
