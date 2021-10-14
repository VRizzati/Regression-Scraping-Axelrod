# The NYC launch of Axelrod Development Group


## Abstract
For the launch of Axelrod Development Group (Axelrod) in NYC, I have been asked to support in analyzing the NY Real Estate market on the sellers side and provide both an interpretation of  what are the factors driving the largest impact on property prices as well as building a model for the Investment team to get an estimate of property price based on the inputted features.<br/>
I worked with scraped data from [Zillow](https://www.zillow.com/) and zipcode-area mapping data from [NYC Gov](https://www1.nyc.gov/assets/doh/downloads/pdf/ah/zipcodetable.pdf) and built a Linear Regression Model representing the relationship between a listing's price (target) and a multitude of features. Across the multiple iterations, the chosen model provides the best balance in satisfying both the interpretative and the predictive goals.

## Design
The design of this project can be distinguished between scraping and linear regression. 

**1. Scraping**  
As a first step, I used Selenium to scrape the listings urls from the main search page, by borough (interestingly, Zillow display only 20 pages for every search). Then, I used this list of urls to scrape the individual listings' page sources and save them locally as html files. Finally, with BeautifulSoup, I scraped the needed information from the individual html files. Aside from the complexity of scraping a dynamic and monitored site like Zillow, once I had the html files saved locally, the scraping process was significantly faster than scraping listing by listing with Selenium.

**2. Linear Regression**  
I started by exploring and cleaning the data of duplicates, missing values, zeros and outliers. From circa 1300 observations to start with, I ended up with around 700 clean datapoints. Then, I applied feature engineering to several features, dropped some irrelevant features and iterated through 12 linear regression model using a simple validation methodology. Finally, I was able to isolate one model which scored the best on R^2/Adjusted R^2 and had the most interpretable coefficients. I tested this model against its Ridge regularized version in a cross-validation scheme. Finally, I chose the Linear Regression model as the model of choice and I ran some tests to measure other relevant stats and check that the linear regression assumptions were met. 

## Data
From Zillow I gathered data on around 1300 properties for sale in the five boroughs of NYC. In particular, I was interested in:
- price
- sqft
- zipcode
- number of schools
- school rating
- number of bedrooms and number of bathrooms
- walk score and transit score
- property view
- property type
- pool presence
- parking presence
- year built

Since the zipcode proved to be a too granular level of aggregation for my analysis, I have decided to first map zipcodes to neighbourhoods and then to boroughs. The model performed significantly better with the borough features. The full mapping has been conducted by trilangulating information from NYC Gov, Google Maps and Zipmap.

## Algorithm
Once explored and cleaned, I have started baselining the model and obtained a R^2 of only 0.32. In an iterative fashion, I applied the following transformation to the data:
- Removed outliers from both the target and a few features
- Transformed sqft to a categorical ordinal feature (small_size, medium_size, large_size)
- Mapped zipcodes to first neighbourhoods and then boroughs
- Summed bedrooms and bathrooms and include only the aggregate variable *rooms* in the regression
- Summed walk and transit scores and, due to their skewed distributions, transformed the *walk_transit_score* into a binary feature (1 for a score in the top 1% of the distribution and 0 otherwise)
- Combined property types with very few observations in an aggregate feature called *Other Property Types*
- Transformed pool from a categorical nominal to a categorical binary feature (1 if there is a pool and 0 otherwise)
- Dropped number of schools and view-related features

From the 12 linear regression models generated through these iterations and scored according to a simple validation methodology, I selected the one presenting the highest R^2 and the most interpretable coefficients. 

Finally, I experimented a Ridge regularization on the model and compared it to the Linear Regression model in a cross-validation scheme. The Linear Regression model outperformed the Ridge Regression model both on predictability and, naturally, interpretability.

## Tools
- BeautifulSoup and Selenium for scraping
- Pandas, Numpy and scikit-learn for EDA, cleaning and modeling
- Matplotlib and Seaborn for visualizations

## Communication
The output of this project is showcased in the presentation included in this repo. 

As a complement, see below the diagnostic plots.
<img width="1074" alt="diagnostic_plots" src="https://user-images.githubusercontent.com/68084582/120348201-f7b5c800-c2ca-11eb-9c3b-4ae9dfecab60.png">
