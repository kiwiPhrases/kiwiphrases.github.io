---
layout: post
title:  "How much more does that neighborhood rent for?"
date:   2019-06-20 5:55:00 -0700
categories: research
---
# Intro

In  *Evicted*, Matthew Desmond reports that a two-bedroom unit in one of Milwaukee's low-income Zip codes costs \$550 while in a middle-class neighborhood a two-bedroom unit fetches for \$750 (Desmond pg151). This suggests that the rental prices charged to some of the low-income tenants in underserved Milwaukee, WI neighborhoods are near the rents that are charged to higher income tenants residing in neighborhoods and dwellings of higher quality. This is a rather interesting phenomenon because, at the extreme, in a competitive market, two otherwise identical units with the same cost of operation but of different quality should not fetch for the same price (Olsen 1969). Desmond's observation, of course, does not suggest that we have two units of different quality selling for the same price but the price difference striked me to be a bit small.


How prevalent is Desmond's observation in Milwaukee and what does the more general quality to rent gradient look like? The simplest way to test this would be to regress rents on a quality index. Alas, the ideal representative survey on a unidimensional quality-index vs rents does not exist. The most comprehensive survey on unit quality and rents is the American Housing Survey (AHS) which publishes unit-level data on rents, unit quality, and its occupants for select metropolitan areas every two years. The [2015 AHS](https://www.census.gov/programs-surveys/ahs/data/2015/ahs-2015-public-use-file--puf-/2015-ahs-metropolitan-puf-microdata.html) Metropolitan sample happens to contain the Core-Based-Statistical Area (CBSA), or the MSA in common parlance, for Milwaukee. For details on the data, you can check its [documentation](https://www.census.gov/content/dam/Census/programs-surveys/ahs/tech-documentation/2015/Getting%20Started%20with%20the%20AHS%20PUF.pdf). 


However, even with the AHS, gaps in the measurement of quality remain meaning that regardless how detailed of a hedonic we run, it is almost guaranteed that it won't meet exogeneity assumption due to household sorting and the fact that a unit in a poor neighborhood is unlikely to have granite kitchen counters, a smart fridge, and an open-floor design (which are just some of the things not measured by AHS). To circumvent this, instead of measuring quality, I lean on the Bayer and McMillan papers to exploit the fact that households sort by socio-economic status into unit quality. Thus, assuming that higher income households sort into higher quality units, examining rents against the occupant's income provides a convenient one-dimensional quality-index proxy that provides a glimpse into the relationship between unit quality and rent spreads.

# Milwaukee 

Let's dive into some of the prelim results. I tabulate the share of households in each income bracket that pay rent in various rent brackets (quintiles). Because we wish to compare apples to apples and of 915 renter observations, 423 are 2 bedroom units, below I tabulate households versus rents for households that occupy 2 bedroom units. 

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
  <thead>
    <tr style="text-align: right;">
      <th>rent bins</th>
      <th>3.999 - 580.0</th>
      <th>580.0 - 650.0</th>
      <th>650.0 - 780.0</th>
      <th>780.0 - 960.0</th>
      <th>960.0 - 2600.0</th>
    </tr>
    <tr>
      <th>income bins</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39.999 - 11000.0</th>
      <td>0.50</td>
      <td>0.18</td>
      <td>0.20</td>
      <td>0.05</td>
      <td>0.07</td>
    </tr>
    <tr>
      <th>11000.0 - 17780.0</th>
      <td>0.39</td>
      <td>0.22</td>
      <td>0.17</td>
      <td>0.12</td>
      <td>0.10</td>
    </tr>
    <tr>
      <th>17780.0 - 23580.0</th>
      <td>0.21</td>
      <td>0.31</td>
      <td>0.19</td>
      <td>0.14</td>
      <td>0.14</td>
    </tr>
    <tr>
      <th>23580.0 - 28980.0</th>
      <td>0.24</td>
      <td>0.21</td>
      <td>0.19</td>
      <td>0.21</td>
      <td>0.14</td>
    </tr>
    <tr>
      <th>28980.0 - 35000.0</th>
      <td>0.19</td>
      <td>0.32</td>
      <td>0.19</td>
      <td>0.13</td>
      <td>0.17</td>
    </tr>
    <tr>
      <th>35000.0 - 40000.0</th>
      <td>0.17</td>
      <td>0.20</td>
      <td>0.29</td>
      <td>0.15</td>
      <td>0.20</td>
    </tr>
    <tr>
      <th>40000.0 - 54760.0</th>
      <td>0.13</td>
      <td>0.18</td>
      <td>0.23</td>
      <td>0.31</td>
      <td>0.15</td>
    </tr>
    <tr>
      <th>54760.0 - 68840.0</th>
      <td>0.14</td>
      <td>0.19</td>
      <td>0.29</td>
      <td>0.21</td>
      <td>0.17</td>
    </tr>
    <tr>
      <th>68840.0 - 89000.0</th>
      <td>0.12</td>
      <td>0.07</td>
      <td>0.21</td>
      <td>0.30</td>
      <td>0.30</td>
    </tr>
    <tr>
      <th>89000.0 - 381500.0</th>
      <td>0.02</td>
      <td>0.00</td>
      <td>0.12</td>
      <td>0.31</td>
      <td>0.55</td>
    </tr>
  </tbody>
