---
title: "Analysis of the Glottic Cancer data"
author: "Hong Cao"
date: "2024-05-19"
output:
  html_document:
    df_print: paged
    toc: yes
    toc_depth: 2
    toc_float: yes
    number_sections: no
    fig_height: 5
    fig_width: 7
    keep_md: true
    theme: cerulean
  pdf_document:
    fig_height: 3.5
    fig_width: 3.5
  word_document:
    toc: no
---

<style type="text/css">
<style>
.tabset .nav-tabs a {
  background-color: red; /* Replace with the actual color code */
  color: white; /* Text color */
}
<style>
</style>
# Abstract

**Glottic cancer** is a malignancy located in the larynx, specifically affecting the true vocal cords and the anterior and posterior commissures. Due to its anatomical location, it can profoundly impact essential life functions such as breathing, swallowing, and phonation, potentially leading to mortality.

In analyzing survival data for this condition, **Kaplan-Meier estimates** were calculated for each predictor to delineate survival probabilities. **Log-rank tests and Kaplan-Meier plots** were performed to evaluate the bivariate relationships between each potential predictor variable and survival status, offering a combined visual and statistical assessment of survival differences across groups.  Additionally, the overall survival rate was analyzed using a multivariate **Cox proportional hazards regression model**, which included five predictor variables: sex, age at diagnosis, race, tumor staging, and treatment status.

The resulte indicates that patients over 65 years old have more than double the risk of mortality than those under 65, and this is statistically significant. Patients receiving both surgery and radiation have a 64% lower risk of death compared to those with no treatment, a highly significant finding. Patients with cancer stage T2 have an 88% higher risk of mortality compared to those with stage T1a, marking a significant escalation in risk with advancing cancer stage. Additionally, individuals with cancer stage T1b exhibit a 54% increased risk of mortality compared to the T1a baseline, highlighting the importance of cancer stage in survival outcomes. Overall, **age，  treatment status and cancer stage** are major determinants of patient survival outcomes in this study.

**Keywords**: Glottic Cancer, Cox Proportional Hazards Model, Treatment Status, Cancer Staging, Age 

# 1. Data Preprocessing And Simple Descriptive of Variables {.tabset}

## Data Definitions

**Derived AJCC** : American Joint Committee on Cancer staging classification system
T1a and T1b: The tumor is only in the vocal cords with normal mobility. T1a refers to cancer involvement of only one cord. T1b refers to cancer involvement of both cords
 ; T2: The tumor is large enough that it goes to the supraglottis and/or subglottis, and/or there is decreased movement of the vocal cord
 ; T1NOS:  T1 tumor not otherwise specified.


**Sex**: 
 Female 
 ; Male


**Age_dx**: Age at diagnosis of glottic cancer


**Race**:
 White
 ; Black
 ; Asian or Pacific Islander
 ; American Indian/Alaska Native
 ; Other unspecified (1991+)
 ; NA(Unknown)

**Rad_Tx_Status**:
*Radiation/Surgery Treatment status*: 
 0 = Surgery only
 ; 1 = Radiation only
 ; 2 = Both surgery and radiation
 ; 3 = Neither surgery nor radiation
 ; NA = Unknown 

**Death_Status**:
 0 = Alive
 ; 1 = Dead

**Survival_mos**: Survival time in months from diagnosis to death or last follow-up (if alive).





## Data Preprocessing
Following data importation, we undertake a series of preprocessing steps to refine the dataset for analysis:

**Removing Missing Data**: We start by excluding records with missing values in the Age_dx variable. We only have one NA in Age_dx, in order to build Age_Group in the following step, we drop the only NA in Age_dx.

**Standardizing Categories**: The variables Race and Rad_Tx_Status are standardized by converting entries labeled as "unknown" and "U" to NA, clearly marking missing information. This step is crucial for maintaining data integrity and facilitating accurate categorization.(Here we do not delete NA directly because it is unreasonable)

**Recoding Death Status**: The Death_Status variable is recoded to create a binary indicator where '0' represents alive and '1' denotes deceased from any cause. This recoding includes transforming -1 and 1 values to 1, streamlining the variable for survival analysis.

**Treatment Classification**: A new variable, Treatment, is derived from Rad_Tx_Status to categorize the type of treatment patients received into four groups: 'Surgery', 'Radiation', 'Both', and 'Neither'. This classification allows for detailed investigation into treatment outcomes.

**Age Grouping**: The continuous Age_dx variable is categorized into two age groups by median（65）: below 65 and 65 or above, using the cut function. This grouping facilitates the analysis of age-related trends in the data.

**Race combination**: Racial categories other than white and black have small sample sizes, and analysis alone may lack statistical significance due to insufficient sample sizes. Therefore, we combine them in to "Other".

## Categorical Variables
Then, depending on the type of predictor, we categorize them into numerical and categorical. By type predictor We looked at the frequency of different Death_Status in different categories. Numerical variables we looked at the median, the mode, the quantile.

