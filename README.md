# Airbnb Boston Analysis

Link to blog post: [here](https://medium.com/@timwinkler99/data-driven-thoughts-on-subletting-your-space-in-boston-on-airbnb-a5d242df0107)

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
3. Can we predict the price using the listings feature and reviews?

## Data Understanding & Modeling

There are 3 files available for this anlysis:

- `calendar.csv` contains all the listings from 2016-09-06 until 2017-09-05, their availability and price for any given day
    - `availability` for a listing does not differentiate between a booked night vs an unavailable night
- `listings.csv` contains all listings and their details which can be found when browsing them on Airbnb
- `reviews.csv` contains reviews from people who stayed in a given listing

Details about the dataset can be found in this [sheet](https://docs.google.com/spreadsheets/d/1iWCNJcSutYqpULSQHlNyGInUvHg2BoUGoNRIGa6Szc4/edit#gid=982310896)

### Question 1:

To address question #1 we could obtain average prices for each liststing and average these again over the neigbourhood, which has already been polished up in column `neighbourhood_group_cleansed`.

Popular listings are already booked well ahead and therefore no price is available in the `calendar.csv` file. In fact for 50.87% of all days, the actual price is not available. Even tough for some listsings the price is the same troughout the year (34.65%), for the majority of listsings the price varies. To account for that I will fill the missing values with using a linear interpolation.For those dates and listsings which do not have a price available at the beginning of the period I apply a backward fill.

![](mean_price_neighbor.jpeg)

There is quite a significant difference in price between Mattapan and Leather District. Average annual prices per neighbourhood increase steadily, however there is a significantly large difference between Mission Hill and Charlestown.

### Question 2:

For question #2 I will calculate daily means across all listings which have at least one price.

![](mean_price_per_night.jpeg)

Prices from April-November are particularly high. The peak in April is most likely related to the [Boston Marathon](https://www.baa.org/races/boston-marathon).

## Data Modeling

Adressing question #3 is more complex than questions #1 & #2. The goal is to use features of the listing (type, rooms, neigborhood, ...) and reviews (no. of reviews per listing, frequency of reviews, ...) to predict the price of a given listing on the day the `listings.csv` data has beeen scraped. To start modeling we have to decide on features we want to include in the model.

| Feature                           | Current Format | Target Format                       |
|-----------------------------------|----------------|-------------------------------------|
| id                                | int            | int                                 |
| last_scraped                      | object         | date (to calc. diff with host since)|
| host_since                        | object         | datediff until 2016-09-07 in days   |
| host_response_time                | object         | categorical                         |
| host_response_rate                | object (in %)  | float                               |
| host_acceptance_rate              | object (in %)  | float                               |
| host_is_superhost                 | object         | categorical                         |
| host_listings_count               | int            | int                                 |
| host_has_profile_pic              | object         | categorical                         |
| host_identity_verified            | object         | categorical                         |
| neighbourhood_cleansed            | object         | categorical                         |
| property_type                     | object         | categorical                         |
| room_type                         | object         | categorical                         |
| accommodates                      | int            | int                                 |
| bathrooms                         | float          | float                               |
| bedrooms                          | float          | float                               |
| beds                              | float          | float                               |
| bed_type                          | object         | categorical                         |
| amenities                         | object (set)   | categorical                         |
| price                             | object (in $)  | float                               |
| cleaning_fee                      | object (in $)  | float                               |
| guests_included                   | int            | int                                 |
| minimum_nights                    | int            | int                                 |
| review_scores_rating              | int            | int                                 |
| review_scores_accuracy            | int            | int                                 |
| review_scores_cleanliness         | int            | int                                 |
| review_scores_checkin             | int            | int                                 |
| review_scores_communication       | int            | int                                 |
| review_scores_location            | int            | int                                 |
| review_scores_value               | int            | int                                 |
| cancellation_policy               | object         | categorical                         |
| reviews_per_month                 | int            | int                                 |

Depending on the features current format, we have to make some changes (e.g., host_since_days, all kinds of rates and dollar amounts, set of amenities, cleaning_fee).
For the other categorical and numerical features we create dummies and fill missing values with the feature columns mean respectively.

## Result Evaluation

### Question 3:

The rsquared on the training data was 0.4704.  The rsquared on the test data was 0.2789. Obviously, there is lot of space to improve this model, for example adding additional features, like no of pictures from the offered space, the quality of those picture, the quality of furniture, etc.

## Licnesing, Authors, and Acknowledgements

- Data Source: [Boston Airbnb Open Data](https://www.kaggle.com/datasets/airbnb/boston?resource=download)