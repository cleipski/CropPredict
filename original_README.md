# Experimenting with a dataset
from your friends at Aerial Intelligence

## The goal
We want to try and predict wheat yield for several counties in the United States. We've collected some data that should give you a good head start on the exercise.

Once you've finished the exercise, we'd like you to share your insights and performance with us, and how you managed to achieve it. More on that below (in the submitting section)

## The starter data :rocket:
- 2013: https://aerialintel.blob.core.windows.net/recruiting/datasets/wheat-2013-supervised.csv
- 2014: https://aerialintel.blob.core.windows.net/recruiting/datasets/wheat-2014-supervised.csv

## Some context
We're providing you with two years worth of Winter Wheat data. These data are geolocated to specific lat-longs and counties.

- Columns A-E in the file provide information on location and time. 
- Columns F-X are raw features, like NDVI or wind speed. 
- Day in Season is a calculated feature defining how many days since the start date of the season have occurred. 
- The yield is the label, the value that should be predicted.
    **Note:** this yield label is not specific to a lat/long but is for the county. Multiple lat/longs will have the same yield since they fall into a single county, even if that individual farm had a higher or lower localized yield. 

Please exclude CountyName, State, and Date from training as this will result in overfitting and lack of generalization to other states. 

Feel free to split and manipulate this data as you see fit. You can choose to focus on the starter data, or you can look at what additional higher level features you can process out of the starter data, and even grabbing more related data. If you go above and beyond the starter data, please let us know what you did and your insight behind doing so in your explanation.

## Submitting your results

Please create a Git repository on a hosted Git platform like GitHub, etc, and send us a link. Your repository should include any code you've written for the exercise, and a writeup README.md or PDF explaining your findings. IPython notebooks are also great.

Some things to consider for your README:
- A brief description of the problem and how you chose to solve it.
- A high level timeline telling us what you tried and what the results from that were
- What your final / best approach was and how it performed
- Technical choices you made during the project
- What challenges or compromises did you face during the project?
- What did you learn along the way?
- If you had more time, what would you improve?

We care about your thought process and your data science prowess. The better we can understand how you approached the problem, the better we can review your project. Here are a few questions we'll consider:
- Can we understand your thought process? Does your README.md clearly and concisely describe the problem, your solution, and what you did to achieve it? Does your code do what the README.md says it does?
- Can we understand your code? Is your logic clean, consistent, and concise?
- Do your technical choices make sense?