</table>
</div>

Above, for example, we see that 19\% of households annually earning \$29,980-35,000 pay rent in the \$650-780 range. For a more poignant perspective, consider the fact that the median renter income in Milwaukee is \$32,000 and that 17\% of households earning \$17,780-\$23,580 pay the same rent as 23\% of households earning \$40,000-54,800. In fact, regardless which elements we compare above and below the median income, a substantial portion of households below the median income pay rents in the same ranges as households earning 2-4 times as much as them. Households of different incomes could be renting units of the same quality but that is unlikely since numerous studies empirically confirm the observation that higher income households will sort into higher quality units and neighborhoods (Bajari and Kahn 2005, Bayer and McMillan 2005, Glaeser, Kahn, and Rappaport 2008). These cross tabulations between income and rents are not a perfect indication of different quality units being rented out for a similar price but are suggestive thereof.


For an alternative look into the spread of rents against unit quality, I regress household income (HINCP) against rents after controlling for unit type, size, length of tenure, year built and a few other controls for units in the Milwaukee CBSA. Note that in the table above, we see that there are reported monthly rents as low \$4 and annual incomes as low as \$30. In the data, it is difficult to discern whether these are just extreme outliers or data entry errors so I regress income on rents on the whole sample and a sample where 5% and 10% of top and bottom rents are trimmed off. Moreover, my analysis crucially relies on the assumption that households sort into quality by income so it makes sense to restrict the population of interest to working-age households aged 18-65. Below, we see the results:

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
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>noRestr</th>
      <th>ageRestr</th>
      <th>rentTrim10AgeRestr</th>
      <th>rentTrim5AgeRestr</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>HINCP</th>
      <td>0.0027 (0.000)</td>
      <td>0.0027 (0.000)</td>
      <td>0.0014 (0.000)</td>
      <td>0.0019 (0.000)</td>
    </tr>
    <tr>
      <th>R^2 adj</th>
      <td>0.291076</td>
      <td>0.353575</td>
      <td>0.350016</td>
      <td>0.348711</td>
    </tr>
    <tr>
      <th>nObs</th>
      <td>894</td>
      <td>710</td>
      <td>608</td>
      <td>658</td>
    </tr>
  </tbody>
</table>
</div>

Above, we see that in Milwaukee, a household earning \$50,000 more will only pay \$70 more dollars in monthly rent for higher quality neighborhood and unobserved unit characteristics as indicated by the coefficient on Household Income (HINCP). Because I do not have data on location or a perfect measure of unit quality, I assume
that a household annually earning \$70,000, for example, will not reside in the same unit as a household
earning \$20,000. Given sorting by income, the additional quality that the wealthier household gets costs
that household only \$70 more in rent, ceteris paribus. Of course, we also see that the results are sensitive to the choice of data subset. Upon further examination, I find that the sensitivity primarily stems from a pocket of the extremely low rent units. Thus, the size of the rent spread across household incomes crucially depends on whether we believe that rents below \$200 or \$400 (5% and 10% trims respectively) are representative of the wider market or not. Regressing log rents against log incomes or using quantile regressions yield very similar results. 




