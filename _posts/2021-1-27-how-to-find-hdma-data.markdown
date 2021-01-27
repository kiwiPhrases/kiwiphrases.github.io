---
layout: post
title:  "How to find FDIC Deposit (SDI) Data?"
date:   2020-10-21 5:55:00 -0700
categories: research
---

# When Google can't help you
I hope to save you many hours of senseless toil with this brief post on where
to download FDIC's bank [Deposit Market Share data](https://www7.fdic.gov/sod/sodMarketBank.asp?barItem=2). These public data show the total dollar amount deposited at each bank within U.S. metropolitan areas.

# Things that work
If you want to download a zipped version of CSV data (I am not aware of any other options), then you have to use a modified link below. The exact link below will download 2017 data but if you want 2015 or 2018 or any other available year then simply change the 2017 in the link below to the desired year.

<p align="center">**http://www5.fdic.gov/sod/ShowFileWithStats1.asp?strFileName=all_2017.zip**</p>

As a side note, if the link doesn't seem to work, make sure you set the browser to allow downloads from this domain.

# Where does that link come from?

The US government's data catalog website has the 2017 file readily available [here](https://catalog.data.gov/dataset/fdic-summary-of-deposits-sod-download-file/resource/9e1c84e1-5d68-4271-8193-3b40b4c57d00). They post the direct download link there so a bit of experimentation let me realize that changing the year in the link allows one to download other years. In this manner, I downloaded years 2008, 2014-2018.

# Things that don't (quite) work
I am mentioning the resources below because they are some of the tops ones to pop up if you search for FDIC Deposit data or something similar.

You can try using FDIC's [datatools](https://www.fdic.gov/open-government/datatools.html) to get Summary of Deposits or Deposit Market Share data but acquiring data for the entire US in this manner is prone to both error and tremendous hassle.

You can download 1987-1993 Deposit Data [here](https://www.fdic.gov/foia/sod/) but if you won't be able to get anything from this century.

# Why did I need these data?
I was trying to recreate Michael Reher's and Pedro Gete's [Financial Studies paper](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2756056) on *Mortgage Supply and Housing Rents* so I can use their instrument in my work. They use the Big 4 banks' share of deposits within a metropolitan area to instrument mortgage denial rates.
