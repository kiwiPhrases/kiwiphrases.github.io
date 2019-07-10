---
layout: post
title:  "Tools for Estimating and Reporting Multiple Specifications"
date:   2019-07-09 5:55:00 -0700
categories: code
---

# Introduction

You just estimated a regression, got [results](#full-table-yikes), and now either your model or your colleague suggests you change or elaborate on the specification. Repeat that a few times and reporting results quickly becomes a headache. It simply is not practical or palatable to report every single [full regression table]((#full-table-yikes)). You begin to sweat at the brow in yearning for some relief. 

Smoke, mirrors, and a caffeinated and bespectacled mouse drops into your room. The mouse exclaims, "I have the aid you seek, you sweaty beast!"

Rarely will I offer kind words about STATA (or SAS) but there is one rather nifty functionality it offers that R, Python, and a few other open source tools still seem to lack: ability to run, organize, and report estimates from multiple regressions in a relatively high level yet flexible framework. By no means do I try to recreate STATA's framework but I do exert effort to lighten your burden in case you have similar needs.

In this notebook, I mostly present the use of the functions but if you are curious about the source code, it's available on [GitHub](https://github.com/kiwiPhrases/multiple-regressions)

# A non-confidential example

We'll use the 2015 American Housing Survey subset of Raleigh, NC observations ( sample was uploaded to [GitHub](https://raw.githubusercontent.com/kiwiPhrases/multiple-regressions/master/AHS_2015_National_Metropolitan_renters_raleigh.csv) ) Suppose we are curious whether housing units deemed severely inadequate by the US Housing Authority fetch for lower rent. I estimate two specifications:

- with structural characteristics

- with structural and occupant characteristics

Specifically, for structural variables I include STORIES, BEDROOMS, and GARAGE while for occupant characteristics I include lengthTenure, NUMADULTS, HHAGE, and HHSEX. The variable of interest is **severelyInaquate**.

Suppose you have already loaded the functions and modules [below](#the-code) then, we simply do the below


```python
# independent variables
structural = ['BEDROOMS','GARAGE','STORIES','severelyInadequate']
occupant = ['HHAGE','NUMADULTS','lengthTenure']

# make two specifications in Patsy format
xList = ['+'.join(occupant)+"+"+"+".join(structural),
         '+'.join(structural)]

# estimate the regressions and get output
runRegressions('RENT',xList,key_vars=['severelyInadequate'], data = df,
                            specification_names = ['demographics and structure','structure'])
```

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
      <th>R^2 adj</th>
      <th>cond. num</th>
      <th>nObs</th>
      <th>severelyInadequate</th>
    </tr>
    <tr>
      <th>specification</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>demographics and structure</th>
      <td>0.149</td>
      <td>365.514</td>
      <td>711</td>
      <td>-95.0526 (0.602)</td>
    </tr>
    <tr>
      <th>structure</th>
      <td>0.123</td>
      <td>27.6683</td>
      <td>711</td>
      <td>-86.4810 (0.656)</td>
    </tr>
  </tbody>
</table>
</div>



*Note* that in the above case, I specified a name for each model specification but I don't have to:


```python
# estimate the regressions and get output
runRegressions('RENT',xList,key_vars=['severelyInadequate'], data = df)

```

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
      <th>R^2 adj</th>
      <th>cond. num</th>
      <th>nObs</th>
      <th>severelyInadequate</th>
    </tr>
    <tr>
      <th>specification</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.149</td>
      <td>365.514</td>
      <td>711</td>
      <td>-95.0526 (0.602)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.123</td>
      <td>27.6683</td>
      <td>711</td>
      <td>-86.4810 (0.656)</td>
    </tr>
  </tbody>
</table>
</div>




```python
help(runRegressions)
```

    Help on function runRegressions in module __main__:
    
    runRegressions(y, xList, key_vars, data, specification_names=None)
        y - dependent variable (string)
        xList - list of explanatory variable strings (list of Patsy compatible formulas)
        key_vars - list of independent variables you wish to see results for
        df - a Pandas DataFrame that contains the data (list)
        specification_names - list of regression names (list of strings)
        
        Description: runRegressions estimates multiple regression specifications
        and returns coefficent estimate for key_vars, p-value, and other stats for each
        specification in a single table.
    
## Full Table Yikes


Here is what a full statsmodels regression summary table looks like. Imagine asking yourself or your readers to read through many of these - especially if you are only interested in a small subset of the variables reported below. (**scream face**)


<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>          <td>RENT</td>       <th>  R-squared:         </th> <td>   0.158</td> 
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.149</td> 
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   12.01</td> 
</tr>
<tr>
  <th>Date:</th>             <td>Wed, 10 Jul 2019</td> <th>  Prob (F-statistic):</th> <td>1.61e-14</td> 
</tr>
<tr>
  <th>Time:</th>                 <td>09:31:52</td>     <th>  Log-Likelihood:    </th> <td> -5442.8</td> 
</tr>
<tr>
  <th>No. Observations:</th>      <td>   711</td>      <th>  AIC:               </th> <td>1.090e+04</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   703</td>      <th>  BIC:               </th> <td>1.094e+04</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     7</td>      <th>                     </th>     <td> </td>    
</tr>
<tr>
  <th>Covariance Type:</th>         <td>HC0</td>       <th>                     </th>     <td> </td>    
</tr>
</table>
<table class="simpletable">
<tr>
           <td></td>             <th>coef</th>     <th>std err</th>      <th>z</th>      <th>P>|z|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>          <td>   -1.6921</td> <td>  129.803</td> <td>   -0.013</td> <td> 0.990</td> <td> -256.102</td> <td>  252.718</td>
</tr>
<tr>
  <th>GARAGE[T.Yes]</th>      <td>   94.5023</td> <td>   53.770</td> <td>    1.758</td> <td> 0.079</td> <td>  -10.884</td> <td>  199.889</td>
</tr>
<tr>
  <th>HHAGE</th>              <td>    4.7165</td> <td>    2.014</td> <td>    2.342</td> <td> 0.019</td> <td>    0.770</td> <td>    8.663</td>
</tr>
<tr>
  <th>NUMADULTS</th>          <td>   91.5411</td> <td>   31.927</td> <td>    2.867</td> <td> 0.004</td> <td>   28.964</td> <td>  154.118</td>
</tr>
<tr>
  <th>lengthTenure</th>       <td>  -13.3735</td> <td>    6.220</td> <td>   -2.150</td> <td> 0.032</td> <td>  -25.564</td> <td>   -1.183</td>
</tr>
<tr>
  <th>BEDROOMS</th>           <td>  134.4728</td> <td>   32.704</td> <td>    4.112</td> <td> 0.000</td> <td>   70.375</td> <td>  198.571</td>
</tr>
<tr>
  <th>STORIES</th>            <td>  139.3267</td> <td>   26.671</td> <td>    5.224</td> <td> 0.000</td> <td>   87.052</td> <td>  191.601</td>
</tr>
<tr>
  <th>severelyInadequate</th> <td>  -95.0526</td> <td>  182.032</td> <td>   -0.522</td> <td> 0.602</td> <td> -451.830</td> <td>  261.724</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>414.521</td> <th>  Durbin-Watson:     </th> <td>   1.930</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td>  <th>  Jarque-Bera (JB):  </th> <td>4054.431</td>
</tr>
<tr>
  <th>Skew:</th>          <td> 2.477</td>  <th>  Prob(JB):          </th> <td>    0.00</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td>13.598</td>  <th>  Cond. No.          </th> <td>    366.</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors are heteroscedasticity robust (HC0)    

# The Code

## Loading the data


```python
#  load sample data (2015 AHS subsample for Raleigh, NC renters) from github
data_url = 'https://raw.githubusercontent.com/kiwiPhrases/multiple-regressions/master/AHS_2015_National_Metropolitan_renters_raleigh.csv'
df = pd.read_csv(data_url)

# drop columns that are all null
allNulls = df.columns[df.isnull().all()]
df.drop(allNulls, axis=1, inplace=True)
```

## Functions


This isn't a package yet so we can't just import it like a regular module (yet)


```python
import pandas as pd
import statsmodels.api as sm
import statsmodels.formula.api as smf

def runRegression(y,x,data, cov_type='HC0'):
    print("Covariance type: %s" %cov_type )
    form = '{0} ~ {1}'.format(y,x)
    mod = smf.ols(formula=form, data=data)
    res = mod.fit(cov_type=cov_type)
    return(res)

def runQuantRegression(y,x,data,quant=.5):
    form = '{0} ~ {1}'.format(y,x)
    mod = smf.quantreg(formula=form, data=data)
    res = mod.fit(quant=.5)
    return(res)

def runRegressions(y, xList, key_vars, data, specification_names=None, cov_type='HC0'):
    """
    y - dependent variable (string)
    xList - list of explanatory variable strings (list of Patsy compatible formulas)
    key_vars - list of independent variables you wish to see results for
    df - a Pandas DataFrame that contains the data (list)
    specification_names - list of regression names (list of strings)
    
    Description: runRegressions estimates multiple regression specifications
    and returns coefficent and p-values estimates for key_vars and regressions
    stats for each specification in a single table. 
    """
    # if no custom names are given, use numbers
    if specification_names is None:
        specification_names = [str(i) for i in range(len(xList))]
    if len(specification_names) != len(xList):
        print('specification names not the same length as independent variables list')
        return(None)
        
    # run regressions    
    regDict = {}
    for x, name in zip(xList, specification_names):
        print("Estimating specification: %s" %name)
        regDict[name] = runRegression(y,x,data, cov_type='HC0')
    print("Estimation done")   
    
    # extract estimates:
    print("Extracting results")
    outDict = {}    
    for key in regDict.keys():
        # get regression statistics
        outDict[key] = {'nObs':regDict[key].nobs,'R^2 adj':regDict[key].rsquared_adj.round(3), 'cond. num':regDict[key].condition_number}
        
        # loop over variables of interest
        for var in key_vars:
            outDict[key][var] = "%.04f (%.03f)" %(regDict[key].params[var], regDict[key].pvalues[var])
        
    outdf =pd.DataFrame(outDict).transpose()
    outdf.index.name='specification'
    return(outdf)    
```