I cross-check the above findings replacing income with explicit measures of quality (or disamenities) and regressing this vector of quality indicators on rents. 

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>no sample restrictions</th>
      <th>working age</th>
      <th>5\% rents trimmed</th>
      <th>10\% rents trimmed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>badSchools</th>
      <td>-34.9406 (0.442)</td>
      <td>-32.3043 (0.414)</td>
      <td>-25.7121 (0.328)</td>
      <td>25.9941 (0.243)</td>
    </tr>
    <tr>
      <th>feltCold</th>
      <td>-104.8648 (0.016)</td>
      <td>-70.4450 (0.074)</td>
      <td>-32.6242 (0.214)</td>
      <td>-14.8569 (0.504)</td>
    </tr>
    <tr>
      <th>lotCrime</th>
      <td>-22.4381 (0.592)</td>
      <td>-7.7089 (0.834)</td>
      <td>2.3938 (0.924)</td>
      <td>-26.9789 (0.209)</td>
    </tr>
    <tr>
      <th>moderatelyInadequate</th>
      <td>23.0876 (0.627)</td>
      <td>-18.7028 (0.672)</td>
      <td>-16.5627 (0.570)</td>
      <td>-17.7034 (0.469)</td>
    </tr>
    <tr>
      <th>notoilet</th>
      <td>24.0687 (0.734)</td>
      <td>53.5934 (0.395)</td>
      <td>33.2086 (0.423)</td>
      <td>25.5587 (0.460)</td>
    </tr>
    <tr>
      <th>roaches</th>
      <td>-95.8533 (0.123)</td>
      <td>-96.2838 (0.078)</td>
      <td>-101.7763 (0.005)</td>
      <td>-66.1337 (0.031)</td>
    </tr>
    <tr>
      <th>rodents</th>
      <td>68.2529 (0.197)</td>
      <td>18.7040 (0.689)</td>
      <td>57.3585 (0.066)</td>
      <td>22.9721 (0.383)</td>
    </tr>
    <tr>
      <th>severelyInadequate</th>
      <td>242.6129 (0.026)</td>
      <td>135.5757 (0.140)</td>
      <td>101.7103 (0.092)</td>
      <td>28.2483 (0.583)</td>
    </tr>
    <tr>
      <th>numObs</th>
      <td>878</td>
      <td>695</td>
      <td>645</td>
      <td>597</td>
    </tr>
  </tbody>
</table>
</div>

In the above table, we see that regardless which sub-sample we use, measures of quality in the AHS are not significantly related to rents in Milwaukee. Running an F-Test on joint significance of the quality indicators for the 'no restrictions' and '10% rents trimmed' samples yield p-values of 70% and 60% respectively implying that the variables are not jointly significant either. This supports the interpretation that unit quality does not have a great impact on the price of the unit in Milwaukee. 

## Beyond Milwaukee 

How does Milwaukee compare to rent spreads across income in other CBSAs? I estimate the regression on 10% trimmed rents on working-age household incomes and rents for 25 CBSAs available in the AHS 2015 National and Metropolitan samples and report the results below:

