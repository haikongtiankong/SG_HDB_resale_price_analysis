# SG_HDB_resale_price_analysis
## Problem Statement
Recent years, there has been high volatility in the housing sales price in Singapore and due to rising cost and 
pandemics, we are seeing a lot of variation in the housing market. Many young married couples have not 
been able to get themselves more financially ready in starting a family. We hope to help them by providing 
in-depth analysis to future housing prices so that they can better estimate their purchasing power.
## Proposed Data Collection Technique
Our technique of data collection is to access government published data and merge them into one big data 
warehouse for analysis. We have chosen the chosen the HDB (Housing and Development Board) Price 
index from the openly available data from Singapore government website data.gov.sg/infrastructure. The 
team approach is to analyse the dataset readily available and include independent variables from external 
datasets to predict prices of housing annually within the year range from 2009 to 2022. The external data 
likes Consumer price indexes across the year range which we have decided to use as part of our data 
prediction variable. Our goal is to predict future selling prices of HDB houses in Singapore base on the 
house location, flat type, floor, and economic situation.
We have also explored different data found in the government website; examples of building material cost 
are one of them. Searching extensively within the data domain to acquire relevant information which we 
think will be useful. 
Below table shows the list of data sets which we have acquired and merged.

* Resale Flat Prices based on approval 2000 Feb to 2012
* Consumer price index at expenditure group level 2019 as base year monthly
* Resale flat prices based on registration date from Mar2012 to dec 2014
* Private residential property rental index base quarter 2009 Q1-100 quarterly
* Resale flat prices based on registration date from Jan 2015 to dec 2016
* HDB property information
* Resale flat prices based on registration date from Jan2017 onwards
* One Map for Location point accuracy

## Data Pre-processing
As there are many data columns available in all the data sets, we need to perform data pre-processing to 
cleanse the data before we can perform analysis.
### Data enhancement:
We use an opensource api called Onemap to search for the property information of the houses and amenities 
around houses, such as mrt, malls, schools, and parks. Then, we use these information to acquire the 
coordinates of houses and all the amenities in order to calculate the distance from each house to its closest 
amenities, the number of amenities within the 5km range of the house, as well as the distance from the house 
to the downtown area. All these numerical features are then added as out new features
### Data merging:
After acquiring the coordinates and other geographically relevant features, we first merge all the features in 
one csv, to facilitate subsequent feature engineering.
### Pre-visualization by certain features:
In order to develop a better understanding of our dataset, we visualize the price distribution and the annual 
price trend by certain features. Moreover, we choose to use price per square meter as our target instead of 
the overall resale price.
F
We also note that the flat type and the region of the house are influential to the price as well. As can be seen 
from figures below, executive room has a more steady and lower price than other type of rooms. It is also 
obvious that houses in the central region of Singapore are likely to have a higher price, while the house price 
in the north seems to be the cheapest on average.
FF
### Inflation adjustment based on economic indicators:
Based on the three economic indicators we acquire, namely cpi for accommodation, cpi for private 
transport, and private residential property rental index, we perform inflation adjustment on the resale prices 
of houses by dividing the original price by the weight of the three indicators. 
With economic development, the currency tends to depreciate, but the apparent amount remains unchanged, 
which means inflation. The data calculated according to the nominal amount of currency is the nominal 
price, and the data is processed by dividing by cpi and other means to eliminate the impact of currency 
depreciation, and the result is the actual price
### Feature selection:
While we have merged our dataset, there are still many features that contain duplicate information. We 
manually eliminate these features by common sense. For example, features ‘street name’ and ‘block’ 
contains information on the street name of the house and its block number. However, feature ’addr’ is a 
detailed address that both include the street name and the block number, so the ‘street name’ and ‘block’ are 
filtered in this stage. Other than this, features with Boolean data type but only have 1 value are also filtered.
### Feature encoding:
In order for the model to train with numerical values, we do feature encoding on certain features. Label 
encoding is performed on features that have a clear order, like ‘storey range’, or ‘flat type’. Label encoding 
can map character type features to integers, to make sure that the information of such features will not be 
lost. One-hot encoding is also used in dealing with the region of the houses. However, if the category 
number is too large for a feature, using one-hot encoding could result in sparse matrix that leads to converge 
of the model terribly slow.
Target encoding, also known as mean encoding, can be used to deal with features with many categories, as it 
uses the average target value to replace each value in the feature column by category. Since we have 
thousands of addresses in our feature ‘addr’, and many categories in features ‘flat model’ and 
‘'bldg_contract_town'’, we decide to use target encoding on these features
### Removing samples with missing values:
Next, we check for all the samples that contain missing value in our dataset and remove them from our 
dataset. The influence of ignoring this information is negligible, because there are around 300k samples in 
our dataset, while only about 800 samples have missing values.
### Removing outliers:
The last step of our feature engineering is to remove some outliers from the dataset and hopefully to make 
the model have a better expression of the overall samples.
In this step, we remove abnormal values with box graph. From the conclusion of pre-visualization of our 
dataset, we believe the geographic location is one of a key factor in predicting price, so we choose to use 
coordinate ‘X’ and ‘Y’ to detect outliers and remove all outlier samples. ‘floor_area_sqm’ is also a chosen 
feature in detecting outliers, as we think some rare houses with very small or large area may also affect the 
final performance of the model.
During this phase, we remove about 700 outlier samples from our dataset.

## Training process
After feature engineering stage, we split our data into training and testing set. The training set contains all 
house price data from Jan 2009 to Dec 2021, while the testing set contains all house price data from Jan 
2022 to Jun 2022. 
For the chosen three ensemble models, we use GridsearchCV from Sklearn to look for the optimal super 
parameters of each model. Grid search tries every specified parameter combination through loop traversal 
and uses cross-validation to verify these parameters. The best parameter combination is our final settings.

## Evaluation of models
After the models are trained, they will be evaluated on the test set. The evaluation metrics are r2 score and 
root mean square error (rmse). We also divide the rmse by the average areas of all houses, to see the price 
prediction error per square meter. As is shown in the figures below, the Xgboost regressor outperformed the 
other two models and are chosen as our final model.
FF

## Feature importance
Fig.6 shows the ranking of feature importance given by our optimal model. As is shown, the area and floor 
level are the most crucial factors in the predictions of house prices. The effect of year for sale, the distance 
to downtown area, the date of lease commences of houses, the flat type, and the distance to the closest 
hawker are also considered as influential features.
F

## Challenges and potential failures to the prediction
Due to the high volatility of current economic factors, we are seeing a huge variation of the costing for each 
year, this is even more significant during the Covid pandemic. The second consideration factors will be the 
consumer price index which typically have an external influence on the global economic situation. This is 
exceptionally true when there is a global recession or technical recession which will impact interest rates and 
the other external factor is war. War affects logistic and commodities across that specific region and will 
ripple the cost globally that indirectly affects domestic consumer price index.

## Conclusion
The prediction of housing prices could be an important reference to the estimation of purchasing power for 
those who want to buy houses in the future 1 or 2 years. Our optimal model shows a prediction error of 561
SGD per square metre. Though the prediction might not be quite precise, the model stills show a superior 
performance and gives a rough estimation of the housing price, which might also be helpful. 
The prediction of actual housing price will never be an easy problem because there are so many hidden 
variables that we do not consider as features. For example, even the seller's personal wish will also the 
change of a single house price, but this factor is difficult to consider in machine learning processing. In the 
future work, adding more features that concern more economic factors and other data related to people's 
livelihoods with a robust model will further decrease the prediction error
