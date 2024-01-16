---
layout: post
title:  "Sunsetting the USC Cycling App"
date:   2022-09-01 5:55:00 -0700
categories: research
---

# Saying goodbye to one's first app

During the onset of the Pandemic lockdowns, I simultaenously took on the USC Cycling team president position and found myself with added locked-in time. What does anyone do in these types of situations? Obviously, build an app. Fuelled by my frustration with the team's manual, Excel-based, inventory management, I quickly found myself going from a simple Python-SQL interface to full on Django-based app. The result was the [SC Cycling Merch](https://github.com/kiwiPhrases/sccycling-merch) app with a cloud-based database sitting on AWS and the app itself hosted on the incredibly easy-to-use (and, at the time, free) Heroku platform. After maintaining and building this app for over 2 years, it was with a sad heart that I shut it down in autumn of 2022 once Heroku decided to discontinue its free hosting services. 


# The app

The app had a simple goal of enabling the team's leadership to easily manage inventory in a systematic, semi-automated way, and to improve the team's gear marketing through an online store. As primarily a Python developer, choosing Django to write an app was essentially a no brainer. I had no prior experience in developing web-based apps but the ample tutorials enabled me to quickly transform a simple blog-post app into a full-featured online store. Really, the hardest parts were the front-end development since that required HTML and CSS knowledge, along with a dash of aesthetic tastes, that I hadn't quite developed at the time. Perhaps on a side note, I further had to perk up a creative side by organizing photoshoots to complete photo collections of our cycling gear. 

For posterity and reminiscence, here are some pics of the website. 

## the front page
<p align="center"><img src="/frontpage.png" alt="frontpage" align="center" width="600"/></p>

## the store
<p align="center"><img src="/thestore.png" alt="store" align="center" width="600"/></p>

## the product
<p align="center"><img src="/theproduct.png" alt="productpage" align="center" width="600"/></p>

## the cart
<p align="center"><img src="/thecart.png" alt="cartpage" align="center" width="600"/></p>



# Why take down a functioning product?

Ultimately, despite being a functional inventory management system, I never quite added a payment feature to the app. Consequently, when orders were submitted, two e-mails went out, and the leadership would then follow up with the buyer to collect payment information via phone or email. The feature was primarily omitted because it would have required a potentially costly payment-processing layer or a subscription to a services such as Square. However, Square has its own online store-building tools so if the team is paying Square anyway, why not use them as a store host, especially now that Heroku was charing to host, too. This is the cost and benefit analysis that ultimately led to the site's shutdown.


# App building take-aways

Perhaps the key takeaway is that I really enjoyed building the app. It tapped into my desire to create things that make our lives easier and, hopefully, better. I also love learning new skills and boy did form processing befuddle me at the time. The remaining bits below are a slew of thoughts. 

## Django and my choice of tools

Would I use Django again? Absolutely. Before and since building the cycling app, I've used a number of different app-constructing methods ranging from the quick and easy R-Shiny to Dash, Flask, Streamlit, Esri (not by choice), effin Excel (not by choice), Tableau (not by choice), and Power BI (you guessed it, also not by choice). The range of tasks in their use varied greatly but with the exception of Flask, none have quite the depth to build multi-page interactive apps. Most are only great for quick dashboards and visualizations with various degrees of interaction. Flask almost parallels Django's abilities yet Django's native database interface and master-level db-management along with straight-forward support for user management makes it my go-to for app development. Django also seems to be slightly less tedious to deploy to Flask. 

## Things I should have done differently

There were a few things in the sc-cycling's app development that, in retrospect, I sort of cringe at. Perhaps the biggest element is the semi-exposed db URI. Why semi-exposed? Heroku allowed github-based deployment so I could simply push updates straight from git to Heroku. This was rather convenient, especially since Heroku's projects were private so db credentials were not blatantly exposed. However, I clearly should have made use of environmental variables to begin with. This was a newbie's mistake. 

The other sources of shame mostly entail front-end development which is something I still struggle with. The sc-cycling app utilized a hodgepodge of html-styling and CSS without particular regard to consistency or readability. At the time, my approach to front-end consisted of googling bits of code that do what I needed and then copy/modifying them to suit my needs. Now adays, the use of CSS and bootstraps is a lot more obvious path to site styling. I, of course, won't quite mention that my site looked a tad dated in the aesthetic department. 

# Conclusion

Honestly, if you made it this far in the post, I applaud you for determination. This post in particular veered more into the personal diary territory than anything prior. Yet, perhaps that's appropriate since this really is a eulogy for my first public big app. If there's any takeaway then it's this: go develop, go create. 