```{=html}
<div id="vicyipspzo" style="padding-left:0px;padding-right:0px;padding-top:10px;padding-bottom:10px;overflow-x:auto;overflow-y:auto;width:auto;height:auto;">
<style>#vicyipspzo table {
  font-family: system-ui, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#vicyipspzo thead, #vicyipspzo tbody, #vicyipspzo tfoot, #vicyipspzo tr, #vicyipspzo td, #vicyipspzo th {
  border-style: none;
}

#vicyipspzo p {
  margin: 0;
  padding: 0;
}

#vicyipspzo .gt_table {
  display: table;
  border-collapse: collapse;
  line-height: normal;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 16px;
  font-weight: normal;
  font-style: normal;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #A8A8A8;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#vicyipspzo .gt_caption {
  padding-top: 4px;
  padding-bottom: 4px;
}

#vicyipspzo .gt_title {
  color: #333333;
  font-size: 125%;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#vicyipspzo .gt_subtitle {
  color: #333333;
  font-size: 85%;
  font-weight: initial;
  padding-top: 3px;
  padding-bottom: 5px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#vicyipspzo .gt_heading {
  background-color: #FFFFFF;
  text-align: center;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#vicyipspzo .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#vicyipspzo .gt_col_headings {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#vicyipspzo .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#vicyipspzo .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#vicyipspzo .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#vicyipspzo .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#vicyipspzo .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#vicyipspzo .gt_spanner_row {
  border-bottom-style: hidden;
}

#vicyipspzo .gt_group_heading {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  text-align: left;
}

#vicyipspzo .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#vicyipspzo .gt_from_md > :first-child {
  margin-top: 0;
}

#vicyipspzo .gt_from_md > :last-child {
  margin-bottom: 0;
}

#vicyipspzo .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#vicyipspzo .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
}

#vicyipspzo .gt_stub_row_group {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: solid;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 5px;
  padding-right: 5px;
  vertical-align: top;
}

#vicyipspzo .gt_row_group_first td {
  border-top-width: 2px;
}

#vicyipspzo .gt_row_group_first th {
  border-top-width: 2px;
}

#vicyipspzo .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#vicyipspzo .gt_first_summary_row {
  border-top-style: solid;
  border-top-color: #D3D3D3;
}

#vicyipspzo .gt_first_summary_row.thick {
  border-top-width: 2px;
}

#vicyipspzo .gt_last_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#vicyipspzo .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#vicyipspzo .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: double;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#vicyipspzo .gt_last_grand_summary_row_top {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-bottom-style: double;
  border-bottom-width: 6px;
  border-bottom-color: #D3D3D3;
}

#vicyipspzo .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#vicyipspzo .gt_table_body {
  border-top-style: solid;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
}

#vicyipspzo .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#vicyipspzo .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#vicyipspzo .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#vicyipspzo .gt_sourcenote {
  font-size: 90%;
  padding-top: 4px;
  padding-bottom: 4px;
  padding-left: 5px;
  padding-right: 5px;
}

#vicyipspzo .gt_left {
  text-align: left;
}

#vicyipspzo .gt_center {
  text-align: center;
}

#vicyipspzo .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#vicyipspzo .gt_font_normal {
  font-weight: normal;
}

#vicyipspzo .gt_font_bold {
  font-weight: bold;
}

#vicyipspzo .gt_font_italic {
  font-style: italic;
}

#vicyipspzo .gt_super {
  font-size: 65%;
}

#vicyipspzo .gt_footnote_marks {
  font-size: 75%;
  vertical-align: 0.4em;
  position: initial;
}

#vicyipspzo .gt_asterisk {
  font-size: 100%;
  vertical-align: 0;
}

#vicyipspzo .gt_indent_1 {
  text-indent: 5px;
}

#vicyipspzo .gt_indent_2 {
  text-indent: 10px;
}

#vicyipspzo .gt_indent_3 {
  text-indent: 15px;
}

#vicyipspzo .gt_indent_4 {
  text-indent: 20px;
}

#vicyipspzo .gt_indent_5 {
  text-indent: 25px;
}

#vicyipspzo .katex-display {
  display: inline-flex !important;
  margin-bottom: 0.75em !important;
}

#vicyipspzo div.Reactable > div.rt-table > div.rt-thead > div.rt-tr.rt-tr-group-header > div.rt-th-group:after {
  height: 0px !important;
}
</style>
<table class="gt_table" data-quarto-disable-processing="false" data-quarto-bootstrap="false">
  <caption><span class='gt_from_md'><strong>Frequency Table of Categorical</strong></span></caption>
  <thead>
    <tr class="gt_col_headings gt_spanner_row">
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="2" colspan="1" scope="col" id="label"><span class='gt_from_md'><strong>Variable</strong></span></th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="2" scope="colgroup" id="level 1; stat_1">
        <div class="gt_column_spanner"><span class='gt_from_md'><strong>Death Status</strong></span></div>
      </th>
    </tr>
    <tr class="gt_col_headings">
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_1"><span class='gt_from_md'><strong>0</strong><br />
N = 3,774</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1" scope="col" id="stat_2"><span class='gt_from_md'><strong>1</strong><br />
N = 647</span><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td headers="label" class="gt_row gt_left" style="font-weight: bold;">Cancer Stage</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    T1a</td>
<td headers="stat_1" class="gt_row gt_center">1,856 (49%)</td>
<td headers="stat_2" class="gt_row gt_center">248 (38%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    T1b</td>
<td headers="stat_1" class="gt_row gt_center">328 (8.7%)</td>
<td headers="stat_2" class="gt_row gt_center">71 (11%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    T1NOS</td>
<td headers="stat_1" class="gt_row gt_center">735 (19%)</td>
<td headers="stat_2" class="gt_row gt_center">99 (15%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    T2</td>
<td headers="stat_1" class="gt_row gt_center">855 (23%)</td>
<td headers="stat_2" class="gt_row gt_center">229 (35%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left" style="font-weight: bold;">Sex</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Female</td>
<td headers="stat_1" class="gt_row gt_center">432 (11%)</td>
<td headers="stat_2" class="gt_row gt_center">69 (11%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Male</td>
<td headers="stat_1" class="gt_row gt_center">3,342 (89%)</td>
<td headers="stat_2" class="gt_row gt_center">578 (89%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left" style="font-weight: bold;">Race</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Black</td>
<td headers="stat_1" class="gt_row gt_center">420 (11%)</td>
<td headers="stat_2" class="gt_row gt_center">78 (12%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Other</td>
<td headers="stat_1" class="gt_row gt_center">188 (5.0%)</td>
<td headers="stat_2" class="gt_row gt_center">21 (3.2%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    White</td>
<td headers="stat_1" class="gt_row gt_center">3,166 (84%)</td>
<td headers="stat_2" class="gt_row gt_center">548 (85%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left" style="font-weight: bold;">Treatment</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Surgery</td>
<td headers="stat_1" class="gt_row gt_center">553 (15%)</td>
<td headers="stat_2" class="gt_row gt_center">69 (11%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Radiation</td>
<td headers="stat_1" class="gt_row gt_center">1,907 (52%)</td>
<td headers="stat_2" class="gt_row gt_center">340 (55%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Both</td>
<td headers="stat_1" class="gt_row gt_center">1,061 (29%)</td>
<td headers="stat_2" class="gt_row gt_center">152 (24%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Neither</td>
<td headers="stat_1" class="gt_row gt_center">173 (4.7%)</td>
<td headers="stat_2" class="gt_row gt_center">60 (9.7%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    Unknown</td>
<td headers="stat_1" class="gt_row gt_center">80</td>
<td headers="stat_2" class="gt_row gt_center">26</td></tr>
    <tr><td headers="label" class="gt_row gt_left" style="font-weight: bold;">Age Group</td>
<td headers="stat_1" class="gt_row gt_center"><br /></td>
<td headers="stat_2" class="gt_row gt_center"><br /></td></tr>
    <tr><td headers="label" class="gt_row gt_left">    &lt;65</td>
<td headers="stat_1" class="gt_row gt_center">1,930 (51%)</td>
<td headers="stat_2" class="gt_row gt_center">213 (33%)</td></tr>
    <tr><td headers="label" class="gt_row gt_left">    &gt;65</td>
<td headers="stat_1" class="gt_row gt_center">1,844 (49%)</td>
<td headers="stat_2" class="gt_row gt_center">434 (67%)</td></tr>
  </tbody>
  
  <tfoot class="gt_footnotes">
    <tr>
      <td class="gt_footnote" colspan="3"><span class="gt_footnote_marks" style="white-space:nowrap;font-style:italic;font-weight:normal;line-height:0;"><sup>1</sup></span> <span class='gt_from_md'>n (%)</span></td>
    </tr>
  </tfoot>
</table>
</div>
```

