# Rent Price per Night Prediction

![project header](images/header.png)

## Dataset
The dataset for this project was given by Incidium, and is about advertisements from New York City on a rental platform.
It consists of 48.894 rows with 16 columns:

- **id**: Acts as a unique key for each ad in the app data
- **nome**: Represents the name of the ad
- **host_id**: Represents the id of the user who hosted the ad
- **host_name**: Contains the name of the user who hosted the ad
- **bairro_group**: Contains the name of the area where the ad is located
- **bairro**: Contains the name of the neighborhood where the ad is located
- **latitude**: Contains the latitude of the location
- **longitude** Contains the longitude of the location
- **room_type**: Contains the type of space for each ad
- **price**: Contains the price per night in dollars listed by the host
- **minimo_noites**: Contains the minimum number of nights that the user must book
- **numero_de_reviews**: Contains the number of reviews given to each listing
- **ultima_review**: Contains the date of the last review given to the listing
- **reviews_por_mes**: Contains the number of reviews provided per month
- **calculado_host_listings_count**: Contains the number of listings per host
- **disponibilidade_365**: Contains the number of days the listing is available for booking

## Objectives
The main objective of this project is:

**To develop a price prediction model from the offered dataset and put it on production**

To achieve this objective, it was further broken down into the following technical sub-objectives:

1. To perform in-depth exploratory data analysis of the dataset
2. To engineer new predictive features from the available features
3. To develop a supervised model to predict the price
4. To put the model in production with a pkl file

## Main Insights
From the exploratory data analysis, these were the main points that stood out:
- Excluding the coordinates columns, all others have a heavily positively skewed distribution, with mode < median < mean
- `bairro_group` shows that Manhattan and Brooklyn are by far the most popular locations, with approximately 40k ads in these two neighborhood groups

![Popular_Locations](images/bairro_group.png)

- `room_type` is dominated by the categories entire home/apt and private room, which is understandable

![Popular_Rooms](images/room_type.png)

- `latitude` and `longitude` showed an excellent behavior in relation to price when sorted by area. It's clear that the neighborhoods located more in the center of the values of both columns tend to be more expensive, and the ones located at the extremes tends to be the cheapest

![Latitude_Longitude_Price](images/latitude_longitude.png)

- `numero_de_reviews` can be a little misleading. The majority of ads have 0 reviews, but the graph shows that the higher the number of reviews, the lower the max price that it's possible to find (in general). This can be attributed by different reasons, for example, ads with a lot of reviews can indicate that the place has a high rate of booking, which means that the host can easily find someone to book the ad and is a constant form of income, so the host knows that to remain competitive, the price needs to decrease. For the ads with no reviews, it can be newer ads where the host doesn't know the price that attracts the most attention, so the price ranges from the minimal to the max. `reviews_por_mes` follows the same behavior as `numero_de_reviews`

![Numero_de_Reviews_Behavior](images/numero_de_reviews.png)

- The column `minimo_noites` does have an impact on price with a non-linear relation, but because of the outliers values that don't follow this relation, it may not have a very big impact on the model. Also, for private rooms and shared rooms, we can clearly see a non-linear relation to the price, and for entire home/apt, the relation is visible, with a few points being outliers

![Minimo_Noites_Price](images/minimo_noites.png)

- `disponibilidade_365` didn't show an impact on price during all steps of the EDA, and was dropped.

## Engineered Features and Data Distribution
From the knowledge gained during the EDA, the following feature was created:
- `distance_to_city_center`: This feature was created using the Haversine formula to calculate the distance, in kilometers, from the latitude/longitude of the row to the approximate coordinates of New York City's center. With it, the correlation matrix was plotted using the **spearman** method to handle the outliers and non-linear relations, and the new feature showed a good correlation to `price`

![Correlation_Matrix](images/correlation_matrix.png)

Since the EDA showed that a lot of numerical features had very highly positive skewed distributions, log and cubic transformations were tested on each of these columns, with a plot of their original distribution and the new ones after these transformations were applied. Also, a boxplot of the transformed feature was made to verify if the number of outliers was reduced or even if they were gone.
- `minimo_noites` had an original skewness of 11.63. The best transformation was the log one, reducing the skewness to 1.47, with a small decrease in the number of outliers
- `numero_de_reviews` had an original skewness of 3.68. The best transformation was the log one, reducing the skewness to 0.36, with all outliers gone
- `reviews_por_mes` had an original skewness of 3.3. The best transformation was the cubic one, reducing the skewness to 0.19, with a reduced number of outliers
- `calculado_host_listings_count` had an original skewness of 7.92. The best transformation was the log one, reducing the skewness to 3.28, but with a lot of outliers
- `distance_to_city_center` had an original skewness of 1.11. The best transformation was the cubic one, reducing the skewness to -0.12, with a small decrease in the number of outliers

Since `latitude` and `longitude` were not transformed (already had skewness very close to 0), all data was scaled using the Robust Scaler, to better handle the outliers, after the columns were transformed.

## Models
