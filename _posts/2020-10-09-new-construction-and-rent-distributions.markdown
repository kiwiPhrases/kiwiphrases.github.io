---
layout: post
title:  "New construction and housing affordability"
date:   2020-10-14 5:55:00 -0700
categories: research
---


<img style="float: left;" src="./images/building_in_la.jpg" alt="building_map" width="300">

# Introduction

Not too long ago, I stayed up passed midnight on a planning commission hearing for a project in Alhambra where an innumerable number of people called-in or wrote that construction of a new project will reduce the stock of affordable housing. Is this fear founded on any empirical evidence? 

In brief, it's complicated. Results rather robustly indicate that construction of new units adds to the stock of units renting for $1500+ per month which is not surprising given that's what developers tend to build. There is some evidence that construction of new units also leads to some, albeit limited, loss of affordable units. 

# Approach and results

I use American Community Survey(ACS) Block Group-level data on the number of units at various rent tiers and regress it against the number of units that were added according to LA City's building permit data. 

Since ACS data come in 5-year periods, I compare unit changes between ACS surveys ending in 2012 and 2017 to the total number of units added between 2013 and 2017 according to the building permit data. I perform a similar exercise by comparing unit changes between the 2017 and 2018 ACS surveys to the number of units added in 2018 (in effect, this compares rent changes between 2014 and 2018 provided uniform sample-weighting by the Census). Note, I only use Block Groups that had some renters and got at least 1 new unit built. 


Before I describe the results, I should mention that I transformed some of the building permit data variables to reel in some of the tails. I could transform number of units lost to building demolitions and number of units in new buildings because values don't change signs. Building additions and alterations take on both positive and negative values making it more difficult to normalize those values without recourse to standardization.  I mention this because transformations heavily impact estimates. I include the untransformed results at the [bottom](#untransformed-variables).


The results in the table below suggest that construction of new units is related to the loss of units renting between $600-$1,000 per month. For a sense of magnitude, the estimates imply that 3 newly built units would lead to a loss of 3 $600-$799 units and a loss of 2 units in the $800-$1,000 range. 100 new units would lead to a loss of about 14 and 10 units respectively. 

