# CropPredict

This project aims to predict winter wheat yields based on location and weather data. It is inspired by  [this](https://github.com/aerialintel/data-science-exercise) data science challenge.

Here I briefly outline the main steps in my approach as well as my main results. A detailed report is also available:  [Full Report](https://github.com/cleipski/CropPredict/blob/master/Full_Report.md)


## Executive summary

A gradient-boosted decision tree regressor turned out to be the best performer. The tuned model achieved an R<sup>2</sup> value of ~0.83 with a root mean square error (RMSE) of 5.3 (yield values in the dataset range from 10 to 80). The mean absolute percentage error is ~5%.

<img src="https://github.com/cleipski/CropPredict/raw/master/images/model_performance.png" width="200"/>


## Technical overview

Below I outline briefly the main steps in the workflow. The  Jupyter notebooks linked in each step contain the code (with comments) that was used to achieve the results.

| Task | Summary | Notebook|
| --- | --- | -- |
| Explore and clean data | Exploring data structure and impute missing values. | [01](https://github.com/cleipski/CropPredict/blob/master/01_data_exploration.ipynb) |
| Collect additional data | For each location determine elevation and length-of-day at a unified date. | [03](https://github.com/cleipski/CropPredict/blob/master/03_elevation_and_length_of_day.ipynb) |
| Feature engineering | Construct higher-level features by characterizing each location across the season. | [04](https://github.com/cleipski/CropPredict/blob/master/04_feature_engineering.ipynb) |
| Statistical analysis | High-level statistical exploration of final feature set. | [05](https://github.com/cleipski/CropPredict/blob/master/05_statisctical_feature_exploration.ipynb) |
| Select algorithm | Compare a number of algorithms using cross validation to identify the most promising performers for this data/feature set. | [06](https://github.com/cleipski/CropPredict/blob/master/06_algorithm_selection.ipynb) |
| Tune model | Tune hyper-parameters of a gradient-boosted tree regressor using cross validation, learning curves and validation curves. Find best balance between performance and bias-variance tradeoff. | [06](https://github.com/cleipski/CropPredict/blob/master/06_algorithm_selection.ipynb) |
| Establish model performance | Use a 30% hold-out test set to compare predicted and observed yields. | [06](https://github.com/cleipski/CropPredict/blob/master/06_algorithm_selection.ipynb) |



## Future work

While the performance of the model appears quite good, a close inspection reveals that it has a tendency to under predict at high yield values (>60 observed). There is also some residual overfitting, even after careful tuning.

In future iterations, these issues could be addressed by:

* getting more data,
* engineering additional and/or different features, or
* using ensemble techniques by combining the results of different models.
