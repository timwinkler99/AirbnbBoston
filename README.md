# Airbnb Boston Analysis

In this notebook I am going to analyse the [Boston Airbnb Open Data](https://www.kaggle.com/datasets/airbnb/boston?resource=download) dataset. This analysis is divided into 5 steps:

- [Business Understanding](#business-understanding)
- [Data Understanding](#data-understanding)
- Data Preperation
- Data Modeling
- Result Evaluation

## Business Understanding

The questions I am trying to answer are

1. What are the most expensive neighbourhoods in Boston?
2. Is there a price-sesonality?
3. Based on the reviews, are there months where people prefere to visit boston?

## Data Understanding

There are 3 files available for this anlysis:

- `calendar.csv` contains all the listings from 2016-09-06 until 2017-09-05, their availability and price for any given day
    - `availability` for a listing does not differentiate between a booked night vs an unavailable night
- `listings.csv` contains all listings and their details which can be found when browsing them on Airbnb
- `reviews.csv` contains reviews from people who stayed in a given listing

Details about the dataset can be found in this [sheet](https://docs.google.com/spreadsheets/d/1iWCNJcSutYqpULSQHlNyGInUvHg2BoUGoNRIGa6Szc4/edit#gid=982310896)

To address question #1 we could obtain average prices for each liststing and average these again over the neigbourhood, which has already been polished up in column `neighbourhood_group_cleansed`.

Popular listings are already booked well ahead and therefore no price is available in the `calendar.csv` file. In fact for 50.87% of all days, the actual price is not available. Even tough for some listsings the price is the same troughout the year (34.65%), for the majority of listsings the price varies. To account for that I will fill the missing values with using a linear interpolation.For those dates and listsings which do not have a price available at the beginning of the period I apply a backward fill.