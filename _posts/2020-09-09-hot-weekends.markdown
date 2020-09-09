---
layout: post
title:  "Are weekends hotter than weekdays in LA?"
date:   2020-09-09 5:55:00 -0700
categories: research
---

<p><img style="float: left;" src="/images/drinking.png" alt="drinkingcylists" width="300"></p>

![drinkingcyclists](/images/drinking.png)


From BLM to the elections, this 2020 summer has been notable for a myriad of reasons but let me discuss one that most of you were probably the least worried about: weekend temps. 


Throughout the entire summer, the time-constrained weekdays seemed comfortably heated for outdoor bike adventures but almost every weekend, the heat rose to unbearable levels. You may think that this thought pattern perfectly aligns with confirmation bias and I would agree with you. After all, our planet is ambivalent to our arbitrary concept of weekdays so why would the weekends be any hotter than weekdays? Yet, here I am downloading weather data to check if weekends were indeed warmer than weekdays. 

# Analysis

By most measures, this summer's climate was indeed indifferent between weekdays and weekends. Using data on temperature statistics from the [National Weather Forecast Service](https://w2.weather.gov/climate/xmacis.php?wfo=lox) for the months of May to Sept 9th, 2020, there is essentially no difference between weekday and weekend temps. In the bar chart below, I plot the average daily minimum, maximum, and average temperatures for weekdays and weekends separately. Looking at the chart, you would be forgiven for not seeing a difference. Similarly, the share of weekends with average temperatures higher than the weekdays was 53\% or nigh coin-toss worthy. By these measures, my perception of hotter weekends is a product of my confirmation bias. 

![weatherbarplot](/images/weatherbarplot.png)

The above doens't seem to be time indifferent though. In the below plot, I visualize the average maximum temperature for each summer week with means computed separately for weekdays and weekends. On weeks 18 through 26, we see only one weekend on which the weekend average was higher than the weekday mean. On week 27, the pattern flips. **80%** of weekends in week 27-36 had weekend temperatures higher than the weekday average. 

![weeklytempmeans](/images/weeklytempaverages.png)

An alternative view can be seen in the plot below. When we plot the entire summer then the average day-of-week temperatures are similar between weekdays and weekends (Left plot) but when we plot only week 27 on (Right plot), the difference between weekday and weekend averages becomes larger. This is suggestive that in the 2nd part of the summer, weekends were, on average, considerably (3-10 degrees) warmer than weekdays. 

![weekdaytempmeans](/images/weekdaytempmeans.png)

# Conclusion

How sensitive is the result to the choice of summer midpoint and choice of mean versus median? As can be seen in the 1st column of the table below, mean max temperature results are not particularly sensitive to the choice of summer mid-point. Whether I shift the midpoint by +/-2 weeks still suggests that weekends tended to be hotter than weekdays though week 27 does seem to be a convenient maximizer. Interestingly, when I use the median maximum temp instead of the mean then the weekday averagas get higher and so it no longer seems that so many weekends were warmer than weekends. *Note*: in case of weekends, medians and means are the same. 

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Max temp stat type</th>
      <th>mean</th>
      <th>median</th>
    </tr>
    <tr>
      <th>weeks from median week</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>-2</th>
      <td>0.67</td>
      <td>0.50</td>
    </tr>
    <tr>
      <th>-1</th>
      <td>0.73</td>
      <td>0.55</td>
    </tr>
    <tr>
      <th>0</th>
      <td>0.80</td>
      <td>0.60</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.78</td>
      <td>0.56</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.75</td>
      <td>0.50</td>
    </tr>
  </tbody>
</table>

But perhaps the difference that the means and medians make also points to why I may have thought that weekends are hotter than weekdays. When latter-summer weekends were warmer than weekdays then the weekends were *much* hotter than the weekday means which may have made the difference particularly salient. Alternatively, in the latter part of the summer, the coolest part of the week also coincided with the time we are making our cycling plans (monday-tuesday) so the difference between the weekend and early week temps was particularly large. Were we making our plans on Thursday or Friday, we may not have perceived weekends to be that much hotter than weekdays. 


I suppose on a modeling note, daily temps are often modeled as some some cos+sin function because weather exudes cyclicality. It's possible that in the latter part of the summer, these cycles simply synced up with the weeks, thus resulting in these strange patterns. 
