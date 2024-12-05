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
```

Here I read my dataset from the working directory within my desktop. I then created a new data-frame to add a row to the original data. This data would consist of the New York City indexes allowing me to easily compare its indexes to other countries.

## Summary of Data

```{r}
sum_stat <- function(x){
  stat <- tibble(
    range=range(x, na.rm = T),
    mean=mean(x, na.rm = T),
    sd=sd(x, na.rm=T),
    na = sum(is.na(x)),
    unique = length(unique(x))
    )
  return(stat)
} #create a sum function that will read the range, mean, sd, na, and unique

sum_stat(COLIndex$Cost.of.Living.Index) #use the function to find the stats of different columns
sum_stat(COLIndex$Rent.Index) 
sum_stat(COLIndex$COL.Plus.Rent) 
sum_stat(COLIndex$Groceries.Index)
sum_stat(COLIndex$Restaurant.Price.Index)
sum_stat(COLIndex$Local.Purchasing.Power.Index)
summarize(COLIndex, unique_age = n_distinct(Country)) #unique values of countries
dim(COLIndex) #dimensions of the data
colnames(COLIndex) #column names of the data
```

I created a function that can calculate the range, average, number of NAs, and the number of unique values of any given numeric columns, also can be known as double or integer columns. Considering this dataset does not have many variables so I wanted to look at the differences among each column. First of all the dataset has 122 countries, which are the amount of rows, and also 8 different variables, which are the amount of columns, their rank (based on their Cost of Living Index), the country, Cost of Living Index, Rent Index, Cost of Living Plus Rent Index, Groceries Index, Restaurant Price Index, and Local Purchasing Power Index. Throughout this I will use the ranks of each country and their variables to look into trends and eventually use the indexes to answer a research question. For now, lets review the basic summary of all the variables to give everyone a baseline introduction to their values before any visuals.

The Cost of Living Index has 108 unique values with a range from 18.8 to 101.1 and an average or mean of 44.02 and standard deviation of 16.87. Comparatively the Rent Index only has 100 unqiue values but with a larger range from 2.4 to 100, meaning no country has an index higher than NYC, the mean is 16.74 with a standard deviation of 13.67. There are also no NA values in the data from any variable. Lastly, here are the last four variables with their ranges, means, standard deviations, and unique values.

-   Cost of Living Plus Rent Index: Range=11.1 to 100, Mean=30.93, SD=14.63, Unique=105

-   Groceries Index: Range=17.5 to 109.1, Mean=44.69, SD=17.72, Unique=105

-   Restaurant Price Index: Range=12.8 to 100, Mean=36.99, SD=19.07, Unique=113

-   Local Purchasing Power Index: Range=2.3 to 182.5, Mean=65.38, SD=39.53, Unique=118

## Exploratory Data Analysis

Below I've created four histograms for the variables I deem necessary to view their patterns and eventually answer my research question. These histograms allow us to look into trends or patterns of different variables, in this case Cost of Living Index, Rent Index, Cost of Living Plus Rent Index, and Local Purchasing Power Index.

```{r}
ggplot(COLIndex, aes(Cost.of.Living.Index))+
  geom_histogram(aes(y = ..density..), alpha = 0.5) +
  geom_density(alpha = 0.2, fill="red") + labs(title="Cost of Living Compared to NYC")
#created histogram of because of continuous variables for all the columns in this dataset. 
graph1<-ggplot(COLIndex, aes(Rank,Cost.of.Living.Index))+ 
  geom_col() +
  labs(title="The Cost of Living Index for Each Rank") #create bivariable bar graph
ggplotly(graph1) #interactive graph
```

First I want to look into the histogram and show that the density peaks between an index of 30-40 and quickly begins to flatten before getting to NYC's index at 100, resulting in a right skewed graph. The ranks of all the countries are based on this index so it is fair to assume this histogram and bar graph are the most likely to have clear patterns. And as we can see the bar graph comparing the ranks to the Cost of Living Index start at 100 with NYC at rank 0 and swiftly get lower to rank 121, however in this graph "Rank 1" actually has a slightly higher index than NYC with 101.1. I've created an interactive graph so it is easier to see the Cost of Living Index for each rank.

```{r}
ggplot(COLIndex, aes(Rent.Index))+
  geom_histogram(aes(y = ..density..), alpha = 0.5) +
  geom_density(alpha = 0.2, fill="red") + labs(title="Rent Index Compared to NYC") #create histogram with the density shown a little opaque to see the histogram still 

graph2<-ggplot(COLIndex, aes(Rank,Rent.Index))+ 
  geom_col() +
  labs(title="The Rent Index for Each Rank") #bivariable bar graph
