---
layout: post
title:  "Uneven rent growth across rental market segments"
date:   2020-01-05 5:55:00 -0700
categories: research
---

# Introduction

![rentGrowth](/images/rrentChange.png)

Any statement mentioning a single growth rate or unaffordability index for all rental units is bound to miss the nuance that not all segments of the rental market grow at an equal pace. As I discuss below, rents for the higher-tier rental market should grow slower than the rest of the market while rents in the low-tier market should grow the fastest. Empirical assessment of rent growth using a 1985-2011 panel of American Housing Survey data confirms this. The real-rent bi-annual growth of the mid-tier rental market is about 5.5% but in the high-tier it's only 3.1% while in the low tier, it's an astonishing 13.1%. Note, these figures look high because they are bi-annual rates. The annual growth rates are approximately half of the bi-annual rates (2.75%, 1.55%, and 6.5% respectively) which are more of the figures we are used to seeing. 


How come we see such large differences in rent growths across the market quality segment? The set of reasons can be quite large. From differential depreciation, construction, and filtering rates to gentrification and types of management. In this post, I focus on the competition between the rental and ownership market. Namely, households residing in the high-tier rental market may and can opt to buy property thus exiting the rental market while residents in low-tier housing are less likely to have such exit privileges. As a result, landlords in the high-tier compete with the ownership market while low-tier landlords only compete amongst themselves. Empirical results support this hypothesis since in times of cheaper homes relative to income, growth rates in high-tier rental are slower while growth rates in the low-tier market are higher. 


# Quick theory

If the upper segment of the housing rental market competes with the ownership market then rents in the upper segment will grow slower than lower tier rents. Moreover, rent growth in the upper tier should be inversely related with home-ownership accessibility. Why? In the classic monopoly, the landlord sets optimal rents at cost plus demand for her location and building. Since it is generally preferable to own rather than rent, landlords renting to higher-income households will be susceptible to their renters buying into the ownership market when conditions are favourible. Consequently, landlords in this market segment compete not only against other landlords but also the ownership market. On the other hand, lower-tier landlords catering to lower-income households who are either unlikely to own or are very slow to own only compete against other landlords so they do not face this added constraint on rent-setting. As a result, higher-tier landlords have less pricing power than their lower-tier counterparts so rents in the higher-end market should grow at a slower pace than rents on the lower end of the market. Moreover, higher-tier rents should grow inversely with home-accessibility because high-tier landlords face a greater risk of renter exodus when the ownership market is easier to buy into. 

# Empirical Approach

I leverage the age-old repeat-sale approach to testing the above hypotheses except, instead of looking at changes in home market price, I look at the change in the unit's real rent over time. Units that start in a relatively low-tier should observe faster growth in rents than units in the upper tier. Assuming vertical differentiation among rental units, I define lower-tier units as any unit whose rent in the first year of the unit's observation rent was in the bottom 75% of rents in that year. Conversely, a high-tier unit is one whose rent started in the top 25% of metro rents. Regressing year-to-year changes in unit rents on a constant and a dummy on high-tier units should show that relative to the constant, high-tier unit rents, on average, grow at a slower rate than lower-tier units. 


Employing a similar strategy but on the sub-sample of units by lower and upper tier, I also regression percent change in unit rents on home-ownership accessibility. I measure home-ownership accessibility as the ratio of median annual house prices to the 75th percentile of occupant renter incomes in the same year. If the price-to-income (pti) ratio is large then the gap between household incomes and home prices is large suggesting that the home-ownership market is more difficult to enter. If the housing market is less accessible then rents in the higher-tier should grow faster so the coefficient on pti should be positive. 


