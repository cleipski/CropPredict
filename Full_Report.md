# CropPredict - Summary Report

### **work in progress**

This project aims to predict winter wheat yields based on location and weather data. It is inspired by  [this](https://github.com/aerialintel/data-science-exercise) data science challenge.

In this report I will provide a high-level overview of my approach to the project, my findings, and my main results.

For technical details I encourage the reader to inspect the Jupyter notebooks in this repository.

## Data sources

To quote from the original text:

"We're providing you with two years worth of Winter Wheat data. These data are geolocated to specific lat-longs and counties.

* Columns A-E in the file provide information on location and time.
* Columns F-X are raw features, like NDVI or wind speed.
Day in Season is a calculated feature defining how many days since the start date of the season have occurred.
The yield is the label, the value that should be predicted. **Note**: this yield label is not specific to a lat/long but is for the county. Multiple lat/longs will have the same yield since they fall into a single county, even if that individual farm had a higher or lower localized yield."

## Task

The task is "to try and predict wheat yield for several counties in the United States."

Considering the nature of the data, this formulation is somewhat open to interpretation. Generally, I would have started by discussing the data and by trying to better understand the eventual application or business incentive. Additional information and discussion with stakeholders can help significantly in ensuring the machine-learning model answers the right questions.

## My approach

As it is, I decided to build a model that is well suited for applications that would ask questions like: "Is location X - which has good coverage of historical weather data - suited for winter wheat and what kind of yield can I expect?"

In its current form, the model is less well suited to answer questions along the lines of: "At location X my weather data so far this season is Y. What kind of yield can I expect at the end of the season?"

But see below for ideas of how to potentially address
this last question as well.

My approach to build this model was to characterize each location across the full season. I essentially marginalized over time, engineering aggregated weather-based features for each location. See the 'Feature engineering' section for more details.


## Data exploration and munging

Each year in the data includes about 1000 unique locations. More than 80% of the locations are common to both year's data. The time frame covered is end of November to beginning of June the following year. Most locations have measurements reported on almost every day during the season (>153 days out of 186 days).

In each year, a small number of locations have measurements reported only on less than 14 days out of the full 186 day period.

These locations were removed from the data. Given their limited coverage, it is difficult to reliably engineer the features used in this current model.  With this approach, ~5% of the locations were excluded each year, accounting for >0.2% or the raw data. But also see the section 'Final words' for some ideas on how to potentially recover some information from the excluded locations.


As provided, the data set was already fairly clean and included only a small number of missing or NULL/NaN values. Missing data was highly concentrated in the 'pressure' and 'visibility' columns. Because these  weather-related measurements are likely to change with time and region, it makes little sense to use global averages to impute the missing values. Therefore I chose to adopt the following procedure: for each location with a missing weather-related value, I searched for the *geographically closest* location that had the value in question reported *on the same day*. The assumption here being that the geographically nearest value on the same day is more representative of the missing value than the average of previous and following days records at the target location.



## Additional data sources

I wanted to include additional features that I thought might be relevant for this study.

* Elevation: For each location I included the elevation as provided by the [Google Maps Elevation API](https://developers.google.com/maps/documentation/elevation/start).

* Length of Day: For each location I included the length of the day at the last date in each year's data (typically June 3rd). This provides a proxy for the hours of sunlight each location potentially receives. This was calculated using the [Astral](https://pythonhosted.org/astral/) package for python.


## Feature engineering

The features I used for the modeling were either taken directly from the data as-is (longitude, latitude, yield), taken from the additional data sources (elevation, length of day), or engineered from the raw data.

In support of the feature engineering, I calculated a few intermediate features:

* daily average temperature: the average of the daily maximum and minimum temperature.

* daily temperature difference: the difference of the daily maximum and minimum temperature.

The final features that were used in the modeling are:

| Feature | Description |
| --- | --- |
| longitude | The geographical longitude of the location in degrees. |
| latitude | The geographical latitude of the location in degrees. |
| elevation | The elevation of the location in meters. |
| LOD | The length of the day at the location, calculated as the difference between sunrise and sunset time. |
| total_precipitation | The total precipitation during the season. Calculated as the cumulative sum of the raw feature 'precipAccumulation'. |
| minMAT30 | Minimum average temperature in a 30-day period. A rolling window average over the daily average temperatures was taken with window-size of 30 days. The minimum of the resulting values gives the 30-day period which shows the lowest average temperatures over 30 days. |
| maxMAT30 | Same as above, but now for the maximum average temperature in a 30-day period. |
| ratioMNDVI30 | Similarly to minMAT30 and maxMAT30 I found the appropriate min/max values for NDVI in a 30-day rolling window and then took the ratio of these values. |
| mean_wind_speed | The simple mean wind speed at a location over the full period. |
| mean_temperature_diff | The mean of the daily temperature differences. |
| std_temperature_diff | The standard deviation of the daily temperature differences about the mean. |
| yield | The target variable. The crop yield at the end of the season on a county basis. |


Before feeding the features into the modeling, I performed feature scaling by removing the mean and scaling to unit variance.



## Algorithm selection

A simple correlation analysis of the final data showed that there is no obvious strong linear correlation between most features and the target variable. However, some features are clearly correlated linearly.

This already indicates that purely linear models may not be the best algorithms for this application. But regularization can help to alleviate co-linearity issues, while introducing higher order (polynomial) features can help with the non-linearity.

Tree-based ensemble methods (random forest, gradient boosting) are typically good at handling non-linear feature interaction and co-linearity. These models can be prone to overfitting which can be
handled by careful tuning of the hyper-parameters.

I decided to run a number of algorithms (see  [06_algorithm_selection.ipynb](https://github.com/cleipski/CropPredict/blob/master/02_data_exploration_and_modeling.ipynb)). Using 5-fold cross validation I compared their performance and found that using mostly default settings, the random forest regressor performed the best, followed by nearest-neighbor regression, L2 linear regression with polynomial features, and support-vector regression using an 'RBF' kernel.

This confirmed the earlier notion that purely linear models are not appropriate for this data/feature space. Surprisingly, gradient boosted trees (GBTs) - a recent favorite among many machine-learning competitions - performed poorly. But this algorithm has a sizable number of hyper-parameters. Some tuning of the parameters actually brought the performance up to levels that exceeded the random forest regressor.

Tuning the hyper parameters of the random forest regressor I was not able to achieve the same performance as with the GBTs. The L2 linear regression with polynomial features lacked even more in tuned performance. Nearest-neighbor regression was clearly overfitting for most parameter combination that would provide good performance. The only real alternative to the GBT performance was a tuned version of the support-vector regression (SVR) using an 'RBF' kernel.


## Model tuning and performance

Out of the two promising algorithms (GBT and SVR) I decided to proceed with a gradient-boosted tree regression model. My argument for this decision is twofold:

* GBTs have been very successful in recent years and I wanted to explore this algorithm further.
* Both competing models are fairly complex with a number of relevant hyper-parameters. So there was no reason to chose one over the other based on complexity (at similar performance you would typically prefer a less complex model over a more complex one).

Expanding the GBT parameter search mentioned above I identified combinations of hyper-parameters that maximize performance. I studied learning curves as well as validation curves to investigate the bias-variance tradeoff.  The final result is a set of parameters that for the current dataset provides good performance while limiting overfitting.

<img src="https://github.com/cleipski/CropPredict/raw/master/images/learning_curve.png" width="400"/>

The learning curve of the tuned model shows that there are still some issues with slight overfitting, but overall the performance and variance look promising. Increasing the 'n_estimators' parameter in the GBT model would have further increased the performance score, but at the cost of increased overfitting. It seems likely that the overfitting could be alleviated by including more training data.


The GBT model also provides access to feature importance ranking:

<img src="https://github.com/cleipski/CropPredict/raw/master/images/feature_importance.png" width="400"/>


The final performance of the tuned model was established using a test set (70/30 split) for which I compared model predictions to actual yield numbers.

<img src="https://github.com/cleipski/CropPredict/raw/master/images/model_performance.png" width="400"/>

The R<sup>2</sup> value of the final model is ~0.83 with a root mean square error (RMSE) of 5.3 (yield values in the dataset range from 10 to 80). The mean absolute percentage error is ~5%.

At very high observed yields (>60) the model appears to consistently under-predict. At lower yields the model seems well balanced.

Comparing the observed and predicted yield on the test set:

<img src="https://github.com/cleipski/CropPredict/raw/master/images/compare_yield.png" width="800"/>



## Final words


*What challenges or compromises did you face during the project?*

My main challenge was being unfamiliar with the subject matter. More domain knowledge would have helped in engineering more powerful features and provided better intuition in judging performance.

The compromise was then to use my best judgement for overcoming my limited domain knowledge as well as having to make assumption on the concrete business incentive I would like to address (see comments above in "My approach").

It was also tricky to overcome overfitting completely. Getting more data would have been an obvious solution, but for brevity's sake I decided against this effort.
Switching to a different algorithm might have helped, but (possibly) at the cost of performance. More careful feature engineering has the potential to offset this effect. Or using ensemble techniques.


*What did you learn along the way?*

It was a great exercise that covered the whole spectrum of a machine-learning project. From data munging over algorithm selection to model tuning and presentation. A good opportunity to get more familiar with the GBT algorithm and the effect of the (numerous) hyper-parameters.


*If you had more time, what would you improve?*

Get additional data for previous/following years and/or for more locations. Learn more about the subject matter and try to engineer more or better features.

Combining the output of different models using ensemble techniques could boost overall performance and limit overfitting.

Try to create a model that answers the question: "At location X my weather data so far this season is Y. What kind of yield can I expect at the end of the season?"

One approach would be to take location X's data and compare its profile with the existing locations. Some sort of similarity measure could then inform me about which known locations X is most similar to, which in turn provides an estimate of the yield Y from the existing data.

Alternatively, a new model could be constructed that maps each individual (daily) measurement directly to the yield. Considering how each location's seasonal weather trends are likely to determine (at least partly) the yield, the problem of predicting outcome from individual measurements will be a challenging (but highly interesting) task.