## Numerical Variables
<table class="table table-striped table-hover table-condensed table-responsive" style="margin-left: auto; margin-right: auto;">
<caption>Summary Statistics for Numerical Variables</caption>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;">     Age_dx </th>
   <th style="text-align:left;">  Survival_mos </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Min.   : 16.00 </td>
   <td style="text-align:left;"> Min.   : 0.10 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 1st Qu.: 57.00 </td>
   <td style="text-align:left;"> 1st Qu.:12.00 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Median : 65.00 </td>
   <td style="text-align:left;"> Median :27.00 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Mean   : 64.91 </td>
   <td style="text-align:left;"> Mean   :29.81 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> 3rd Qu.: 73.00 </td>
   <td style="text-align:left;"> 3rd Qu.:46.00 </td>
  </tr>
  <tr>
   <td style="text-align:left;">  </td>
   <td style="text-align:left;"> Max.   :106.00 </td>
   <td style="text-align:left;"> Max.   :71.00 </td>
  </tr>
</tbody>
</table>

## Brief Summary

**For Categorical Variable**
The distribution of tumor stages indicates a higher frequency of diagnosis early in the dataset (T1a). The gender distribution indicates that men are disproportionately represented in the data. The racial distribution shows that most of the data set is made up of white people, with smaller percentages of other races. The status of radiation therapy indicates that most patients receive some level of radiation therapy, none at all or less at the highest level.

**For Numerical Variable**
The age distribution indicates that the study population predominantly consists of older adults, with a significant spread in age, reflecting a diverse study population in terms of age. The survival time variable indicates variability in survival post-diagnosis, with a significant proportion of participants surviving beyond the median survival time. These statistics are crucial for understanding the survival trends and mortality rates in the context of the disease under study.

# 2. Kaplan-Meier estimates for predictors {.tabset}
We will use survfit() to generate overall K-M estimates and estimates across Treatment:

