---
layout: post
title: "Can you let me off with a warning?"
subtitle: Predicting the outcome of traffic stops in Columbus, Ohio
comments: true
---

![Officer_picture](https://oklahomalawyer.com/wp-content/uploads/2016/02/TrafficStop.jpg)
_Is it possible to predict the outcome of a traffic stop before it occurs?_

&nbsp;
&nbsp;
&nbsp;

## First look at the data


The [Stanford Open Policing Project](https://openpolicing.stanford.edu/ ) has cataloged tens of millions of traffic stops across the United States, noting the location, time, demographics and of course outcome of these traffic stops. My goal is to determine if it is possible to predict whether there will be an action taken against a person pulled over, either a citation or an arrest – or if the person will be let off with a warning.

I'll be looking at data from Colombus, Ohio between the years of 2012 through 2016. 

![Initial_data](https://github.com/Pdugovich/Pdugovich.github.io/blob/master/img/Original%20Dataframe%20Head.png?raw=true)

Taking a look at the data, it seems like there is plenty to work with. Unfortunately, much of this data is  duplicate information, useless, or constitutes data leakage. 

&nbsp;
&nbsp;
&nbsp;

## Feature Engineering

With a little feature engineering, we can expand some of the information into additional features useful for our predictive model. The date and time were expanded into 'month', 'day', 'day of the week', 'hour', and even 'minute'. Also, there were some missing values. Though I would impute them later, I thought it might be relevant if they were missing to begin with, so I added binary features for if those data points were missing from the observation.

![Engineered_data](https://github.com/Pdugovich/Pdugovich.github.io/blob/master/img/Engineered%20Features.png?raw=true)
 

&nbsp;
&nbsp;
&nbsp;

## Predicting

My best model ended up being an XGBoost Classifier with early stopping. Using this model, I was able to improve the Areas Under the Curve score to 67.65%. Which, while an improvement over the baseline, wasn't as I high as I was hoping for.

Either way, lets check in to see which features had the highest permutation importance in this model.

![Permutation_importances](https://github.com/Pdugovich/Pdugovich.github.io/blob/master/img/Permutation%20Weights.png?raw=true)

It's interesting that hour of the day is by far the most important feature for predicting outcome, even greater than the reason for the stop!  Lets take a quick look at the raw data for hour of the day pulled over to get a feel for when the worst times to get pulled over are.


![Hour_of_day](https://github.com/Pdugovich/Pdugovich.github.io/blob/master/img/by_hour_of_day.png?raw=true)

&nbsp;
&nbsp;
&nbsp;


## Back to the Predictions

Using my model, I mapped out the likelihoods of my test set being let off with a warning and compared it to the actual outcome.

![Comparison](https://github.com/Pdugovich/Pdugovich.github.io/blob/master/img/Test%20Prediction%20vs%20Actual%20Map.png?raw=true)

&nbsp;
&nbsp;
&nbsp;


## Where it goes Wrong

It's clear to see how the predictions _roughly_ match the outcomes, but where does my model go the most wrong?

&nbsp;
&nbsp;
&nbsp;


### The most extreme false-negative
Where my model most strongly predicted the subject would be let off with a warning, but wasn't was this subject:


![Wrong_predicts_warning](https://github.com/Pdugovich/Pdugovich.github.io/blob/master/img/Model%20Incorrectly%20Predicted%20Warning%20iloc.png?raw=true)

&nbsp;

And a shap force plot summarizing _why_ the model made the prediction.


 ![Shap_wrong_predicts_warning](https://github.com/Pdugovich/Pdugovich.github.io/blob/master/img/Model%20Incorrectly%20Predicted%20Warning%20shap.png?raw=true)

&nbsp;

Finally a more expanded chart of each feature's effect on the model

![Features_wrong_predicts_warning](https://github.com/Pdugovich/Pdugovich.github.io/blob/master/img/Model%20Incorrectly%20Predicted%20Warning%20features.png?raw=true)

&nbsp;
&nbsp;
&nbsp;


### The most extreme false-positive

&nbsp;

Now where my model predicted the subject would **NOT** just be let off with a warning, but was.

![Wrong_predicts_citation](https://github.com/Pdugovich/Pdugovich.github.io/blob/master/img/Model%20Incorrectly%20Predicted%20NOT%20Warning%20iloc.png?raw=true)

&nbsp;

Shap force plot

![Shap_wrong_predicts_citation](https://github.com/Pdugovich/Pdugovich.github.io/blob/master/img/Model%20Incorrectly%20Predicted%20NOT%20Warning%20shap.png?raw=true)

&nbsp;

Expanded feature chart

![Features_wrong_predicts_citation](https://github.com/Pdugovich/Pdugovich.github.io/blob/master/img/Model%20Incorrectly%20Predicted%20NOT%20Warning%20features.png?raw=true)

&nbsp;
&nbsp;
&nbsp;

## Could the Model be Improved?

Of course! The most direct way to improve the model is by having more data. Feature engineering is great, but can only get you so far. 
There are many features I wish I had, 'number_of_hours_since_officer_last_ate' and 'big_5_personality_score_of_officer' are just a _little_ difficult to get a hold of, but would probably be immensely useful in making outcome predictions.
Data that likely exists and would be helpful, but I don't have access to are things like the subject's 'date_of_birth', 'model_of_vehicle', 'year_of_vehicle', 'color_of_vehicle' also the exact identification of the officer would be great, but aside from that, 'officer_race', 'officer_sex', 'officer_years_on_force'. Along with those, I'd like more information on road and weather conditions, whether they were driving through construction or school zones. 

Ultimately, this dataset was interesting,  but I'd need a lot data to be confident in predictions I draw from it.

&nbsp;
&nbsp;
&nbsp;

## Code Base

The code can be found at: https://github.com/Pdugovich/Columbus_Traffic_Stops_Project_Repo
