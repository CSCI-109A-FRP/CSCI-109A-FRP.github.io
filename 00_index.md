---
layout: page
title: CSCI-109A
permalink: /
---

_Fulvio Di Napoli, Robert Anglberger and Pierre Mary_

We want to predict the probability for each party to win the 2018 congressional elections and select the one with the highest probability as the winner.

# Problem Statement

Midterm elections, like the one in Novemeber, often swing the power of the politcal parties and
can provide a marker for how a president is performing. Historically, the president’s party loses
seats: on average 27 seats in the House of Representatives (t = 5.09, p < 0.0001) and 3.8 seats in the
Senate (t = 3.21, p = 0.0044) ([data](http://www.presidency.ucsb.edu/data/mid-term_elections.php)). This significant change could be due voter fatigue, a reaction
by the public to the President’s actions, or simply a form of ’regression to the mean’. Essentially,
the political direction of the country often flips or flops based on the result of the midterms.
The goal of this project is to predict the outcomes of the midterm elections: predicting the results
of all federal House of Representative elections in November (Senate seats are a bonus). Any piece
of information can be used to perform this prediction, but the suggested feature set should include
past elections, polling data, and demographic information of congressional districts (see below).
For an example prediction model for 2018, see [fivethirtyeight](https://projects.fivethirtyeight.com/2018-midterm-election-forecast/house/)

# Background

Midterm elections in the United States are the general elections held in November every four years, near the midpoint of a president's four-year term of office.
On the next midterm election day, 6th November 2018, all of the 435 seats in the United States House of Representatives, will compete for election.
Each district will contribute with one seat in the House of Representatives.
The party with a president in the White House has lost an average of 32 seats in the House of Representatives and two in the Senate in every mid-term election since the American Civil War in 1861-65.
Both houses of Congress are currently controlled by the Republican Party, which backs President Donald Trump.
The Democrats think they can win control of the House of Representatives this year by winning a majority of seats.
If the Democrats want to take control of the House by having the most seats, they’re going to have to claim at least 23 from the Republicans.

# Challenge

## Data quality challenges

### Election results, old data formats
As we go back in time, data tend to be on pdf files, from scanned paper documents, rather than on digitized tables. On fec.gov website, the first available digitized data are in 1996, in form of html table.

### Polling
One main issue with polling data is that a lot of congressional districts have little or no polling. To solve this problem we may uses polls from states and districts that have polling, as well as national generic congressional ballot polls, to infer what the polls would say in those that don’t or that have only minimal polling.
From the technical side, we may uses a k-nearest neighbors algorithm to identify similar congressional districts and states based on a variety of demographic, geographic and political factors.
In addition, we may want to weight polls based on their sample size, their recency and their [pollster rating](https://projects.fivethirtyeight.com/pollster-ratings/) given by FiveThirtyEight (which in turn is based on the past accuracy of the pollster, as well as its methodology)

### Fundraising
Data are available by Election Jurisdiction and candidat but not by district (useful for House forecast at district level). To get this segmentation we will need to also get the list of candidates by district.

### No contest
In some states there is no Democrat or Republican representative at the elections. Therefore, it counts as zero votes, even though the party is supported by a certain percentage of the State population. As with polling dat, we should be able to infer the party support, in those cases.

## Other challenges

### Vacancies
Representatives who run for re-election, are more likely to be confirmed. Representatives who do not run for re-election, will leave the seat more open for a change. This aspect will be addressed specifically during our research.
[https://transition.fec.gov/pubrec/electionresults.shtml](https://transition.fec.gov/pubrec/electionresults.shtml) and [https://en.wikipedia.org/wiki/United_States_midterm_election#Historical_record_of_midterm_elections ](https://en.wikipedia.org/wiki/United_States_midterm_election#Historical_record_of_midterm_elections)

### Local vs national-wide factors
The outcome of votation in each district is influenced by both national-wide and local factors.
The popularity of the parties in the nation, should be weighted against local factors: in fact, voters decide whether to show up to the polls and whom to support based both on:  

1. which party they want to control Congress  
2. which of the individual candidates on the ballot they prefer as their representative  

Also voters’ turnout could depend on whether or not they will vote to elect their State Governor, or other local elections.