##  For All
**Analysis of all patients**: 

- Kaplan-Meier survival analysis involved 4,313 participants and recorded 621 events, most of whom were still alive at the time node, so the median survival cannot be estimated, and the confidence interval is similarly shown as "NA".

- This indicates a low incidence of events relative to the cohort size during the study follow-up period, indicating that most subjects are still alive.Therefore, more extensive follow-up or a larger sample of events is needed for precise median survival estimates.

``` r
# For all (overall K-M estimates)
surv_all <- survfit(Surv(time = gc_data_updated$Survival_mos, 
                         event = gc_data_updated$Death_Status) ~ 1, 
                    data = gc_data_updated)
surv_all
```

```
## Call: survfit(formula = Surv(time = gc_data_updated$Survival_mos, event = gc_data_updated$Death_Status) ~ 
##     1, data = gc_data_updated)
## 
##         n events median 0.95LCL 0.95UCL
## [1,] 4421    647     NA      NA      NA
```

## By Treatment Status

**Analysis By radiation or surgery Treatment status**: 

- The Radiation Only group, being the largest, reports the highest number of events, suggesting that this group might have different characteristics or baseline risks compared to others.
   
- The Surgery Only and Neither groups, despite their smaller sizes, show fewer events, potentially indicating better survival outcomes or lower baseline risks.
   
- The group receiving Both treatments has a considerable number of events despite a moderate group size. This might suggest that patients receiving both treatments had more advanced or aggressive disease at baseline, necessitating a more intensive treatment approach, or that the combination of treatments does not significantly improve survival outcomes over other treatment modalities.
   
- Also, since there is no median and 95% confidence limit, we can only roughly analyze according to the event rate:**There's  difference in survival between the treatments**

``` r
surv_by_trt <- survfit(Surv(time = Survival_mos, 
                     event = Death_Status == '1') ~ Treatment, 
                     data = gc_data_updated)
surv_by_trt
```

```
## Call: survfit(formula = Surv(time = Survival_mos, event = Death_Status == 
##     "1") ~ Treatment, data = gc_data_updated)
## 
##    因为不存在，106个观察量被删除了 
##                        n events median 0.95LCL 0.95UCL
## Treatment=Surgery    622     69     NA      NA      NA
## Treatment=Radiation 2247    340     NA      NA      NA
## Treatment=Both      1213    152     NA      NA      NA
## Treatment=Neither    233     60     NA      NA      NA
```
## By Cancer Staging
**Analysis By cancer staging**: 

- T1a Stage: Represents the largest subgroup with 2,104 patients, among which there were 248 events. This stage typically denotes an early-stage tumor that is less likely to have spread, which might suggest a relatively better prognosis compared to more advanced stages.
 
- T1b Stage: Comprises 399 patients with 71 events observed. Although smaller in size, the proportion of events suggests a survival outcome that warrants closer examination against T1a.
  
- T1NOS (Not Otherwise Specified): Includes 834 patients with 99 events. The 'NOS' designation indicates tumors that haven't been fully classified within the T1 staging, possibly due to varying factors or insufficient data.
  
- T2 Stage: Contains 1,084 patients, experiencing 229 events. As a more advanced stage than T1, T2 denotes larger tumor size or greater invasion, which typically correlates with a higher risk and possibly poorer survival outcomes.
 
- Also, since there is no median and 95% confidence limit, we can only roughly analyze according to the event rate:**There's  difference in survival between the cancer staging**

``` r
surv_by_ajcc <- survfit(Surv(time = Survival_mos, 
                     event = Death_Status == '1') ~ Derived_AJCC, 
                     data = gc_data_updated)
surv_by_ajcc
```

```
## Call: survfit(formula = Surv(time = Survival_mos, event = Death_Status == 
##     "1") ~ Derived_AJCC, data = gc_data_updated)
## 
##                       n events median 0.95LCL 0.95UCL
## Derived_AJCC=T1a   2104    248     NA      NA      NA
## Derived_AJCC=T1b    399     71     NA      NA      NA
## Derived_AJCC=T1NOS  834     99     NA      NA      NA
## Derived_AJCC=T2    1084    229     NA      NA      NA
```


## By Sex
**Analysis By sex**: 

 - Sex=Female: Among the 501 female participants, there were 69 events recorded. 

 - Sex=Male: The male group, significantly larger, consists of 3,920 individuals, with 578 events observed.

 - Also, since there is no median and 95% confidence limit, we can only roughly analyze according to the event rate:**There's little difference in survival between the sexes**.

``` r
surv_by_sex <- survfit(Surv(time = Survival_mos, 
                     event = Death_Status == '1') ~ Sex, 
                     data = gc_data_updated)
surv_by_sex
```

```
## Call: survfit(formula = Surv(time = Survival_mos, event = Death_Status == 
##     "1") ~ Sex, data = gc_data_updated)
## 
##               n events median 0.95LCL 0.95UCL
## Sex=Female  501     69     NA      NA      NA
## Sex=Male   3920    578     NA      NA      NA
```