Results also suggest that the construction of new units leads to more units renting for $1,500+. Of course, this result is not surprising given that developers in LA City tend to build units catering to middle and high-income households (at least until JJJ passed but that's another topic). 

Two other things pop out. Regardless of variable transformations, construction of new units, demolition and other activities, explains up to about 2% of variation in $600-$1,000 unit changes but up to 40% of the $2,0000 or More unit changes. This suggests that even if construction of new units does cause a decrease in more affordable units, it is but a very small factor. 

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
      <th>Rent600to799</th>
      <th>Rent800to999</th>
      <th>Rent1000to1249</th>
      <th>Rent1500to1999</th>
      <th>Rent2000orMore</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bldg_Addition</th>
      <td>-0.267***</td>
      <td>0.238*</td>
      <td>-0.183</td>
      <td>0.092</td>
      <td>2.766***</td>
    </tr>
    <tr>
      <th>Bldg_Alter_Repair</th>
      <td>0.053</td>
      <td>-0.157*</td>
      <td>0.068</td>
      <td>0.465***</td>
      <td>-0.354***</td>
    </tr>
    <tr>
      <th>Bldg_Demolition</th>
      <td>-0.004</td>
      <td>0.092</td>
      <td>0.38***</td>
      <td>-0.219</td>
      <td>-0.515***</td>
    </tr>
    <tr>
      <th>Bldg_New</th>
      <td>0.004</td>
      <td>-0.001</td>
      <td>0.006</td>
      <td>-0.049***</td>
      <td>0.176***</td>
    </tr>
    <tr>
      <th>r_squared</th>
      <td>0</td>
      <td>0</td>
      <td>0.01</td>
      <td>0.03</td>
      <td>0.4</td>
    </tr>
    <tr>
      <th>adjusted_r</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.03</td>
      <td>0.4</td>
    </tr>
    <tr>
      <th>f-stat</th>
      <td>1.83</td>
      <td>1.55</td>
      <td>2.4</td>
      <td>12.3</td>
      <td>262.77</td>
    </tr>
    <tr>
      <th>n_obs</th>
      <td>1550</td>
      <td>1550</td>
      <td>1550</td>
      <td>1550</td>
      <td>1550</td>
    </tr>
  </tbody>
</table>
</div> 

Estimates for the 2017-2018 periods show similar results but with smaller coefficient magnitudes. I included these results at the bottom of the page. 

# Conclusion

So what does this mean for those who call the planning commissioners and city councils to say that new development will make the neighborhood less affordable? If we brush aside the rather large volume model and data issues I enumerate below, one could say that these callers do have some weak empirical backing but, overall, it is a tenuous relationship. 

# The Data

I obtain data on all building permits issued in LA City between 2013 and 2020 from LA City's Open Data [portal](https://data.lacity.org/A-Prosperous-City/Building-Permits/nbyu-2ha9/data). I use the location, type, valuation, and number of units(amongst much more) for residential projects that were issued a building permit. 


Data on rent distributions at the Census Block Group level were obtained from the 5-year American Community Survey(ACS)s for year spans starting with 2008-2012, 2013-2017, and ending with 2014-2018.  Rent data  were spatially matched to building permit data using Census TIGER shapefiles. 

# Iffy data fidelity

There are a few most or less obvious concerns with the data. First, I am comparing nominal rents, not real rents because it is a tad difficult to deflate aggregated ACS data though given that inflation has been fairly low over the past decade, that isn't much of a concern. Second, it seems building permits don't quite account for the full number of new units in a given area. LA City releases a Housing Annual Progress Report. In the [2019 Report](https://planning.lacity.org/odocument/8204713d-6574-46b6-b41c-6f6311c247f6/LosAngeles2019_Summary.pdf), we see that LA City annually approved a minimum of 13,696 units and a maximum of 22,258 units between 2014 and 2019. The building permit data suggest a min of 10,572 and a max of 12,777 in the same period. Moreover, the time series pattern between the two datasets don't match. The totals in the data do not match either. According to the Housing Progress Report, LA City produced 82,097 units while the permits data suggest 57,165. Is ACS data better? In the same period, ACS says that LA City produced 51,772 units.

Given that all data sources seem to underestimate the amount of development that's happening in LA City, the estimated coefficients likely understate the true effects. 

# Other issues

ACS rent distributions are not disaggregated at the price and unit-size level. Since larger units tend to rent for higher prices, if a developer builds 3 bedroom units in a neighborhood of 1-bedroom apartments then even if the larger new units are priced comparably to existing units, we'll still see a shift in the rent distribution. 

Then there are endogeneity issues. Developers don't just randomly pick neighborhood in which they build - they typically perform careful analysis to make sure that the neighborhoods they'll build are desirable places to live in. As a result, it is completely possible that rents in a neighborhood don't go up because of new construction but rather because of the already-existing amenities that both rents and developers are responding to. 

Rent control is another issue. Even if local amenity-improvements would lead to higher land values, rent in rent-controlled units would be locked in; thereby, dampening the effect of new development. Since I don't observe rent-controlled units, I would assume that the above estimates understate the relationship between new units and rents in an environment without rent control.

# 2017-2018 results
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>static_index</th>
      <th>dynamic_index</th>
      <th>Rent600to799</th>
      <th>Rent1500to1999</th>
      <th>Rent2000orMore</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bldg_Addition</th>
      <td>0.01</td>
      <td>0.013</td>
      <td>-0.005</td>
      <td>0.005</td>
      <td>-0.002</td>
    </tr>
    <tr>
      <th>Bldg_Alter_Repair</th>
      <td>0.004</td>
      <td>0.003</td>
      <td>-0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Bldg_Demolition</th>
      <td>0.001</td>
      <td>-0.003</td>
      <td>-0.0</td>
      <td>0.002*</td>
      <td>-0.002**</td>
    </tr>
    <tr>
      <th>np.log1p(Bldg_New)</th>
      <td>0.009*</td>
      <td>0.007</td>
      <td>-0.002</td>
      <td>-0.001</td>
      <td>0.005*</td>
    </tr>
    <tr>
      <th>r_squared</th>
      <td>0.01</td>
      <td>0.01</td>
      <td>0</td>
      <td>0.01</td>
      <td>0.02</td>
    </tr>
    <tr>
      <th>f-stat</th>
      <td>1.07</td>
      <td>1.29</td>
      <td>0.51</td>
      <td>0.98</td>
      <td>3.08</td>
    </tr>
    <tr>
      <th>n_obs</th>
      <td>602</td>
      <td>589</td>
      <td>602</td>
      <td>602</td>
      <td>602</td>
    </tr>
  </tbody>
</table>

#  Untransformed variables

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Rent600to799</th>
      <th>Rent800to999</th>
      <th>Rent1000to1249</th>
      <th>Rent1500to1999</th>
      <th>Rent2000orMore</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bldg_Addition</th>
      <td>-0.267***</td>
      <td>0.238*</td>
      <td>-0.183</td>
      <td>0.092</td>
      <td>2.766***</td>
    </tr>
    <tr>
      <th>Bldg_Alter_Repair</th>
      <td>0.053</td>
      <td>-0.157*</td>
      <td>0.068</td>
      <td>0.465***</td>
      <td>-0.354***</td>
    </tr>
    <tr>
      <th>Bldg_Demolition</th>
      <td>-0.004</td>
      <td>0.092</td>
      <td>0.38***</td>
      <td>-0.219</td>
      <td>-0.515***</td>
    </tr>
    <tr>
      <th>Bldg_New</th>
      <td>0.004</td>
      <td>-0.001</td>
      <td>0.006</td>
      <td>-0.049***</td>
      <td>0.176***</td>
    </tr>
    <tr>
      <th>r_squared</th>
      <td>0</td>
      <td>0</td>
      <td>0.01</td>
      <td>0.03</td>
      <td>0.4</td>
    </tr>
    <tr>
      <th>adjusted_r</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.03</td>
      <td>0.4</td>
    </tr>
    <tr>
      <th>f-stat</th>
      <td>1.83</td>
      <td>1.55</td>
      <td>2.4</td>
      <td>12.3</td>
      <td>262.77</td>
    </tr>
    <tr>
      <th>n_obs</th>
      <td>1550</td>
      <td>1550</td>
      <td>1550</td>
      <td>1550</td>
      <td>1550</td>
    </tr>
  </tbody>
</table>