For [data](#code-data-and-stata), I use the 1985-2011 bi-annual American Housing Survey (AHS) panel on units assembled by Rosenthal for his 2014 AER paper on filtering. The data are meant to be nationally representative so include units from over 147 metropolitan areas (SMSAs). To control for this, I include a SMSA fixed effect in the regressions. The repeat-sale approach typically uses change of ownership in houses to register changes in home values but since rents can change without change of occupant, I examine all bi-annual changes in rents and include a dummy for whether the unit retains its previous tenant or not. Per usual, percent change in rents is approximated by the log ratio of a unit's rent in years t+2 and t. The price to income ratio is estimated using the home values of home owners who have lived in their dwelling for less than 10 years and 75th percentile of renter occupant incomes. All rents have been deflated by the US average all-item CPI (though all-item less shelter is a more standard approach). I exclude units with bi-annual growth over 100%. 

# Results

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead><tr><td colspan="3">dependent variable.</td></tr></thead>
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>rent change</th>
      <th>high-tier rent change</th>
    </tr>
  </thead>
  <tfoot><tr><td colspan="3">p-values in parentheses.</td></tr></tfoot>
  <tbody>
    <tr>
      <th>Intercept</th>
      <td>0.046 (0.050)</td>
      <td>-0.027 (0.444)</td>
    </tr>
    <tr>
      <th>higher-tier</th>
      <td>-0.026 (0.000)</td>
      <td></td>
    </tr>
    <tr>
      <th>low-tier</th>
      <td>0.080 (0.000)</td>
      <td></td>
    </tr>
    <tr>
      <th>pti</th>
      <td></td>
      <td>0.002 (0.035)</td>
    </tr>
    <tr>
      <th>same HH</th>
      <td>-0.027 (0.000)</td>
      <td>-0.006 (0.022)</td>
    </tr>
    <tr>
      <th>nObs</th>
      <td>145237</td>
      <td>37118</td>
    </tr>
    <tr>
      <th>R^2 adj</th>
      <td>0.02</td>
      <td>0</td>
    </tr>
    <tr>
      <th>condNum</th>
      <td>435</td>
      <td>1739</td>
    </tr>
  </tbody>
</table>
</div>

Results concur with expectations outlined [above](#quick-theory). In the table above, in the rent change column, I regress bi-annual changes in real rents on a higher-tier unit dummy as defined in [empirical section](#empirical-approach). The intercept suggests that market rents for lower-tier units grew at an average of 7% every two years and that higher-tier unit rents grew at 2% (or 5 percentage points less). This confirms the expectation that higher-tier unit rents grow at a slower pace than higher-tier units. 

In the high-tier rent change column, I regress rent changes of units that started in the top 25% on the price-to-income (pti) ratio. Again, as expected, in years that the ownership market is more inaccessible, rents grow faster suggesting competition between the ownership and high-tier rental market. The same does not hold when I regress on changes among units in the bottom 75%. 

# Discussion

A potential concern is regression to the mean. Namely, as time goes on, high-tier units converge in quality through deterioration and replacement toward lower-tier units so rents should also converge toward the average. This will bias the coefficient on higher-tier units downward. Conversely, low-tier units deemed no longer qualified to be occupied by either the market or officials fall out. This unit survival is likely to lends to an upward bias among coefficents on low-tier units.   


Renter property prices do not remain dormant through a property's lifetime. If the property changes hands and home prices are going up, ie pti is growing, then the rental property will also cost more. In the data, I observe neither change of ownership nor changes in the property's value which may endogenize pti. 


One rather obvious critique is that lower-tier rents grow off a smaller base value so they will inherently grow at a faster pace. Normalizing the base rent by a unit's initial rent wouldn't change percent changes so I am not sure how to get around this concern. 


A key assumption in all repeat-sales models is that both unit and neighborhood characteristics remain constant across time. This is unlikely to be the case. Nor are the valuations of these characteristics are likely to remain constant.

# Code Data and STATA

The notebook used to wrangle the data and estimate the models can be found on [Github](https://github.com/kiwiPhrases/repeatRents/blob/master/repeatRents.ipynb). Additionally, because I use Rosenthal data (and code) from his 2014 AER paper, *Are Private Markets and Filtering a Viable Source of Low-Income Housing? Estimates from a "Repeat Income" Model*, I also post python [code](https://github.com/kiwiPhrases/repeatRents/blob/master/changePaths.py) used to change the paths in the STATA program files so that Rosenthal's data can be recreated. As a side note, I used some old STATA 13 installation I found on my laptop to run his code but I also tried on machines with newer STATA. Using newer STATA runs into countless problems which I could not debug though, I'm guessing if you use STATA, then you're used to such incumbrances. 