## By Race
**Analysis By race**: 

 - Race=Black: Comprising 498 individuals, this group observed 78 events. 

 - Race=Other: This group includes 209 individuals with a broader racial categorization, witnessing 21 events.
 
 - Race=White: The largest of the three, this group consists of 3,714 individuals and recorded 548 events. The significant size of this group and the number of events it encompasses highlight its central role in the survival analysis. 
 
 - Also, since there is no median and 95% confidence limit, we can only roughly analyze according to the event rate:**There's little difference in survival between the race**.

``` r
surv_by_race <- survfit(Surv(time = Survival_mos, 
                     event = Death_Status == '1') ~ Race, 
                     data = gc_data_updated)
surv_by_race
```

```
## Call: survfit(formula = Surv(time = Survival_mos, event = Death_Status == 
##     "1") ~ Race, data = gc_data_updated)
## 
##               n events median 0.95LCL 0.95UCL
## Race=Black  498     78     NA      NA      NA
## Race=Other  209     21     NA      NA      NA
## Race=White 3714    548     NA      NA      NA
```

## By Age Group

**Analysis By age group**: 

 - Age_Group=<65: This younger age group, consisting of 2,143 individuals, witnessed 213 events. 
 
 - Age_Group=>65: The older age group includes 2,278 individuals, with 434 events noted.
 
 - The distribution of events between the two age groups points towards a higher event occurrence in the older age group. This outcome is consistent with general expectations about age-related increases in mortality risk.

- Also, since there is no median and 95% confidence limit, we can only roughly analyze according to the event rate:**There's little difference in survival between the age group**.


``` r
# Define age groups
surv_by_age <- survfit(Surv(time = Survival_mos, 
                     event = Death_Status == '1') ~ Age_Group, 
                     data = gc_data_updated)
surv_by_age
```

```
## Call: survfit(formula = Surv(time = Survival_mos, event = Death_Status == 
##     "1") ~ Age_Group, data = gc_data_updated)
## 
##                  n events median 0.95LCL 0.95UCL
## Age_Group=<65 2143    213     NA      NA      NA
## Age_Group=>65 2278    434     NA      NA      NA
```

# 3. Log-rank test and K-M Plot for predictors {.tabset}

## For All
**Analysis of all patients**: 

First, we plotted a total Kaplan-Meier survival curve describing the probability of survival for all patients over time.

- As can be seen from the figure above, the overall survival probability for all patients gradually decreases from 1 over time. Each mark (small vertical line) on the curve indicates the occurrence of one or more events (such as death).

- A stratified table of survival data shows the number of patients at a particular point in time. For example, at day 0 there were 4421 patients, at day 20 there were 2739 patients, at day 40 there were 1442 patients, at day 60 there were 482 patients, and at day 80 the data showed 0, meaning that all the subjects had died or ended the study before that point.


``` r
# Plot the Kaplan-Meier survival curve using survminer for all patients
ggsurvplot(
    fit = surv_all, 
    xlab = "Days", 
    ylab = "Overall survival probability",                
    risk.table = TRUE,
    conf.int = FALSE,
    censor = TRUE,
    legend = "none") + labs(title = "K-M plot for Glottic cancer Dataset data for all patients")
```