<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="2" halign="left">levels</th>
      <th colspan="2" halign="left">logs</th>
    </tr>
    <tr>
      <th></th>
      <th>HINCP</th>
      <th>invMills</th>
      <th>HINCP</th>
      <th>invMills</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Houston-The Woodlands-Sugar Land, TX</th>
      <td>0.0002 (0.000)</td>
      <td>135.0361 (0.000)</td>
      <td>0.0605 (0.000)</td>
      <td>0.1304 (0.000)</td>
    </tr>
    <tr>
      <th>Cincinnati, OH-KY-IN</th>
      <td>0.0002 (0.328)</td>
      <td>76.7584 (0.001)</td>
      <td>0.0457 (0.000)</td>
      <td>0.1051 (0.005)</td>
    </tr>
    <tr>
      <th>Cleveland-Elyria, OH</th>
      <td>0.0007 (0.002)</td>
      <td>79.5145 (0.003)</td>
      <td>0.0287 (0.021)</td>
      <td>0.1328 (0.002)</td>
    </tr>
    <tr>
      <th>Raleigh, NC</th>
      <td>0.0008 (0.000)</td>
      <td>80.5466 (0.004)</td>
      <td>0.0415 (0.000)</td>
      <td>0.0914 (0.004)</td>
    </tr>
    <tr>
      <th>Atlanta-Sandy Springs-Roswell, GA</th>
      <td>0.0011 (0.000)</td>
      <td>107.0223 (0.002)</td>
      <td>0.0595 (0.000)</td>
      <td>0.1212 (0.005)</td>
    </tr>
    <tr>
      <th>Phoenix-Mesa-Scottsdale, AZ</th>
      <td>0.0011 (0.000)</td>
      <td>25.8471 (0.457)</td>
      <td>0.0392 (0.000)</td>
      <td>0.0427 (0.291)</td>
    </tr>
    <tr>
      <th>Memphis, TN-MS-AR</th>
      <td>0.0011 (0.000)</td>
      <td>60.6889 (0.003)</td>
      <td>0.0497 (0.000)</td>
      <td>0.0936 (0.004)</td>
    </tr>
    <tr>
      <th>Dallas-Fort Worth-Arlington, TX</th>
      <td>0.0013 (0.000)</td>
      <td>7.8705 (0.814)</td>
      <td>0.0535 (0.000)</td>
      <td>0.0200 (0.611)</td>
    </tr>
    <tr>
      <th>Milwaukee-Waukesha-West Allis, WI</th>
      <td>0.0013 (0.000)</td>
      <td>23.5242 (0.284)</td>
      <td>0.0548 (0.000)</td>
      <td>0.0454 (0.108)</td>
    </tr>
    <tr>
      <th>Denver-Aurora-Lakewood, CO</th>
      <td>0.0014 (0.000)</td>
      <td>16.1274 (0.710)</td>
      <td>0.0680 (0.000)</td>
      <td>0.0338 (0.404)</td>
    </tr>
    <tr>
      <th>Miami-Fort Lauderdale-West Palm Beach, FL</th>
      <td>0.0016 (0.000)</td>
      <td>109.6278 (0.050)</td>
      <td>0.0530 (0.000)</td>
      <td>0.1267 (0.021)</td>
    </tr>
    <tr>
      <th>Kansas City, MO-KS</th>
      <td>0.0016 (0.000)</td>
      <td>31.9566 (0.149)</td>
      <td>0.0874 (0.000)</td>
      <td>0.0434 (0.168)</td>
    </tr>
    <tr>
      <th>Washington-Arlington-Alexandria, DC-VA-MD-WV</th>
      <td>0.0016 (0.000)</td>
      <td>347.9056 (0.000)</td>
      <td>0.1547 (0.000)</td>
      <td>0.2862 (0.000)</td>
    </tr>
    <tr>
      <th>New Orleans-Metairie, LA</th>
      <td>0.0017 (0.000)</td>
      <td>188.1493 (0.000)</td>
      <td>0.1141 (0.000)</td>
      <td>0.2511 (0.000)</td>
    </tr>
    <tr>
      <th>Portland-Vancouver-Hillsboro, OR-WA</th>
      <td>0.0018 (0.000)</td>
      <td>14.6210 (0.660)</td>
      <td>0.0739 (0.000)</td>
      <td>0.0330 (0.330)</td>
    </tr>
    <tr>
      <th>Pittsburgh, PA</th>
      <td>0.0020 (0.000)</td>
      <td>37.8121 (0.264)</td>
      <td>0.0954 (0.000)</td>
      <td>0.0718 (0.220)</td>
    </tr>
    <tr>
      <th>Riverside-San Bernardino-Ontario, CA</th>
      <td>0.0021 (0.000)</td>
      <td>189.3232 (0.000)</td>
      <td>0.0829 (0.000)</td>
      <td>0.1799 (0.000)</td>
    </tr>
    <tr>
      <th>Detroit-Warren-Dearborn, MI</th>
      <td>0.0022 (0.000)</td>
      <td>114.5310 (0.000)</td>
      <td>0.0749 (0.000)</td>
      <td>0.1420 (0.002)</td>
    </tr>
    <tr>
      <th>Philadelphia-Camden-Wilmington, PA-NJ-DE-MD</th>
      <td>0.0022 (0.000)</td>
      <td>163.7015 (0.000)</td>
      <td>0.0936 (0.000)</td>
      <td>0.2507 (0.000)</td>
    </tr>
    <tr>
      <th>Chicago-Naperville-Elgin, IL-IN-WI</th>
      <td>0.0025 (0.000)</td>
      <td>4.5575 (0.918)</td>
      <td>0.1376 (0.000)</td>
      <td>-0.0015 (0.977)</td>
    </tr>
    <tr>
      <th>Los Angeles-Long Beach-Anaheim, CA</th>
      <td>0.0025 (0.000)</td>
      <td>77.4644 (0.185)</td>
      <td>0.1019 (0.000)</td>
      <td>0.1093 (0.063)</td>
    </tr>
    <tr>
      <th>Boston-Cambridge-Newton, MA-NH</th>
      <td>0.0026 (0.000)</td>
      <td>33.2519 (0.616)</td>
      <td>0.1889 (0.000)</td>
      <td>0.0674 (0.347)</td>
    </tr>
    <tr>
      <th>San Francisco-Oakland-Hayward, CA</th>
      <td>0.0026 (0.000)</td>
      <td>223.7342 (0.013)</td>
      <td>0.1690 (0.000)</td>
      <td>0.1142 (0.080)</td>
    </tr>
    <tr>
      <th>Seattle-Tacoma-Bellevue, WA</th>
      <td>0.0028 (0.000)</td>
      <td>13.2359 (0.759)</td>
      <td>0.1243 (0.000)</td>
      <td>0.0191 (0.647)</td>
    </tr>
    <tr>
      <th>New York-Newark-Jersey City, NY-NJ-PA</th>
      <td>0.0038 (0.000)</td>
      <td>43.9854 (0.519)</td>
      <td>0.1535 (0.000)</td>
      <td>0.0291 (0.692)</td>
    </tr>
  </tbody>
