# mlfs-book

O'Reilly book - Building Machine Learning Systems with a feature store: batch, real-time, and LLMs

# Run Air Quality Tutorial

See [tutorial instructions here](https://docs.google.com/document/d/1YXfM1_rpo1-jM-lYyb1HpbV9EJPN6i1u6h2rhdPduNE/edit?usp=sharing) # Create a conda or virtual environment for your project
conda create -n book
conda activate book

    # Install 'uv' and 'invoke'
    pip install invoke dotenv

    # 'invoke install' installs python dependencies using uv and requirements.txt
    invoke install

## PyInvoke

    invoke aq-backfill
    invoke aq-features
    invoke aq-train
    invoke aq-inference
    invoke aq-clean

# Lab Description

#### Lab Group 1337

Dashboard: https://lukasfri.github.io/id2223-mlfs-book/air-quality/

## Lab Tasks Completed

[x] Write a backfill feature pipeline that downloads historical weather data (ideally >1 year of data), loads a csv file with historical air quality data (downloaded from https://aqicn.org) and registers them as 2 Feature Groups with Hopsworks.
[x] Schedule a daily feature pipeline notebook that downloads yesterday’s weather data and air quality data, and also the weather prediction for the next 7-10 days and update the Feature Groups in Hopsworks. Use GH Actions or Modal.
[x] Write a training pipeline that (1) selects the features for use in a feature view, (2) reads training data with the Feature View, trains a regression or classifier model to predict air quality (pm25). Register the model with Hopsworks.
[x] Write a batch inference pipeline that creates a dashboard. The program should download your model from Hopsworks and plot a dashboard that predicts the air quality for the next 7-10 days for your chosen air quality sensor.
[x] Monitor the accuracy of your predictions by plotting a hindcast graph showing your predictions vs outcomes (measured air quality).
[x] Update your Model by adding a new feature, lagged air quality for the previous 1 day, 2 days, and 3 days. Measure and explain the performance improvement or regression for these features.
[x] Provide predictions for all air quality sensors in a Swedish city/county.

## City/County Sensors

For this lab we chose to cover the county of Örebro. Örebro county contains two PM 2.5 air quality sensors available on aqicn.org, One in the city of Örebro itself (available at https://aqicn.org/station/@249862) and one in the city of Nora (available at aqicn.org/station/@249862).

## E Level Assignment: Using the Code Skeleton

To complete the E level segment of the assignment we chose to first start with the sensor in the city of Nora as it had longer and more reliable historical data available than the sensor in Örebro city. We used the historical sensor and weather data to backfill our feature stores on Hopsworks.ai using the provided jupyter notebook, then trained our XGBoost model using the provided jupyter notebooks as well, finally we made sure the GitHub actions could successfully run and publish a GitHub Pages dashboard.

## C Level Assignment: Adding Lagged Air Quality Measurements to the Model

To add lagged air quality measurements to the prediction model, we modified the provided jupyter notebooks to store the previous three days of air quality measurements along with each day's air quality measurements, and used that data while training the prediction model. When performing inference more than one day ahead, we use the predicted air quality of previous days to stand in for the lagged air quality measurements for the days where real measurements are not yet available.

## A Level Assignment: Supporting All Sensors in Örebro County

For supporting all sensors in Örebro County we only had one sensor to add (Örebro/Rudbecksskolan) due to there only being three sensors in the county, of which one was O3 only. Therefore, we did some minor modifications to support multiple cities (for example in the sensor JSON). During our work we found that Hopsworks has a bug/is lacking support for proper unicode - writing Örebro in APIs turned out to yield an error. Therefore, since we only had two cities, we did a workaround where if the city wasn’t Nora, we assumed it was Örebro.

To support multiple cities in the GH actions we used a matrix strategy of environments to run the workflow for each of the cities upon start. Running the pipelines in parallel worked fine and yielded no real slowdown in our action time.
