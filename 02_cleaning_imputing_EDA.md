---
layout: page
title: EDA
permalink: /eda
---

# Data sources and crawling
The first step was to identify the data we will need to predict the elections results at district level, then, to identify where to find this data and finally, write crawlers when necessary.
Election results: This data was available in the official states websites on different format across the states and years. It would have been way too long to reconcile all of them. The needed data  are also available on some websites like Wikipedia.org and Ballotpedia.org. It was a good tradeoff between ease of extraction and accuracy. But many times we faced the situation that the data quality was bad and we even had to add data manually.

# Variable description

- **won**: Response variable. 1 if the candidate won, 0 otherwise
- **district**: Congressional districts name
- **is_incumbent**: Whether an incumbent is running for re-election
- **name**: The candidate's name
- **party**: The candidate's party
- **percent**: The percentage of votes received by the candidate
- **state**: The name of the state
- **votes**: The number of votes received by the candidate
- **year**: The year of the election.
- **first_time_elected**: The year of the first election won in this district by the candidate (NaN if not applicable).
- **count_victories**: The number of elections won in this district by the candidate
- **unemployement_rate**: The unemployment rate of the district (or of the country if the information was not available) for the month before the election.
- **is_presidential_year**: 1 if there is a presidential election this year, 0 otherwise.
- **president_can_be_re_elected**: Can the president stand for re-election ? 1 = Yes, 0 = No
- **president_party**: The president's party (R or D)
- **president_overall_avg_job_approval**: The presidential job approval ratings (available from Truman - to Trump). Source: Gallup.
- **last_D_house_seats**: The number of house seats won by democrats at the last elections
- **last_R_house_seats**: The number of house seats won by republicans at the last elections
- **last_house_majority**: Which party have the majority (R or D)
- **fundraising**: How much money does the candidate raised for the campaign

# Data cleaning
The biggest effort was to collect data from many different sources like websites, pdf, excel and csv files. The challenge was to get the all data for the baseline to the same aggregation level. Especially it was difficult to get data on district level. Details are described in the page for crawling. Even after extensive data imputation we had some missing values (or NaNs):

<table>
  <thead>
    <tr>
      <th>Variable</th>
      <th># NaN</th>
      <th>Data imputation</th>
      <th># NaN after imputation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>district</td>
      <td>0</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <td>is_incumbent</td>
      <td>112</td>
      <td>if sum of is_incumbent of state/district/year > 0 then assign 0, else 1</td>
      <td>0</td>
    </tr>
    <tr>
      <td>name</td>
      <td>0</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <td>party</td>
      <td>0</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <td>percent</td>
      <td>15</td>
      <td>calculated with mean of votes from state and district</td>
      <td>0</td>
    </tr>
    <tr>
      <td>state</td>
      <td>0</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td>votes</td>
      <td>58</td>
      <td>replace with mean of votes from state and district</td>
      <td>0</td>
    </tr>
    <tr>
      <td>won (response variable)</td>
      <td>0</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <td>year</td>
      <td>0</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <td>first_time_elected</td>
      <td>4445</td>
      <td>take value from year if won=1 else 0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>count_victories</td>
      <td>0</td>
      <td></td>
      <td>0</td>
    </tr>
    <tr>
      <td>unemployement_rate</td>
      <td>979</td>
      <td>We don't have data before 1946. Set to 0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>is_presidential_year</td>
      <td>102</td>
      <td>Set to 0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>president_can_be_re_elected</td>
      <td>102</td>
      <td>Set to 0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>president_party</td>
      <td>102</td>
      <td>Set to 0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>president_overall_avg_job_approval</td>
      <td>1060</td>
      <td>Replace with mean from state and district</td>
      <td>0</td>
    </tr>
    <tr>
      <td>last_D_house_seats</td>
      <td>102</td>
      <td>Replace with mean from state and district</td>
      <td>0</td>
    </tr>
    <tr>
      <td>last_R_house_seats</td>
      <td>102</td>
      <td>Replace with mean from state and district</td>
      <td>0</td>
    </tr>
    <tr>
      <td>last_house_majority</td>
      <td>102</td>
      <td>Replace with the most common value from state and district</td>
      <td>0</td>
    </tr>
    <tr>
      <td>fundraising</td>
      <td>7161</td>
      <td>Replace with mean from state and district</td>
      <td>172</td>
    </tr>
  </tbody>
</table>

For the remaining 172 NaN entries for fundraising we dropped the lines to have a clean dataset.

# Variable selection
Here we used the scatterplot matrix to find out which variables will be important for the baseline and give us insights about their relevance.  

Looking for correlations between continuous variables, we have dropped some of the discrete variables from the plot as well as combine some others together. Its difficult to display in a readable format in this word document but it can be seen anyway in the notebook.  

First, we differentiated the observations by the “won” factors:

![Variable selection](/assets/02/02_variable_selection_01.png)

Though we can’t see clear linear correlations or patterns, by differentiating by the winners against the losers, we can see how the green and orange observations are very distinct, in some cases.

Some examples:  

- The fund raising of winners is much higher than losers
- Winners reach higher count of victories

Then, we plot only the candidates who won the elections, differentiated by party:

![Variable selection](/assets/02/02_variable_selection_02.png)

Still we can’t see clear linear correlations or patterns, although we notice that there are some differences:

- Democrats winners reach higher count_victories, sign of a bigger political longevity or district stability
- For a higher unemployment rate, Democrats win more than Republicans
- Democrat winners raise slightly less funds than Republican winners

The variable correlation, when the Republicans won, is summarized for Republicans in this table:

![Variable selection](/assets/02/02_variable_selection_03.png)

This is the same table for the Democrats:

![Variable selection](/assets/02/02_variable_selection_04.png)

We can see that the strength of the relationships between Democrats and Republicans is very often similar. In general its weak relationships with only 3 correlation values above 0.7. We also wanted to know about the spread of the data and how the values for Democrats and Republicans compare to each other so we created some boxplots to show this:

![Variable selection](/assets/02/02_variable_selection_05.png)

Above plots were created with function “expl_boxplots(dataframe,variables).

# Baseline model
The baseline will be to predict the response variable “won” per each district.   

The observations of the 2018 election will be our test set, while the previous elections will be our training set.

We start with a simple Logistic Regression model, cross-validated over 5 folds.

For the moment we simply drop the rows containing NaN and see what is the result. The resulting predictions reach an accuracy of 96% over the training set and 94% over the test set. Unfortunately, after dropping the rows containing NaNs, we have only 292 districts.

In this case, though, the number of observations to predict is fixed and known a priori: it’s 435 districts.

That’s important because from its majority will depend the power at the House of Representative.

So, in the next phase we will do imputation of NaN values, to make sure that in each district we will have a prediction.

For the moment, we can add a complementary method, to have a simple prediction for each district, by just taking the max occurring winner per each district before 2018.

If we compare that to the actual election results in 2018, we get 77% accuracy.
