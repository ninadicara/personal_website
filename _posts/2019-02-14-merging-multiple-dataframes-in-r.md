---
layout: post
title: Merging Multiple Dataframes in R
date: 2019-02-14 15:34:30
description: A quick explainer with code
---

I’m publishing this on the blog in case anyone else has been facing the same problem as me and is unsure how to get around it!

I’ve recently started a project working with around 15 large data sets of survey results, each has approximately 15,000 observations/rows with between 150-600 variables/columns depending on the data set.

Each survey was sent out to all the same people, but a different subset replied each time, so each dataset has a different subset of rows. Thankfully the same unique identifier is available in every dataset, formatted the same way.

**Mission:** To join all 15+ dataframes in the form of a ‘full-join’ (in SQL speak).

<img src="/images/merge-dataframes.jpg">

I knew there must be a simple way to do this, but unfortunately all the examples online are for two datasets. With more than three dataframes to merge those options just get messy and impractical.

Plus surely the best bit of programming is about finding a neat and tidy solution to a messy problem, especially recursive ones.

Some had attempted to join multiple but this required a function they had written being copied into the code, customised and then implemented. This was neat, but it seemed so simple that I refused to believe there wasn’t an existing package which could answer the issue.

In the end it was DataCamp that came though for me, with (as promised) a neat and tidy solution that didn’t involve creating 15 temporary files or writing your own function.

**Solution:**

First, create a list of the files you are going to merge.

Then, create a new dataframe joining them all together using reduce() from purr.

```r
install.packages(c("purrr", "dplyr"))
library(purrr)
library(dplyr)

#Import and label your files
A <- #import A, B, C and D as dataframes
B <- 
C <-
D <- 

files <- list(A, B, C, D)
new_df <- reduce (files, full_join, by = "ID")
```

This has already saved me a lot of time as I’ve changed the file list a couple times and not needed to change anything else but the list. Neat and tidy programming, check!

Any other solutions to this problem welcomed!