</table>
</div>

Above, we see that higher rent spreads across incomes seem to correspond with wealthier, coastal cities. This is expected not least because it is more difficult to purchase homes in top MSAs so households rent across a higher range of incomes. A simple way to verify this is to regress the above coefficient estimates on incomes against Saiz's measures of supply elasticity and the Wharton Regulatory Index. Predictably, MSAs with lower supply elasticities and more housing regulations also have higher spreads across rents. Interestingly, other measures such as income spread and average income are not correlated with rent spreads. 


# Discussion

Milwaukee results lend themselves to a rather interesting phenomenon. First, if a household earning \$20k a year wishes to live in a neighborhood among households earning \$70k, it should expect to cough up an additional $70-$100 a month for rent. A \$20k household takes home about $1.67k a month so a $70-$100 represents spending an additional 4%-6% of its monthly income on rent. It does not seem like much so why would low-income households continue to reside in low-income neighborhoods when they could reside in middle-income neighborhoods? This is a rather complex question. First, it assumes that low-income households would want to spend the additional $70-100 a month on housing instead of other goods which need not hold. Second, it assumes that low-income households know that for an additional $70-$100 they could buy into a nicer neighborhood and unit. This may not hold because low-income households may have limited information regarding the rent to quality schedule due to the way they find housing (Something I will detail in another post). Finally, it assumes that low-income households can buy into the middle-income market. Landlords catering to middle-income households can deny low-income households access to higher-tier units through background and credit checks, discrimination, risk-premiums, and fear of the endogenous impact on perceived neighborhood effects and; hence, its price. These posited factors lead me to think that one potential cause behind the low rent spreads could be a segmented rental market in which there exists a barrier for low-income households to buy into higher quality units even if they may be able to afford it. 

In another post, I will also show how landlord market power in Milwaukee seems to correspond with renter shares and prevalence of low-quality and cheap properties. This may suggest that another channel behind the low-spread is landlord market power in lower-income neighborhoods. 

The above postulations suggest why rents would be high for the low end but low rent spreads require some dampening from the top too. This table for MSAs in the US provides some potential insight into that. From the higher supply elasticities in low-spread areas, I suspect that ownership is more accessible to middle-income households. For example, in SF, even if you earn $250k, it will take you a few years to accumulate the requisite downpayment for a 1.5 million dollar condo whereas in Milwaukee and Texas, the barrier to home ownership is much lower. As a result, the household in SF does not really face a tenure choice problem, it must rent. On the other hand, middle-income households in higher-elasticity areas do face the option to choose tenure so landlords catering to these households compete against for-sale units and; thus, may not be able to charge higher rents. Low-income households, on the other hand, who have little hope to own a unit have no choice but to rent so landlords catering to them may leverage the absence of an outside option and charge higher prices. 

Please note that these results are preliminary and are pending further analysis, assumptions checks, and much more. It is only intended as a quick demonstration of concept.

