---
layout: page
title: Data Crawling
permalink: /data-crawling
---
# Part 1 - Data crawling

The first challenge in order to predict the 2018 house results was to obtain publically available historical data from various public sources. These data are:  

- Historical congressional election results for all the districts that exists in 2008.  
- National unemployement rates
- Presidental job approval
- House seats repartition
- Candidate fundraising

You can find the complete code for this step on the GitHub repository, [harvard_E-109A_final/01_crawler.ipynb](https://github.com/pmary/harvard_E-109A_final/blob/master/01_crawler.ipynb)

## 1.1 Historical congressional election results
We got the midterm house results by crawling two sources: [Wikipedia.org](https://wikipedia.org) and [ballotpedia.org](https://ballotpedia.org).

### 1.1.1 Wikipedia
First, we extracted the list of all 2016 districts from this page: [2016 United States House of Representatives elections](https://en.wikipedia.org/wiki/2016_United_States_House_of_Representatives_elections)  

Then, we extracted the congressional election results from each of these district pages.

### 1.1.2 Ballotpedia
The 2018 house results was not available on Wikipedia so we had to found another source: Ballotpedia.
On this websites we was able to retrieve the historical results by district from 2012 to 2018.  

## 1.2 National unemployement rates
We downloaded the national unemployement rate from 1948 to 2018 by month from the [Bureau of Labor Statistics](https://data.bls.gov) website.  

## 1.3 Presidental job approval
We was able to scrap the informations used by Gallup to build [this page](https://news.gallup.com/interactives/185273/presidential-job-approval-center.aspx).

## 1.4 House seats repartition
We extracted the number of seats by party by year from the following Wikipedia page: [List of United States House of Representatives elections, 1856–present](https://en.wikipedia.org/wiki/List_of_United_States_House_of_Representatives_elections,_1856%E2%80%93present)

## 1.5 Candidate fundraising
We got the candidate fundraising data from from 2009 to 2018 on followthemoney.org.  
The candidate names are not formated the same way as our data from Wikipedia and Ballotpedia so used a fuzzy search algorithm to match them

# Part 2 - Detect and manually fix errors and add missing results
Wikipedia is a great collaborative knowledge base but sometime it lakes of structure.
This is why in some edge-cases, the crawler didn't do a good job or the data. On the other hand, the data was particularly messy sometimes. For example, we found that for some elections there was more than 1 winner or even none or duplicate candidates. We had to write some test to detect such errors and then we manually fixed them.  

# Part 3 - Data derivation
With the data at hand, we were able to derivate the following new predictors:  

- Whether this is a presidential year or not
- Whether the president can stand for re-election
- The year of the first time an incumbent has been elected
- The number of past victories of a candidate
