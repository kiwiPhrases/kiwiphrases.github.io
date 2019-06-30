---
layout: post
title:  "Fuzzy Matching in Pandas (Python)"
date:   2019-06-29 5:55:00 -0700
categories: research
---

# Introduction

But aren't there solutions for this already? Indeed, for strings, check out this great stack [question](https://stackoverflow.com/questions/13636848/is-it-possible-to-do-fuzzy-match-merge-with-python-pandas) and even Pandas now has a method [`pd.merge_asof`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.merge_asof.html) though good luck on making the latter work. 

Here, I provide a quick alternative to the above solutions as derived from my stack [question](https://stackoverflow.com/questions/56638423/find-absolute-minimum-between-two-numpy-arrays-but-keep-the-sign). This solution is good for a few reasons: 

1. Works fast on relatively large data without memory overruns
2. You can track unmatched and their closest matched elements
3. Works in NumPy so you don't need to load anything new

## Gerald's Example

Consider two Pandas DataFrames `A` and `B` where our primary target is `A` while `B` has some important info we need to add to `A`. 

Suppose Gerald is a teddy bear that likes green horses and has stables at particular zips (stored in `A`). Gerald wants to find green horses that are at (or near) his stables from a listing of horses `B`. 


```python
import pandas as pd
import numpy as np

A = pd.DataFrame({'gerald':list('teddy'), 'zips':[900081234,900081235,900081238,900105886,900107732]})
B = pd.DataFrame({'horses':list("greenhorse"),'zips':[900081234,900155588,900081244,900081238,900105885,900107734,900155566,500155583,200224422,900081236]})
```

So what happens when we try to merge the DataFrames to help Gerald find green horses?

<img style="float: left;" src="/gerald.jpg" alt="gerald" width="200">

```python
C = A.merge(B, on='zips', how='left')
print("Number of unmatched zips: %d" %C.horses.isnull().sum())
```

    Number of unmatched zips: 3
    




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
      <th>gerald</th>
      <th>zips</th>
      <th>horses</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>t</td>
      <td>900081234</td>
      <td>g</td>
    </tr>
    <tr>
      <th>1</th>
      <td>e</td>
      <td>900081235</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>d</td>
      <td>900081238</td>
      <td>e</td>
    </tr>
    <tr>
      <th>3</th>
      <td>d</td>
      <td>900105886</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>y</td>
      <td>900107732</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>


## Can we improve? 
Using the ordinary, exact match merge, Gerald finds only two green horses. Can we improve?
**Depends**. I am usually very wary of fuzzy matching but if you know your data and it makes sense then let's proceed. 

In our case, 9 digit zips close to each other geographically also tend to be close to each numerically. For example, zips *900081234* and *900081235* may represent two different faces of the same building so even though *900081235* was **not** matched, it makes sense to have it match to the 900081324 stable

## Road Map

The plan to match zips that are close to the matched ones is as follows:

1. find the *matched* zip that is closest to the **unmatched** zip
2. if *matched* zip is close enough, modify unmatched zip to matched one
3. use map of unmatched to matched zips to merge to perform a nearest zip merger


```python
############################## Step 1 ################################
def getNearestDiff(left_array, right_array):
    """
    Code and algorythm credit goes to piRSquared from Stackoverflow.
    Modified for use here by me
    """
    right_sorted  = np.sort(right_array) # sort right array
    a  = right_sorted.searchsorted(left_array) #find closest index(sort of)
    right      = np.minimum(a, right_sorted.shape[0] - 1)
    left       = np.maximum(a - 1, 0)

    # find differences from both right and left sides
    right_diff = left_array - right_sorted[right]
    left_diff  = left_array - right_sorted[left ]

    #depending on which is smaller in absolute value, returns smallest diff
    #with correct sign
    return(np.where(np.abs(right_diff) <= left_diff, right_diff, left_diff))

############################## Step 2 ################################
def makeMap(left_array, diffs, threshold=1):
    mIdx = np.abs(diffs)<=threshold
    return(pd.DataFrame({'unmatched':left_array[mIdx],'nearest_matched':left_array[mIdx] - diffs[mIdx]}))

############################## Step 3 ################################
def modifyLeftDf(tieIn, B, merge_col):
    # merge
    modB = B.merge(tieIn.rename(columns={'nearest_matched':merge_col}),on=merge_col,how='right')
    modB = pd.concat([B,modB],sort=False).reset_index(drop=True)
    
    ## transfer values
    modB.unmatched.fillna(modB.zips,inplace=True)
    modB.rename(columns = {merge_col:'nearest_'+merge_col,'unmatched':merge_col},inplace=True)
    return(modB)

############################ All steps ################################
def merge_nearest(A, B, merge_col, mismatch_col, threshold=1):
    print("Performing initial merge")
    C = A.merge(B, on=merge_col, how='left')
    left_array = C.loc[C[mismatch_col].isnull(), merge_col]
    
    print("\tNumber of unmatched elements: %d" %left_array.shape[0])
    diffs = getNearestDiff(left_array,B[merge_col])
    tieIn = makeMap(left_array,diffs,threshold=threshold)
    
    print("Making modified right DataFrame")
    modB = modifyLeftDf(tieIn, B, merge_col)
    
    print("Merge with nearest matches (threshold: %d)" %threshold)
    C = A.merge(modB, on=merge_col, how='left')
    wNearest = C.loc[C[mismatch_col].isnull(), merge_col].shape[0]
    print("\tNumber of unmatched elements with nearest matches: %d" %wNearest)
    return(C)
```

## Implementation


```python
merge_nearest(A,B,'zips','horses',threshold=1)
```

    Performing initial merge
    	Number of unmatched elements: 3
    Making modified right DataFrame
    Merge with nearest matches (threshold: 1)
    	Number of unmatched elements with nearest matches: 1
    




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
      <th>gerald</th>
      <th>zips</th>
      <th>horses</th>
      <th>nearest_zips</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>t</td>
      <td>900081234</td>
      <td>g</td>
      <td>900081234.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>e</td>
      <td>900081235</td>
      <td>e</td>
      <td>900081236.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>d</td>
      <td>900081238</td>
      <td>e</td>
      <td>900081238.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>d</td>
      <td>900105886</td>
      <td>n</td>
      <td>900105885.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>y</td>
      <td>900107732</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>

We have given Gerald two green horses that he otherwise would not have found: Gerald is ecstatic! (release balloons)



# Discussion

I am sure I am performing unnecessary steps and this could be optimized in a number of ways but I have tested this on fairly large arrays and it works pretty quickly. The main throttles are the merges. For example, I don't have to remerge two entire dataframes, I could remerge only the unmatched subsets. 

# The details

### 1. Find nearest diff
```python
diffs = getNearestDiff(C.loc[C.horses.isnull(),'zips'], B.zips)
print("Found smallest differences:",diffs)
```

    Found smallest differences: [-1  1 -2]
    

### 2. Modify unmatched zips


```python
d_thresh = 1

tieIn = pd.DataFrame({'unmatched':C.loc[C.horses.isnull(),'zips'],'nearest_matched':C.loc[C.horses.isnull(),'zips'] - diffs})
tieIn.loc[np.abs(diffs)>d_thresh,'nearest_matched'] = np.nan
print("Map unmatched values to nearest matched value:")
tieIn
```

    Map unmatched values to nearest matched value:
    




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
      <th>unmatched</th>
      <th>nearest_matched</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>900081235</td>
      <td>900081236.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>900105886</td>
      <td>900105885.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>900107732</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
## bring over the data from B onto tieIn using the nearest matched
## and append these values to the original B. Do some convenient renaming
modB = B.merge(tieIn.rename(columns={'nearest_matched':'zips'}),on='zips',how='right')
modB = pd.concat([B,modB],sort=False).reset_index(drop=True)
modB.unmatched.fillna(modB.zips,inplace=True)
modB.rename(columns = {'zips':'nearest_zip','unmatched':'zips'},inplace=True)
modB
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
      <th>horses</th>
      <th>nearest_zip</th>
      <th>zips</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>g</td>
      <td>900081234.0</td>
      <td>900081234.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>r</td>
      <td>900155588.0</td>
      <td>900155588.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>e</td>
      <td>900081244.0</td>
      <td>900081244.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>e</td>
      <td>900081238.0</td>
      <td>900081238.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>n</td>
      <td>900105885.0</td>
      <td>900105885.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>h</td>
      <td>900107734.0</td>
      <td>900107734.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>o</td>
      <td>900155566.0</td>
      <td>900155566.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>r</td>
      <td>500155583.0</td>
      <td>500155583.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>s</td>
      <td>200224422.0</td>
      <td>200224422.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>e</td>
      <td>900081236.0</td>
      <td>900081236.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>n</td>
      <td>900105885.0</td>
      <td>900105886.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>e</td>
      <td>900081236.0</td>
      <td>900081235.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>900107732.0</td>
    </tr>
  </tbody>
</table>
</div>



## 3. Re-merge with nearest matches

Now that modified `B` contains the nearest matches too, we can re-merge. As can be seen below, we do a lot better by matching 4 out of 5 zips instead of 2 out of 5


```python
C = A.merge(modB, on='zips', how='left')
print("Number of unmatched zips: %d" %C.horses.isnull().sum())
C
```

    Number of unmatched zips: 1
    




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
      <th>gerald</th>
      <th>zips</th>
      <th>horses</th>
      <th>nearest_zip</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>t</td>
      <td>900081234</td>
      <td>g</td>
      <td>900081234.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>e</td>
      <td>900081235</td>
      <td>e</td>
      <td>900081236.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>d</td>
      <td>900081238</td>
      <td>e</td>
      <td>900081238.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>d</td>
      <td>900105886</td>
      <td>n</td>
      <td>900105885.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>y</td>
      <td>900107732</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>
