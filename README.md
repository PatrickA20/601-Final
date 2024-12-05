---
title: "DACSS 601 Final Project"
author: "Patrick Anderson"
description: "Final Project"
date: 8/16/24
format: 
  html: 
    embed-resources: true
    self-contained-math: true
---

# Final Project: Cost of Living Index by Country

## Introducing the Dataset

This dataset represents the Cost of Living Index by Country per the 2024 mid year data imported from Kaggle and Numbeo. The whole dataset is compared to New York City, which has an index of 100 throughout every column. For example, if a country's index is 80 that means the average price of groceries, rent, cost of living, etc is 20% lower than it would be in NYC. The goal of this dataset is to see the comparisons from everywhere across the world to the most well known city, NYC. Throughout this, I will look further into the trends and patterns among the different variables in comparison with NYC, as I look closer into more detailed questions I will continue to compare those values to NYC to truly understand how expensive NYC is like everyone says.

There are 121 different countries and their rank is based on their Cost of Living (Cost.of.Living.Index) and here are what the rest of the columns mean.

-   Cost.of.Living.Index: "This index indicates the relative prices of consumer goods like groceries, restaurants, transportation, and utilities. It excludes accommodation expenses such as rent or mortgage."

-   Rent.Index: "This index estimates the prices of renting apartments in a city compared to New York City."

-   COL.Plus.Rent: "This index estimates consumer goods prices, including rent, in comparison to New York City."

-   Groceries.Index: "This index provides an estimation of grocery prices in a city relative to New York City. Numbeo uses item weights from the "Markets" section to calculate this index for each city."

-   Restaurant.Price.Index: "This index compares the prices of meals and drinks in restaurants and bars to those in NYC."

-   Local.Purchasing.Power.Index: "This index indicates the relative purchasing power in a given city based on the average net salary. A domestic purchasing power of 40 means that residents with an average salary can afford, on average, 60% less goods and services compared to residents of New York City with an average salary."

## Data Transformation

```{r}
library(tidyverse)
library(readr)
library(readxl)
library(haven)
library(stringr)
library(ggplot2)
library(stringr)
library(lubridate)
library(plotly)
```

In the code chuck above, I've downloaded every library that I might need within R so that all of the chunks will run fluidly.

```{r}
COLIndex<-read_csv("~/Desktop/DACSS 601 Challenges/DACSS601_data/COL_Index.csv")
head(COLIndex) #read in the dataset and show the first couple of rows

NYC.Index<-data.frame(0, "New York City",100, 100,100,100,100,100) #adding a new NYC dataframe to add to the COLIndex data
names(NYC.Index)<-c("Rank","Country","Cost.of.Living.Index","Rent.Index","COL.Plus.Rent","Groceries.Index","Restaurant.Price.Index","Local.Purchasing.Power.Index") #matching up the data frame with the correct column names to bind it to COLIndex
COLIndex<-rbind(COLIndex,NYC.Index) #binding the NYC data to COLIndex
tail(COLIndex) #used tail to show New York City was added correctly

write_csv(COLIndex, "COL_Index.NYC.csv") #save a new csv to working directory