![](method_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

## By Treatment Status
**Analysis By Radiation or Surgery Treatment Status**: 

- Null hypothesis and alternative hypothesis of the log-rank test：

H0: There is no difference in the survival function between those who were in different Radiation/Surgery Treatment status classification

Ha: There is difference in the survival function between those who were in different Radiation/Surgery Treatment status classification

- The chart below compares survival rates for different treatment states. The curve was divided into four groups according to the type of treatment received: surgery only, radiation only, both, and neither.

- The four curves show the different treatment methods. From the **differences in the curves**, it can be seen that the survival of patients with different treatment methods is significantly different. To be specific:
   
 (1)The survival curve for the surgery group only was initially higher but declined over time, suggesting that surgical treatment may have provided a better probability of survival in the short term, but its effect may have diminished over time.
   
 (2)The curve for the radiation-only group was lower at the start than for the surgery-only group, but over time the probability of survival tended to be similar for both groups.

 (3)The curves for both groups were the highest overall, which may indicate that surgery combined with radiation therapy provided the best probability of survival.

 (4)The survival curve was consistently the lowest in the neither group, indicating that patients who did not receive any treatment had the worst probability of survival.

- In the table below the curve, we can see that the number of patients in each group gradually decreases over time. This is due to patients dying or dropping out of the study for other reasons over time.

- According to the output **P-value (<0.0001)** in the figure, there is sufficient evidence to reject the null hypothesis, that is, **there is a statistically significant difference in survival between the treatment status**
 
 
![](method_files/figure-html/unnamed-chunk-13-1.png)<!-- -->


```
## Call:
## survdiff(formula = Surv(Survival_mos, Death_Status == "1") ~ 
##     Rad_Tx_Status, data = gc_data_updated)
## 
## n=4315, 因为不存在，106个观察量被删除了.
## 
##                       N Observed Expected (O-E)^2/E (O-E)^2/V
## Rad_Tx_Status=0.00  622       69     86.8      3.66      4.28
## Rad_Tx_Status=1.00 2247      340    320.6      1.17      2.44
## Rad_Tx_Status=2.00 1213      152    187.4      6.67      9.61
## Rad_Tx_Status=3.00  233       60     26.2     43.58     45.75
## 
##  Chisq= 55.4  on 3 degrees of freedom, p= 6e-12
```

```
## p value =  5.61784e-12
```


## By Cancer Staging
**Analysis By Cancer Stagings**: 

- Null hypothesis and alternative hypothesis of the log-rank test：

H0: There is no difference in the survival function between those who were in different cancer staging.

Ha: There is difference in the survival function between those who were in different cancer staging.

- The four curves show the different cancer stages. From the **differences in the curves**, it can be seen that the survival of patients with different cancer stages is significantly different. To be specific:
 
（1）The probability of survival appears to be highest in the T1a group, which may mean that patients with this stage have the best prognosis.
（2）The T2 group had the lowest survival curve, indicating a poorer prognosis for patients at this stage.
（3）The survival curves for the T1b and T1NOS groups were somewhere in between.

- With a P-value (<0.0001), we have sufficient evidence to reject the null hypothesis, that is, the difference in survival between different cancer stage groups is very significant.
![](method_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

```
## Call:
## survdiff(formula = Surv(Survival_mos, Death_Status == "1") ~ 
##     Derived_AJCC, data = gc_data_updated)
## 
##                       N Observed Expected (O-E)^2/E (O-E)^2/V
## Derived_AJCC=T1a   2104      248    315.0     14.25     27.93
## Derived_AJCC=T1b    399       71     56.5      3.73      4.10
## Derived_AJCC=T1NOS  834       99    123.7      4.92      6.11
## Derived_AJCC=T2    1084      229    151.8     39.21     51.51
## 
##  Chisq= 62.4  on 3 degrees of freedom, p= 2e-13
```

```
## p value =  1.764144e-13
```

## By Sex
**Analysis By Sex**: 

- Null hypothesis and alternative hypothesis of the log-rank test：

H0:There is no difference in the survival function between those who were in different sex group.

Ha:There is difference in the survival function between those who were in different sex group.

- The chart below compares overall survival rates for male and female patients.

- From the curve, the survival curves of the two sexes **almost coincide**, that is, there is no significant difference in survival rates between the sexes.

- With a **P-value of 0.43**, we do not have enough evidence to reject the null hypothesis, that is, **the difference in survival between the sexes is not significant**.

![](method_files/figure-html/unnamed-chunk-19-1.png)<!-- -->

```
## Call:
## survdiff(formula = Surv(Survival_mos, Death_Status == "1") ~ 
##     Sex, data = gc_data_updated)
## 
##               N Observed Expected (O-E)^2/E (O-E)^2/V
## Sex=Female  501       69     75.5    0.5519     0.628
## Sex=Male   3920      578    571.5    0.0729     0.628
## 
##  Chisq= 0.6  on 1 degrees of freedom, p= 0.4
```

```
## p value =  0.4281099
```

## By Race

**Analysis By Race**: 

- Null hypothesis and alternative hypothesis of the log-rank test：

H0:There is no difference in the survival function between those who were in different race group.

Ha:There is difference in the survival function between those who were in different race group.

- The chart below compares the overall survival rates of patients across ethnic groups.

- From the curve, there is **no significant difference in the survival curve** between patients of different races, that is, there is no significant difference in the survival rate between races.

- With a **P-value (0.21)**, we do not have enough evidence to reject the null hypothesis, that is, **the difference in survival between different races is not significant**.
![](method_files/figure-html/unnamed-chunk-22-1.png)<!-- -->

```
## Call:
## survdiff(formula = Surv(Survival_mos, Death_Status == "1") ~ 
##     Race, data = gc_data_updated)
## 
##               N Observed Expected (O-E)^2/E (O-E)^2/V
## Race=Black  498       78     70.9   0.70361    0.7944
## Race=Other  209       21     29.4   2.41067    2.5388
## Race=White 3714      548    546.6   0.00337    0.0218
## 
##  Chisq= 3.1  on 2 degrees of freedom, p= 0.2
```

```
## p value =  0.2086899
```
 
## By Age Group

- Null hypothesis and alternative hypothesis of the log-rank test：

H0: There is no difference in the survival function between those who were in different age group.

Ha: There is difference in the survival function between those who were in different age group.

- The chart below compares the overall survival rate of patients in different age groups.

- From the curve, there are significant differences in the survival curve of patients between different races, and the survival curve of the group under 65 years old is higher than that of the group 65 years and older, which indicates that the overall survival rate of patients in the younger group is higher. The probability of survival declined over time in both groups, but the **decline appeared to be slower** in the under-65 group.
 
- According to the output **P-value (<0.0001)** in the figure, there is sufficient evidence to reject the null hypothesis, that is, **there is a statistically significant difference in survival between age group**
 
![](method_files/figure-html/unnamed-chunk-25-1.png)<!-- -->

```
## Call:
## survdiff(formula = Surv(Survival_mos, Death_Status == "1") ~ 
##     Age_Group, data = gc_data_updated)
## 
##                  N Observed Expected (O-E)^2/E (O-E)^2/V
## Age_Group=<65 2143      213      324      38.2      77.1
## Age_Group=>65 2278      434      323      38.4      77.1
## 
##  Chisq= 77.1  on 1 degrees of freedom, p= <2e-16
```

```
## p value =  0
```

# 4. Multivariable Cox Proportional Hazards Regression Model {.tabset}

## Set Reference
Before modeling, we first select a reference for each of the multi-class predictors so that we can compare the effects of the other classes. Here, we set a reference group for each of our multi-categorical predictors of treatment, race, and cancer stage. Specifically, a group of patients who received no treatment was selected as a reference group for treatment status to compare the relative effects of other treatment modalities, such as surgery alone, radiation alone, or both. Within the race category, the white group, as the largest race, is set as the reference group for the other races. In the comparison of cancer stages, stage T1a was chosen as the baseline category in order to assess the probability of survival of other cancer stages such as T1b, T1NOS and T2 relative to stage T1a. With this approach, we can quantitatively analyze the association between individual predictors and survival and determine if there is a statistically significant difference.



``` r
# Set reference
gc_data_updated$Treatment = relevel(gc_data_updated$Treatment, ref = "Neither")
gc_data_updated$Race <- relevel(gc_data_updated$Race, ref = "White")
gc_data_updated$Derived_AJCC <- relevel(gc_data_updated$Derived_AJCC, ref = "T1a")
```

## Cox Regression Model
Then we perform a multivariable cox proportional hazards regression model using the following covariates: sex, age at diagnosis, race, tumor stage, and Treatment status.
The Cox regression model is a semi-parametric model that can be used to fit univariable and multivariable regression models that have survival outcomes.
$$h(t|X_i) = h_0(t)exp(\beta_1X_{i1}\ +\ \dots\ + \beta_pX_{ip})$$
(Because the cox model of R language can carry out dummy conversion automatically, there is no need to convert in advance.)

```
## Call:
## coxph(formula = Surv(Survival_mos, Death_Status == "1") ~ Derived_AJCC + 
##     Sex + Age_Group + Race + Treatment, data = gc_data_updated)
## 
##   n= 4315, number of events= 621 
##    (因为不存在，106个观察量被删除了)
## 
##                        coef exp(coef) se(coef)      z Pr(>|z|)    
## Derived_AJCCT1b     0.43122   1.53914  0.13733  3.140  0.00169 ** 
## Derived_AJCCT1NOS  -0.02914   0.97128  0.12186 -0.239  0.81103    
## Derived_AJCCT2      0.62993   1.87748  0.09486  6.641 3.12e-11 ***
## SexMale             0.09061   1.09485  0.13143  0.689  0.49053    
## Age_Group>65        0.71032   2.03465  0.08517  8.340  < 2e-16 ***
## RaceBlack           0.07434   1.07717  0.12473  0.596  0.55119    
## RaceOther          -0.33779   0.71334  0.22799 -1.482  0.13845    
## TreatmentSurgery   -0.96469   0.38110  0.17792 -5.422 5.89e-08 ***
## TreatmentRadiation -0.79216   0.45286  0.14088 -5.623 1.88e-08 ***
## TreatmentBoth      -1.02134   0.36011  0.15402 -6.631 3.33e-11 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
##                    exp(coef) exp(-coef) lower .95 upper .95
## Derived_AJCCT1b       1.5391     0.6497    1.1759    2.0145
## Derived_AJCCT1NOS     0.9713     1.0296    0.7649    1.2333
## Derived_AJCCT2        1.8775     0.5326    1.5590    2.2611
## SexMale               1.0948     0.9134    0.8462    1.4165
## Age_Group>65          2.0346     0.4915    1.7218    2.4043
## RaceBlack             1.0772     0.9284    0.8436    1.3755
## RaceOther             0.7133     1.4019    0.4563    1.1152
## TreatmentSurgery      0.3811     2.6240    0.2689    0.5401
## TreatmentRadiation    0.4529     2.2082    0.3436    0.5969
## TreatmentBoth         0.3601     2.7769    0.2663    0.4870
## 
## Concordance= 0.655  (se = 0.012 )
## Likelihood ratio test= 172.2  on 10 df,   p=<2e-16
## Wald test            = 179.2  on 10 df,   p=<2e-16
## Score (logrank) test = 187  on 10 df,   p=<2e-16
```

## Present Results


In order to make the output results of the model clearer, we further output the specific parameters of each predictor.
<table class="table table-striped" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> Term </th>
   <th style="text-align:right;"> Adjusted Hazard Ratio </th>
   <th style="text-align:right;"> Lower 95% CI </th>
   <th style="text-align:right;"> Upper 95% CI </th>
   <th style="text-align:left;"> P-value </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Cancer Stage T1b (vs. T1a) </td>
   <td style="text-align:right;"> 1.54 </td>
   <td style="text-align:right;"> 1.18 </td>
   <td style="text-align:right;"> 2.01 </td>
   <td style="text-align:left;"> &lt; 0.01 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cancer Stage T1NOS (vs. T1a) </td>
   <td style="text-align:right;"> 0.97 </td>
   <td style="text-align:right;"> 0.76 </td>
   <td style="text-align:right;"> 1.23 </td>
   <td style="text-align:left;"> 0.81 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cancer Stage T2 (vs. T1a) </td>
   <td style="text-align:right;"> 1.88 </td>
   <td style="text-align:right;"> 1.56 </td>
   <td style="text-align:right;"> 2.26 </td>
   <td style="text-align:left;"> &lt; 0.01 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Male (vs. Female) </td>
   <td style="text-align:right;"> 1.09 </td>
   <td style="text-align:right;"> 0.85 </td>
   <td style="text-align:right;"> 1.42 </td>
   <td style="text-align:left;"> 0.49 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Race Black (vs. White) </td>
   <td style="text-align:right;"> 1.08 </td>
   <td style="text-align:right;"> 0.84 </td>
   <td style="text-align:right;"> 1.38 </td>
   <td style="text-align:left;"> 0.55 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Race Other (vs. White) </td>
   <td style="text-align:right;"> 0.71 </td>
   <td style="text-align:right;"> 0.46 </td>
   <td style="text-align:right;"> 1.12 </td>
   <td style="text-align:left;"> 0.14 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Treatment Status Surgery (vs. Neither) </td>
   <td style="text-align:right;"> 0.38 </td>
   <td style="text-align:right;"> 0.27 </td>
   <td style="text-align:right;"> 0.54 </td>
   <td style="text-align:left;"> &lt; 0.01 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Treatment Status Radiation (vs. Neither) </td>
   <td style="text-align:right;"> 0.45 </td>
   <td style="text-align:right;"> 0.34 </td>
   <td style="text-align:right;"> 0.60 </td>
   <td style="text-align:left;"> &lt; 0.01 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Treatment Status Both (vs. Neither) </td>
   <td style="text-align:right;"> 0.36 </td>
   <td style="text-align:right;"> 0.27 </td>
   <td style="text-align:right;"> 0.49 </td>
   <td style="text-align:left;"> &lt; 0.01 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age &gt; 65 (vs. Age&lt; 65) </td>
   <td style="text-align:right;"> 0.49 </td>
   <td style="text-align:right;"> 0.42 </td>
   <td style="text-align:right;"> 0.58 </td>
   <td style="text-align:left;"> &lt; 0.01 </td>
  </tr>
</tbody>
</table>


## Model Interpretation

Our study utilized a Cox proportional hazards model to assess the impact of various factors on survival probability.  Holding other variables constant, the model's findings indicated the following:

- **Treatment Status**: Patients who received both surgery and radiation treatments had a 64% reduction in the risk of death compared to those who received neither treatment (Adjusted Hazard Ratio 0.36, 95% CI: 0.27-0.49, p < 0.01).

- **Age Group**: Patients older than 65 years had a significantly higher risk of death compared to those younger than 65 (Adjusted Hazard Ratio 2.03, 95% CI: 1.72-2.40, p < 0.01).

- **Cancer Staging**: Compared to stage T1a, patients with stage T1b had a 54% increased risk of death (Adjusted Hazard Ratio 1.54, 95% CI: 1.18-2.01, p < 0.01), and those with stage T2 had an 88% increased risk (Adjusted Hazard Ratio 1.88, 95% CI: 1.56-2.26, p < 0.01).  The risk associated with stage T1NOS was not statistically significant (Adjusted Hazard Ratio 0.97, 95% CI: 0.76-1.23, p > 0.05).

- **Gender**: The slight increase in risk of death for males compared to females was not statistically significant (Adjusted Hazard Ratio 1.09, 95% CI: 0.85-1.42, p > 0.05).

- **Race**: The risk of death for Black individuals, as compared to White, was slightly higher, yet not significant (Adjusted Hazard Ratio 1.08, 95% CI: 0.84-1.38, p > 0.05).  The risk for other races was marginally lower compared to White, but this also did not reach statistical significance (Adjusted Hazard Ratio 0.71, 95% CI: 0.46-1.12, p > 0.05).

- **Treatments**: The risk of death for patients who only underwent surgery was reduced by 62% compared to those who received no treatment (Adjusted Hazard Ratio 0.38, 95% CI: 0.27-0.54, p < 0.01), and for those who only received radiation therapy, the risk was reduced by 55% (Adjusted Hazard Ratio 0.45, 95% CI: 0.34-0.60, p < 0.01).
Patients who received a combination of both surgery and radiation therapy demonstrated a substantial decrease in the risk of mortality compared to patients who did not receive any treatment. The adjusted hazard ratio of 0.36 indicates that the combined treatment group had a 64% lower risk of death, with a 95% confidence interval ranging from 0.27 to 0.49, which is statistically significant (p < 0.01).

- **Age Group**: The analysis also revealed that patients older than 65 years had a higher risk of death compared to younger patients. Specifically, the adjusted hazard ratio of 2.03 suggests that the risk of death for patients over 65 is more than double that of patients under 65. This result is statistically significant with a 95% confidence interval between 1.72 and 2.40 (p < 0.01).

## Summary

The Cox proportional hazards model indicates that patients over 65 years old have more than double the risk of mortality than those under 65, and this is statistically significant. Patients receiving both surgery and radiation have a 64% lower risk of death compared to those with no treatment, a highly significant finding.    Patients with cancer stage T2 have an 88% higher risk of mortality compared to those with stage T1a, marking a significant escalation in risk with advancing cancer stage.   Additionally, individuals with cancer stage T1b exhibit a 54% increased risk of mortality compared to the T1a baseline, highlighting the importance of cancer stage in survival outcomes. Overall, age， comprehensive treatment and cancer stage are major determinants of patient survival outcomes in this study.