ggplotly(graph2) #interactive graph
```

While looking at the trends of the histogram for the variable, Rent Index, we see a much steeper right skew and the closest index to NYC is less than 75. Rank 1 is over 50% lower than the Rent Index of NYC, showing just how expense rent is compared to the country with the highest Cost of Living Index through half of 2024. The country with the closest Rent Index is rank 4, Singapore, but that is still 33% lower than NYC. America is known to be expensive to rent and buy houses but this really puts the difference into perspective. The bar graph does not depict any outliers and seemingly gets lower when the Cost of Living Index is lower. Most country's rent is proportional with their cost of living and allows space for other expenses.

```{r}
ggplot(COLIndex, aes(Local.Purchasing.Power.Index))+
  geom_histogram(aes(y = ..density..), alpha = 0.5) +
  geom_density(alpha = 0.2, fill="red") + labs(title="Local Purchasing Power Compared to NYC") #create histogram with the density shown a little opaque to see the histogram still

graph4<-ggplot(COLIndex, aes(Rank,Local.Purchasing.Power.Index))+ 
  geom_col() +
  labs(title="The Local Purchasing Power Index for Each Rank")
ggplotly(graph4)
```

Now this is an interesting graph and really shows how expensive NYC is, considering their Rent Index is the highest but there are plenty of countries with the similar or higher Purchasing Power Indexes. Normally the histogram has a steep right skew and when the index hits 100 there is no density but here there is a second increase beyond 100. Now most of the indexes are around 40 but comparatively to the other graphs this one still shows plenty of indexes around where NYC is. Rank 20, Luxembourg, has an index 82% higher than NYC, a really significant increase.

## Question Analysis 1

When researching throughout this dataset, Russia and China stood out to me because they are both massive countries and it would be interesting to see how they compare to NYC. While creating visuals I noticed Hong Kong was the only city represented and wanted to look into how another major city would compare to NYC. My first question would be represented as so,

1)  How do the biggest countries in the world Cost of Living and Rent Index such as China or Russia compare New York? What stands out? Hong Kong is also represented in the data, does this city compare to NYC? Is the rent index in Hong Kong comparable?

```{r}
COLIndex_1<-COLIndex|>
  filter(Country == "Russia"| Country == "China" | Country == "Hong Kong (China)"| Country == "New York City") #filtered out the correct countries for easier use
COLIndex_1

graph5<-ggplot(COLIndex_1, aes(x=Country, y=Cost.of.Living.Index, fill=Rent.Index)) + geom_col() + labs(title="Cost of Living Compared to NYC") #plotted the filtered countries with another column and made a title
ggplotly(graph5) #interactive graph

graph6<-ggplot(COLIndex_1, aes(x=Country, y=Local.Purchasing.Power.Index)) + geom_col() + labs(title="Purchasing Power Index Compared to NYC") #plotted the filtered countries with another column and made a title
ggplotly(graph6)
```

By creating this interactive graph, I can compare both the overall Cost of Living Index with the Rent Index. The bar graph was filtered to only include the four rows necessary and to create an easy analysis. I chose to fill the graph with the Rent Index of each element because the interactive feature will allow to view both at the same time, keeping my graphs and analysis concise. Once again, emphasizing how expensive it is to live in New York City. However, I am honestly not surprised that Hong Kong is a closer comparison to NYC, considering it is just a city and not a whole country. While the Cost of Living is only 30% lower in Hong Kong, the Rent Index is near 50% lower than what it is in NYC. According to CNBC, last year in 2023 New York City took over Hong Kong's 4 year reign of being the most expensive city to live in for citizens who are not native to the area. Now over a year later, it appears maybe the gap is widening. It's also interesting to look into how similar Russia and China are their indexes are only separated by 2% max. I would have to imagine that this is due to the great amount of land in both countries that is not in adapted by many people.

After seeing the comparison with Cost of Living and Rent Indexes, I wanted to look into Purchasing Power Index, with how a person making the average salary within that area can afford. These results were a lot more surprising and truly shows how expensive New York City is just to live in, not even including other goods and services. Hong Kong's Purchasing Power Index is actually 9.3% higher than New York City's meaning a citizen with an average salary in Hong Kong can afford 9.3% more than what a citizen making the average salary in NYC can afford. Also, China's index increases and becomes a little bit more comparable to NYC than what their Cost of Living Index is. It is kind of alarming how anyone can live in Hong Kong comfortably with an average salary but that same salary in NYC would severely limit financial flexibility and their happiness level.

## Question Analysis 2

The differences between Hong Kong and NYC made me want to look further into the top 50 ranks and how their Rent Indexes and Local Purchasing Power Index compares to NYC. So I created a scatter plot with two dots for each rank separated by color and interactive so you can easily identify them. The question I purpose in this scenario is,

2.  How do Local Purchasing Power Indexes vary among the top 50 ranked countries and does that have any correlation with their Rent Index?

```{r}
COLIndex_3<-COLIndex|>
  filter(Rank <= 50) #filtered out the top 50 ranks
