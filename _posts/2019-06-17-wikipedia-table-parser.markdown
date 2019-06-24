---
layout: post
title:  "Robust Wikipedia Table Parser"
date:   2019-06-17 5:55:00 -0700
categories: coding example
---
# Robust HTML table parser code for tables with rowspan and/or colspans
In scouring the web, it was to the embarrasment of humanity that I could not find a parser that could accurately parse the tables found on Wikipedia such as [Wisconsin_political_power](https://en.wikipedia.org/wiki/Political_party_strength_in_Wisconsin); I thus wrote my own.

The issue is that parsers for Wikipedia tables could not handle rowspan and colspan attributes in tables so when these parsers faced such obstacles they either errered out or returned jarmbled tables and entries. Because I needed data on a Wikipedia article in which the data tables had these attributes, I had to somehow figure out how to parse such tables. If you encounter a similar issue, I hope this saves you time. 


Below, I include both the functions and a few examples. If you wish to just see the examples, you can scroll past the Parser Functions

## Parser functions
The functions below parse HTML tables (BeautifulSoup objects). I also include a convenience function for fetching the HTML of the website though for details, I suggest looking through the examples. 

{% highlight python %}
##Libraries used are:
import pandas as pd
import numpy as np
from bs4 import BeautifulSoup as bs
import requests

def pre_process_table(table):
    """
    INPUT:
        1. table - a bs4 element that contains the desired table: ie <table> ... </table>
    OUTPUT:
        a tuple of: 
            1. rows - a list of table rows ie: list of <tr>...</tr> elements
            2. num_rows - number of rows in the table
            3. num_cols - number of columns in the table
    """
    rows = [x for x in table.find_all('tr')]

    num_rows = len(rows)
    
    ## get table's column count. Most of often, this will be accurate
    num_cols = max([len(x.find_all(['th','td'])) for x in rows])

    ## ...on occasion, the above fails because some columns have colspan attributes too. The below accounts for that:
    header_rows_set = [x.find_all(['th', 'td']) for x in rows if len(x.find_all(['th', 'td']))>num_cols/2]
    
    num_cols_set = []

    for header_rows in header_rows_set:
        num_cols = 0
        for cell in header_rows:
            row_span, col_span = get_spans(cell)
            num_cols+=len([cell.getText()]*col_span)
            
        num_cols_set.append(num_cols)
    
    num_cols = max(num_cols_set)
    #print(num_cols)
    
    return (rows, num_rows, num_cols)


def get_spans(cell):
        """
        INPUT:
            1. cell - a <td>...</td> or <th>...</th> element that contains a table cell entry
        OUTPUT:
            1. a tuple with the cell's row and col spans
        """
        if cell.has_attr('rowspan'):
            rep_row = int(cell.attrs['rowspan'])
        else: # ~cell.has_attr('rowspan'):
            rep_row = 1
        if cell.has_attr('colspan'):
            rep_col = int(cell.attrs['colspan'])
        else: # ~cell.has_attr('colspan'):
            rep_col = 1 
        
        return (rep_row, rep_col)
 
def process_rows(rows, num_rows, num_cols):
    """
    INPUT:
        1. rows - a list of table rows ie <tr>...</tr> elements
    OUTPUT:
        1. data - a Pandas dataframe with the html data in it
    """
    data = pd.DataFrame(np.ones((num_rows, num_cols))*np.nan)
    for i, row in enumerate(rows):
        col_stat = data.iloc[i,:][data.iloc[i,:].isnull()].index[0]

        for j, cell in enumerate(row.find_all(['td', 'th'])):
            rep_row, rep_col = get_spans(cell)

            #print("cols {0} to {1} with rep_col={2}".format(col_stat, col_stat+rep_col, rep_col))
            #print("\trows {0} to {1} with rep_row={2}".format(i, i+rep_row, rep_row))

            #find first non-na col and fill that one
            while any(data.iloc[i,col_stat:col_stat+rep_col].notnull()):
                col_stat+=1

            data.iloc[i:i+rep_row,col_stat:col_stat+rep_col] = cell.getText()
            if col_stat<data.shape[1]-1:
                col_stat+=rep_col

    return data

def fetch_website(url):
    ## apply a more friendly user agent
    user_agent={'User-agent':'Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/44.0.2403.18 Safari/537.36'}
    r=requests.get(url, headers=user_agent)
    try:
        #print("Accessed and downloaded URL data")
        return(r.content)
    except ConnectionError:
        print("Skipping this url")
        return(None)    
    
def main(table):
    """
    DESCRIPTION:
        Parse one table and return a Pandas DataFrame
    """
    import pandas as pd
    import numpy as np
    from bs4 import BeautifulSoup as bs
    
    rows, num_rows, num_cols = pre_process_table(table)
    df = process_rows(rows, num_rows, num_cols)
    return(df)
{% endhighlight %}

### Example - the essence of the game


```python
s = """
<table>
    <tr>
        <th rowspan="2">year</th>
        <th>col1</th>
        <th colspan="2">col2</th>
    </tr>
    <tr>
        <td rowspan="2">aaa</td>
        <td rowspan="2">bbb</td>
        <td rowspan="1">ccc</td>
    </tr>
    <tr>
        <td>2017</td>
        <td rowspan="2">ddd</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>col1</td>
        <td colspan="1">col2</td>
    </tr>
</table>
"""
table = bs(s, 'lxml').find('table')
rows, num_rows, num_cols = pre_process_table(table)
df = process_rows(rows, num_rows, num_cols)
df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>year</td>
      <td>col1</td>
      <td>col2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>year</td>
      <td>aaa</td>
      <td>bbb</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2017</td>
      <td>aaa</td>
      <td>bbb</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2018</td>
      <td>col1</td>
      <td>col2</td>
    </tr>
  </tbody>
</table>
</div>


### Example 2 - Political Parties in Wisconsin:
Here, I will parse the HTML table linked in the description. 


```python
## Go to the link and download the page's html code:
url = "https://en.wikipedia.org/wiki/Political_party_strength_in_Wisconsin"
site = fetch_website(url)
soup = bs(site, 'lxml')
```


```python
## Find tables on the page and locate the desired one:
## Caveat: note that I explicitly search for a wikitable!  
tables = soup.findAll("table", class_='wikitable')

## I want table 3 or the one that contains years 2000-2018
table = tables[3]
```


```python
## run the above functions to extract the data
rows, num_rows, num_cols = pre_process_table(table)
df = process_rows(rows, num_rows, num_cols)

## print the result
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>10</th>
      <th>11</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Year</td>
      <td>Executive offices</td>
      <td>Executive offices</td>
      <td>Executive offices</td>
      <td>Executive offices</td>
      <td>Executive offices</td>
      <td>State Legislature</td>
      <td>State Legislature</td>
      <td>United States Congress</td>
      <td>United States Congress</td>
      <td>United States Congress</td>
      <td>Electoral College votes</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Year</td>
      <td>Governor</td>
      <td>Lieutenant Governor</td>
      <td>Secretary of State</td>
      <td>Attorney General</td>
      <td>Treasurer</td>
      <td>State Senate</td>
      <td>State Assembly</td>
      <td>U.S. Senator (Class I)</td>
      <td>U.S. Senator (Class III)</td>
      <td>U.S. House</td>
      <td>Electoral College votes</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2000</td>
      <td>Tommy Thompson (R)</td>
      <td>Scott McCallum (R)</td>
      <td>Doug La Follette (D)</td>
      <td>Jim Doyle (D)</td>
      <td>Jack Voight (R)</td>
      <td>17D, 16R</td>
      <td>55R, 44D</td>
      <td>Herb Kohl (D)</td>
      <td>Russ Feingold (D)</td>
      <td>5D, 4R</td>
      <td>Gore/Lieberman (D) N</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2001</td>
      <td>Scott McCallum (R)</td>
      <td>Margaret Farrow (R)</td>
      <td>Doug La Follette (D)</td>
      <td>Jim Doyle (D)</td>
      <td>Jack Voight (R)</td>
      <td>18D, 15R</td>
      <td>56R, 43D</td>
      <td>Herb Kohl (D)</td>
      <td>Russ Feingold (D)</td>
      <td>5D, 4R</td>
      <td>Gore/Lieberman (D) N</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2002</td>
      <td>Scott McCallum (R)</td>
      <td>Margaret Farrow (R)</td>
      <td>Doug La Follette (D)</td>
      <td>Jim Doyle (D)</td>
      <td>Jack Voight (R)</td>
      <td>18D, 15R</td>
      <td>56R, 43D</td>
      <td>Herb Kohl (D)</td>
      <td>Russ Feingold (D)</td>
      <td>5D, 4R</td>
      <td>Gore/Lieberman (D) N</td>
    </tr>
  </tbody>
</table>
</div>



### Example 3 - Cricket

Stackoverflow had a Wikipedia parsing [question](http://stackoverflow.com/questions/28763891/what-should-i-do-when-tr-has-rowspan), thought I would use the page of interest.

```python
url = "http://en.wikipedia.org/wiki/List_of_England_Test_cricket_records"
site = fetch_website(url)
soup = bs(site, 'lxml')
tables = soup.findAll("table", class_='wikitable')

## I want table 3 or the one that contains years 2000-2018
table = tables[3]

rows, num_rows, num_cols = pre_process_table(table)
df = process_rows(rows, num_rows, num_cols)

## print the result
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Rank</td>
      <td>Score</td>
      <td>Opposition</td>
      <td>Venue</td>
      <td>Place</td>
      <td>Season</td>
      <td>Result</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>45</td>
      <td>v  Australia</td>
      <td>Sydney Cricket Ground</td>
      <td>Sydney, Australia</td>
      <td>1886/87</td>
      <td>Won</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>46</td>
      <td>v  West Indies</td>
      <td>Queen's Park Oval</td>
      <td>Port of Spain, Trinidad and Tobago</td>
      <td>1994</td>
      <td>Lost</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>51</td>
      <td>v  West Indies</td>
      <td>Sabina Park</td>
      <td>Kingston, Jamaica</td>
      <td>2009</td>
      <td>Lost</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>52</td>
      <td>v  Australia</td>
      <td>The Oval</td>
      <td>London, England</td>
      <td>1948</td>
      <td>Lost</td>
    </tr>
  </tbody>
</table>
</div>



### Example 4 - Political Parties in DC


```python
url = "https://en.wikipedia.org/wiki/Political_party_strength_in_Washington,_D.C."
site = fetch_website(url)
soup = bs(site, 'lxml')
tables = soup.findAll("table", class_='wikitable')

## I want table 3 or the one that contains years 2000-2018
table = tables[0]

rows, num_rows, num_cols = pre_process_table(table)
df = process_rows(rows, num_rows, num_cols)

## print the result
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Year</td>
      <td>Executive office</td>
      <td>Executive office</td>
      <td>District Council</td>
      <td>District Council</td>
      <td>U.S. Congress</td>
      <td>U.S. Congress</td>
      <td>U.S. Congress</td>
      <td>U.S. Congress</td>
      <td>Presidential electoral college votes</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Year</td>
      <td>Mayor</td>
      <td>Attorney General</td>
      <td>Chair</td>
      <td>Composition</td>
      <td>U.S. House Delegate</td>
      <td>Shadow U.S. Senator (Seat 1)</td>
      <td>Shadow U.S. Senator (Seat 2)</td>
      <td>Shadow Representative</td>
      <td>Presidential electoral college votes</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1801\n...\n1870</td>
      <td>[1]</td>
      <td>[1]</td>
      <td>[1]</td>
      <td>[1]</td>
      <td>[1]</td>
      <td>[1]</td>
      <td>[1]</td>
      <td>[1]</td>
      <td>[2]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1871</td>
      <td>[3]</td>
      <td>[3]</td>
      <td>[3]</td>
      <td>[3]</td>
      <td>Norton P. Chipman (R)</td>
      <td>[4]</td>
      <td>[4]</td>
      <td>[4]</td>
      <td>[2]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1872</td>
      <td>[3]</td>
      <td>[3]</td>
      <td>[3]</td>
      <td>[3]</td>
      <td>Norton P. Chipman (R)</td>
      <td>[4]</td>
      <td>[4]</td>
      <td>[4]</td>
      <td>[2]</td>
    </tr>
  </tbody>
</table>
</div>


