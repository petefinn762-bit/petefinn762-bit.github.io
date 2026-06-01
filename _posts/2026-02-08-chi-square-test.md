---
layout: post
title: Assessing Campaign Performance Using Chi-Square Test For Independence
image: "/posts/ab-testing-title-img.png"
tags: [AB Testing, Hypothesis Testing, Chi-Square, Python]
---

In this project, I applied the chi-Square test for independence (a hypothesis test) to assess the performance of two types of mailer that were sent out to promote a new service.

# Table of contents

- [00. Project Overview](#overview-main)
    - [Context](#overview-context)
    - [Actions](#overview-actions)
    - [Results & Discussion](#overview-results)
- [01. Concept Overview](#concept-overview)
- [02. Data Overview & Preparation](#data-overview)
- [03. Applying Chi-Square Test For Independence](#chi-square-application)
- [04. Analysing The Results](#chi-square-results)
- [05. Discussion](#discussion)

___

# Project Overview  <a name="overview-main"></a>

### Context <a name="overview-context"></a>

Earlier in the year, my client, a grocery retailer, ran a campaign to promote their new "Delivery Club" - an initiative that costs the customer £100 per year for membership in return for free deliveries that normally cost £10 each.

For the campaign promoting the club, customers were put randomly into three groups: the first group received a low quality, low cost mailer; the second group received a high quality, high cost mailer; and the third group were a control group, receiving no mailer at all.

The client knew that customers who were contacted signed up for the Delivery Club at a far higher rate than the control group, but wanted to understand if there was a significant difference in signup rate between the cheap mailer and the expensive one.  This would allow them to make more informed decisions in the future, with the overall aim of optimising campaign ROI.

<br>
<br>
### Actions <a name="overview-actions"></a>

For this test, I applied the chi-square test for independence as it is focused on comparing the *rates* of two groups.  Full details of this test can be found in the dedicated section below.

**Note:** Another option when comparing "rates" is a test known as the *z-test for proportions*.  While I could have used this test here, I chose the chi-square test for independence for the following reasons:

* The chi-square test can be represented using 2x2 tables of data, which is easier to explain to stakeholders
* The chi-square test can extend out to more than 2 groups, meaning the client can have one consistent approach to measuring significance

From the *campaign_data* table in the client database, I isolated customers that received "Mailer 1" (low cost) and "Mailer 2" (high cost) for this campaign, and excluded customers who were in the control group.

I set out my hypotheses and acceptance criteria for the test, as follows:

**Null Hypothesis:** There is no relationship between mailer type and signup rate. They are independent.
**Alternate Hypothesis:** There is a relationship between mailer type and signup rate. They are not independent.
**Significance Level:** 0.05

As a requirement of the chi-square test for independence, I aggregated this data down to a 2x2 matrix for *signup_flag* by *mailer_type* and fed this into the algorithm (using the *SciPy* library) to calculate the chi-square statistic, p-value, degrees of freedom, and expected values.

<br>
<br>

### Results & Discussion <a name="overview-results"></a>

The observed sign-up rates suggested that the high cost mailer was more effective:

* Mailer 1 (Low Cost): **32.8%** signup rate
* Mailer 2 (High Cost): **37.8%** signup rate

However, the chi-square test produced the following statistics:

* Chi-Square Statistic: **1.94**
* p-value: **0.16**

The critical value for the chi-square statistic at the specified significance level of 0.05 is **3.84** (with 1 degree of freedom).

Based upon these statistics, I retained the null hypothesis, and concluded that there was no relationship between mailer type and signup rate.

In other words - while I saw that the higher cost Mailer 2 had a higher signup rate (37.8%) than the lower cost Mailer 1 (32.8%), the difference did not meet my acceptance criteria for statistical significance (p < 0.05).

Without running this hypothesis test, the client may have concluded that they should always look to go with higher cost mailers. However, this test allowed them to see that this would not *necessarily* gain them any extra revenue as a result.

It is worth noting that I did not claim that there *definitely wasn't a difference between the two mailers*; I merely advised that the client should not make any rigid conclusions *at this point*. More insight would be gained by running more A/B Tests like this, gathering more data, and then re-running this test.

<br>
<br>

___

# Concept Overview  <a name="concept-overview"></a>

<br>
#### A/B Testing

An A/B Test is a randomised experiment containing two groups, A & B, that receive different experiences. It measures and compares the response of each group, giving information that helps drive future business decisions.

Application of A/B testing can range from testing different online ad strategies, different email subject lines when contacting customers, or testing the effect of mailing customers a coupon vs a control group.  Companies like Amazon are running these tests in an almost never-ending cycle, testing new website features on randomised groups of customers... all with the aim of finding what works best so they can stay ahead of their competition.

<br>
#### Hypothesis Testing

A hypothesis test is used to assess the plausibility or likelihood of an assumed viewpoint based on sample data - in other words, it helps us assess whether a certain view we have about some data is likely to be true or not.

There are many different scenarios we can run hypothesis tests on, and they all have slightly different techniques and formulas; however, they all have some shared, fundamental steps and logic that underpin how they work.

<br>
**The Null Hypothesis**

In any hypothesis test, we start with the null hypothesis. The null hypothesis is where we state our initial viewpoint, and in statistics, and specifically hypothesis testing, our initial viewpoint is always that the result is purely by chance or that there is no relationship or association between two outcomes or groups.

<br>
**The Alternate Hypothesis**

The aim of the hypothesis test is to look for evidence to support or reject the null hypothesis.  If we reject the null hypothesis, that would mean we’d be supporting the alternate hypothesis.  The alternate hypothesis is essentially the opposite viewpoint to the null hypothesis - that the result is *not* by chance, or that there *is* a relationship between two outcomes or groups.

<br>
**The Significance Level**

In a hypothesis test, before we collect any data or run any numbers, we specify a significance level.  This is a p-value threshold at which we’ll decide to reject or support the null hypothesis.  It is essentially a line we draw in the sand saying "if I were to run this test many, many times, what proportion of those times would I want to see different results come out, in order to feel comfortable or confident that my results were not just some unusual occurrence"

Conventionally, we set our significance level at 0.05 - but this does not have to be the case.  If we need to be more confident that something did not occur through chance alone, we could lower this value to something much smaller, meaning that we only come to the conclusion that the outcome was special or rare if it’s extremely rare.

So to summarise, in a hypothesis test, we test the null hypothesis using a p-value and then decide its fate based on a pre-determined significance level.

<br>
**Types Of Hypothesis Test**

There are many different types of Hypothesis Tests, each of which is appropriate for use in differing scenarios, depending on a) the type of data that you’re looking to test and b) the question that you’re asking of that data.

In the case of the task here, where I was looking to understand the difference in sign-up *rate* between two groups, I utilised the chi-square test for independence.

<br>
#### Chi-Square Test For Independence

The chi-square test for independence is a type of hypothesis test that assumes observed frequencies for categorical variables will match the expected frequencies.

The *assumption* is the null hypothesis, which as discussed above is always the viewpoint that the two groups will be equal.  With the chi-square test for independence we look to calculate a statistic which, based on the specified significance level, will mean we either reject or support this initial assumption.

The *observed frequencies* are the true values that we’ve seen.

The *expected frequencies* are essentially what we would *expect* to see based on all of the data.

**Note:** Another option when comparing "rates" is a test known as the *Z-test for proportions*.  While I could have used this test here, I chose the chi-square test for independence for the following reasons:

* The chi-square test can be represented using 2x2 tables of data, which is easier to explain to stakeholders
* The chi-square test can extend out to more than 2 groups, meaning the client can have one consistent approach to measuring significance

___

<br>
# Data Overview & Preparation  <a name="data-overview"></a>

In the client database, I had access to a *campaign_data* table which recorded which customers received each type of "Delivery Club" mailer, which customers were in the control group, and which customers joined the club as a result.

For this task, I was looking to find evidence that the Delivery Club signup rate for customers that received "Mailer 1" (low cost) was different to those who received "Mailer 2" (high cost) and thus from the *campaign_data* table I only extracted customers in those two groups, and excluded customers who were in the control group.

The code below is designed to achieve the following:

* Load in the Python libraries required for importing the data and performing the chi-square test (using SciPy)
* Import the required data from the *campaign_data* table
* Exclude customers in the control group, producing a dataset with Mailer 1 & Mailer 2 customers only

<br>
```python

# install the required python libraries
import pandas as pd
from scipy.stats import chi2_contingency, chi2

# import campaign data
campaign_data = ...

# remove customers who were in the control group
campaign_data = campaign_data.loc[campaign_data["mailer_type"] != "Control"]

```
<br>
A sample of this data (the first 10 rows) can be seen below:
<br>
<br>

| **customer_id** | **campaign_name** | **mailer_type** | **signup_flag** |
|---|---|---|---|
| 74 | delivery_club | Mailer1 | 1 |
| 524 | delivery_club | Mailer1 | 1 |
| 607 | delivery_club | Mailer2 | 1 |
| 343 | delivery_club | Mailer1 | 0 |
| 322 | delivery_club | Mailer2 | 1 |
| 115 | delivery_club | Mailer2 | 0 |
| 1 | delivery_club | Mailer2 | 1 |
| 120 | delivery_club | Mailer1 | 1 |
| 52 | delivery_club | Mailer1 | 1 |
| 405 | delivery_club | Mailer1 | 0 |
| 435 | delivery_club | Mailer2 | 0 |

<br>
The DataFrame contains the following:

* customer_id
* campaign name
* mailer_type (either Mailer1 or Mailer2)
* signup_flag (either 1 or 0)

___

<br>
# Applying Chi-Square Test For Independence <a name="chi-square-application"></a>

<br>
#### State Hypotheses & Significance Level For Test

The very first thing we need to do in any form of hypothesis test is state our null hypothesis, our alternate hypothesis, and the significance level (more details on these in the section above).

As can be seen below, I coded these in explicitly so I could utilise them later to explain the results.  I specified the common significance level of 0.05.

```python

# specify hypotheses & significance level for test
null_hypothesis = "there is no relationship between mailer type and signup rate.  They are independent."
alternate_hypothesis = "there is a relationship between mailer type and signup rate.  They are not independent."
significance_level = 0.05

```

<br>
#### Calculate Observed Frequencies & Expected Frequencies

As mentioned in the section above, in a chi-square test for independence, the *observed frequencies* are the true values that we’ve seen, in other words the actual rates per group in the data itself.  The *expected frequencies* are what we would *expect* to see based on *all* of the data combined.

The code below does the following:

* Summarises the dataset to a 2x2 matrix for *signup_flag* by *mailer_type*
* Based on this, it calculates the
    * chi-square statistic
    * p-value
    * degrees of freedom
    * expected values
* Prints out the chi-square statistic & p-value from the test
* Calculates the critical value based upon the significance level & the degrees of freedom
* Prints out the critical value

```python

# aggregate the data to get observed values
observed_values = pd.crosstab(campaign_data["mailer_type"], campaign_data["signup_flag"]).values

# run the chi-square test
chi2_statistic, p_value, dof, expected_values = chi2_contingency(observed_values, correction = False)

# print chi-square statistic
print(chi2_statistic)
>> 1.94

# print p-value
print(p_value)
>> 0.16

# find the critical value for the test
critical_value = chi2.ppf(1 - significance_level, dof)

# print critical value
print(critical_value)
>> 3.84

```
<br>
The observed sign-up rates suggested that the high cost mailer was more effective:

* Mailer 1 (Low Cost): **32.8%** signup rate
* Mailer 2 (High Cost): **37.8%** signup rate

The results from the chi-square test would reveal if this difference was robust or if it might have occurred by chance.

I obtained a chi-square statistic of **1.94** and a p-value of **0.16**.  
The critical value for the chi-square statistic at the specified significance level of 0.05 is **3.84** (with 1 degree of freedom).

**Note** When applying the chi-square test above, I used the parameter *correction = False* which means I was not applying the *Yates' Correction*. This is applied to prevent overestimation of statistical signficance when your Degrees of Freedom is equal to one and one of your values in the matrix is below 5, which was not the case here.

___

<br>
# Analysing The Results <a name="chi-square-results"></a>

At this point, I had everything I needed to understand the results of the chi-square test - and just from the results above, I could see that, since the resulting p-value of **0.16** was *greater* than the significance level of 0.05, I would _retain_ the Null Hypothesis and conclude that there was no significant difference between the signup rates of Mailer 1 and Mailer 2.

I could have made the same conclusion based upon the resulting chi-square statistic of **1.94** being _lower_ than the critical value of **3.84**

To make this script more dynamic, I created code to automatically interpret the results and explain the outcome...

```python

# print the results (based upon p-value)
if p_value <= significance_level:
    print(f"As the p-value of {p_value} is lower than the significance_level of {significance_level}, we reject the null hypothesis and conclude that {alternate_hypothesis}")
else:
    print(f"As the p-value of {p_value} is higher than the significance_level of {significance_level}, we retain the null hypothesis and conclude that {null_hypothesis}")

>> As the p-value of 0.16351 is higher than the significance_level of 0.05, we retain the null hypothesis and conclude that there is no relationship between mailer type and signup rate.  They are independent.


# print the results (based upon p-value)
if chi2_statistic >= critical_value:
    print(f"As the chi-square statistic of {chi2_statistic} is higher than the critical value of {critical_value}, we reject the null hypothesis and conclude that {alternate_hypothesis}")
else:
    print(f"As the chi-square statistic of {chi2_statistic} is lower than the critical value of {critical_value}, we retain the null hypothesis and conclude that {null_hypothesis}")
    
>> As the chi-square statistic of 1.9414 is lower than the critical value of 3.841458820694124, we retain the null hypothesis and conclude that there is no relationship between mailer type and signup rate.  They are independent.

```
<br>
As can be seen from the outputs of these print statements, I did indeed retain the null hypothesis.  I could not find enough evidence that the signup rates for Mailer 1 and Mailer 2 were different and thus concluded that there was no significant difference.

___

<br>
# Discussion <a name="discussion"></a>

While I saw that the higher cost Mailer 2 had a higher signup rate (37.8%) than the lower cost Mailer 1 (32.8%), the difference did not meet my acceptance criteria for statistical significance (p < 0.05).

Without running this hypothesis test, the client may have concluded that they should always look to go with higher cost mailers. However, this test allowed them to see that this would not *necessarily* gain them any extra revenue as a result.

It is worth noting that I did not claim that there *definitely wasn't a difference between the two mailers*; I merely advised that the client should not make any rigid conclusions *at this point*. More insight would be gained by running more A/B Tests like this, gathering more data, and then re-running this test.