tail(COLIndex_3)

graph7<-ggplot(COLIndex_3)+ geom_point(aes(Rent.Index, Rank)) + geom_point(aes(Local.Purchasing.Power.Index,Rank),color='red') + theme_minimal() + labs(title="Rent & Purchasing Power Indexes for Top 50 COL Index", x="Rent Index & Purchasing Power Index") 
#combined two scatter plots and used color=red to show the difference
ggplotly(graph7)

COLIndex_LPP <- COLIndex_3|>
      mutate(Local.Purchasing.Power.Index = case_when(
        Local.Purchasing.Power.Index > 100 ~ "Above NYC", 
        Local.Purchasing.Power.Index <= 100 ~ "NYC and Below"
        ))|>
  select(Rank, Country, Local.Purchasing.Power.Index) #recoded the column to characters to read clearer and selected the correct columns necessary
head(COLIndex_LPP)

graph8<-ggplot(COLIndex_LPP, aes(Local.Purchasing.Power.Index)) + 
  geom_bar() + 
  labs(title= "Purchasing Power Indexes Compared to NYC") #used that mutated data to create a bar graph of the results
ggplotly(graph8)
```

I chose to use scatter plots for this analysis because I allows me to look along the horizon axis to compare the Rent Index and the Purchasing Power Index. While figuring out the best way to visualize this data, I tried to make a bar graph but the scatter plot turned out better for analysis purposes. New York City is at Rank 0 and only has one dot because they both have indexes of 100. The data point being at the bottom allows me to look above it and see the difference between indexes greater or less than NYC. All of the black points are well below NYC, referencing how much lower their Rent Indexes are but the red points are way more scattered and to me show that the average salary is more versitile outside of New York City. However, the intriguing outlier in this situation is Rank 43 whose Purchasing Power Index is only 2.3 and lower than their Rent Index of 11.8. The scatter plot is great for me to see the visual of the two indexes but when creating this bar graph and recoding the Local Purchasing Power column to read as "Above NYC" and "NYC and Below", I don't have to count all the dots and can tell that 27 countries of the top 50 ranks have higher Purchasing Power Indexes than NYC. Considering all of these countries have lower Cost of Living Indexes, it amazes me that more than half of the top 50 can afford more on their average salary.

## Critical Reflection

Overall, all of these visuals and other statistics have shown me with great evidence how expensive it is to live in New York City compared some of the biggest countries in the world. However, the dataset was limited and I know there are many more than 6 variables when comparing Cost of Living Indexes throughout the world. I also wished to include a happiness level for each of these countries but the data I could find did not fit the right time frame. I feel like that would have been really interesting to look into and compare along side with finances. This dataset does not allow me to answer why NYC is this expensive and while I can find many solutions to this online it would not be able to be incorporated with this data. When putting this project together, I quickly learned that all of these indexes have value and not one is more important than the other it is just about how you want to analyze each country. I hope this data and finished product can be expanded upon and analyzed closer than the broad numbers. The non-data aspects of how these numbers come about is just as important as the data and would bring even more realization and conclusions.

## Bibliography

Adamovic, M. (n.d.). *Cost of living*. Cost of Living. https://www.numbeo.com/cost-of-living/ 

Chiew Tong, G. (2023, June 8). *New York overtakes Hong Kong as the most expensive city in the world for expats, new survey shows*. CNBC. https://www.cnbc.com/2023/06/08/nyc-overtakes-hong-kong-as-most-expensive-city-in-world-for-expats-eca.html 

Hadley Wickham, Hadley, et al. “R for Data Science (2E).” *R for Data Science (2e)*, r4ds.hadley.nz/. Accessed 16 Aug. 2024.

Long, James (JD), and Paul Teetor. “R Cookbook, 2nd Edition.” *R Cookbook, 2nd Edition*, 26 Sept. 2019, rc2e.com/.

Myrios. (2024, July 19). *Cost of living index by country*. Kaggle. https://www.kaggle.com/datasets/myrios/cost-of-living-index-by-country-by-number-2024?resource=download 

R Core Team. “R: A Language and Environment for Statistical \## Computing.” *The R Project for Statistical Computing*, R Foundation for Statistical Computing, Vienna, Austria., 2021, www.R-project.org.
