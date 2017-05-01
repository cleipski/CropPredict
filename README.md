# CropPredict - Summary Report

### **work in progress**

This project aims to predict winter wheat yields based on location and weather data. See [here](https://github.com/aerialintel/data-science-exercise) for additional details.

In this report I will provide a high-level overview of my approach to the project, my findings and my main results.

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

Considering the nature of the data, this formulation is somewhat open to interpretation. Generally, I would have like to start by discussing the data and to better understand the eventual application or business incentive.

## My approach

As it is, I decided to build a model that is well suited for applications that would ask questions like: "Is location X - which has good coverage of historical weather data - suited for winter wheat and what kind of yield can I expect?"

In its current form, the model is less well suited to answer questions along the lines of: "At location X my weather data so far this season is Y. What kind of yield can I expect at the end of the season?"

But see below for ideas of how to potentially address
this last question as well.

My approach to build this model was to characterize each location across the full season. I essentially marginalized over time, engineering aggregated features for each locations. See the 'Feature engineering' section for more details.


## Data exploration and munging

Each year's data includes about 1000 unique locations. A good 80% of the locations are common to both year's data. The time frame covered is end of November to beginning of June the following year. Most locations have measurements reported every day during the season.

## Additional data sources

I wanted to include additional features that I thought might be relevant for this study.

* Elevation: For each location I included the elevation as provided by the Google Maps Elevation API.

* Length of Day: For each location I included the length of the day at the last date in each year's data (typically June 3rd). This provides a proxy for the hours of sunlight each location potentially receives.


## Feature engineering





## Algorithm selection


## Model tuning and performance


## Final words




Some things to consider for your README:

* A brief description of the problem and how you chose to solve it.
* A high level timeline telling us what you tried and what the results from that were
* What your final / best approach was and how it performed
* Technical choices you made during the project
* What challenges or compromises did you face during the project?
* What did you learn along the way?
* If you had more time, what would you improve?

We care about your thought process and your data science prowess. The better we can understand how you approached the problem, the better we can review your project.
