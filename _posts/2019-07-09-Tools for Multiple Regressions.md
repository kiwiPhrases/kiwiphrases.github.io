---
layout: post
title:  "Tools for Estimating and Reporting Multiple Specifications"
date:   2019-07-09 5:55:00 -0700
categories: code
---

# Introduction

You just estimated a regression, got results, and now either your model or your colleague suggests you change or elaborate on the specification. Repeat that a few times and reporting results quickly becomes a headache. It simply is not practical or palatable to report every single full regression table and you begin to sweat at the brow in yearning for some relief. Smoke, mirrors, and a caffeinated and bespectacled mouse drop into the room. The mouse exclaims, "I have the aid you seek, you sweaty human!"

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

    Estimating specification: demographics and structure
    Covariance type: HC0
    Estimating specification: structure
    Covariance type: HC0
    Estimation done
    Extracting results
    

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
