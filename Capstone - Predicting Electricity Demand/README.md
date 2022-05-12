# Capstone: Predicting Singapore's Electricity Peak Demand


### Problem Statement

As an employee of ETDD, I am tasked to predict Singapore's electricity demand.

This study focuses on predicting the peak electricity demand, which is the highest instantaneous demand, of Singapore for one-year ahead.

Peak demand is important as we need to make sure that there is sufficient electricity infrastructure (e.g. generation plants, transmission and distribution network) in place to meet Singapore's demand at any point in time. This information will be useful to advise decision makers on the amount of infrastructure required.

---

### Datasets

* Half-hourly System Demand (MW) from 1 Jan 2003 - 18 Apr 2022 
(Source: EMC, Uniform Singapore Energy Price (USEP) and Demand Forecast, [*source*](https://www.emcsg.com/marketdata/priceinformation))

* GDP in Chained (2015) Dollars from 1Q 2003 - 4Q 2021 (Source: Singapore Department of Statistics, [*source*](https://www.singstat.gov.sg/find-data/search-by-theme/economy/national-accounts/latest-data))

					

---

### EDA

* Demand
    - While there is a general increasing trend of half-hourly system demand, Augmented Dickey-Fuller test rejected the H0 (p-value<0.01), indicating that our time series is stationary. This is due to the high resolution of data (i.e. half-hourly) where there seems to be no dependency of Yt on Yt-1, so we aggregate the the demand to use daily highest demand.
    - Based on daily highest demand, the Augmented Dickey-Fuller Test cannot reject the H0: non-stationary, so we will be using the daily highest demand for modelling.
    - We then perform differencing, and at the first order differenced demand, stationary assumption is fulfilled.

* Exogenous predictors
    - GDP
        * Demand and GDP also trend very closely because ~85% of the total demand is attributed by the commercial and industrial sector.
        * Sharp dips in demand in 2008 and 2020 which were due to global economic crisis and COVID-19 respectively.
        * To factor in GDP as exogenous predictors, we need to lag the quarterly GDP by a year.
    - Week
        * Demand is usually higher in the middle of the year between Mar and Oct and lower at the start and end of the year due to holiday seasons
        * Dummified weeks as it does not seem to have a linear linear relationship with demand.
    - Day of Week
        * Demand is higher on Mon to Fri than Sat and Sun.
        * Group them into weekdays and weekends.
        * Dummified both day of week and weekdays/weekends and determine which is a better predictor through modelling.        
    - % WFH due to COVID-19 (as a proxy of economy recovery)
        * Demand was the lowest during Circuit Breaker (CB) period when there was severe disruption to economic activities and almost everyone has to WFH, except for essential services. Demand increases with easing of measures and more people returning to their workplaces.
        * Similar to week and day of week, as the relationship between demand and % WFH does not seem linear, so we dummified the % WFH.


---

### Model Exploration and Evaluation

* Train-test preparation
    - Use 2003-2018 as train data and the last 3 years, 2019-2021, as test data

* Evaluation Metrics
    - Since the purpose of the study is to accurately predict peak demand for one-year ahead, we forecast the demand for 1 year ahead based on all historical data (e.g. predict 2019 demand using 2003-2018 demand)
    - We then measure the error between actual and predicted peak for all 3 years and take a simple average of these errors as our evaluation metric
    
* Model Exploration
    - Base model: ARIMA
        * Differenced 1 (d=1), hypertuning for p and q.
        * Model with least error is ARIMA(3,1,3) with mean error of 444 MW (to set this into context, a combined cycle power plant (CCGT) has capacity of about 600MW, so error of about 600MW will result in one more or one less power plant being built).
    - SARIMA
        * Based on ACF and PACF plot of the first order differenced demand, there is a clear seasonal pattern which repeats every 7 days so we set S=7 and D=1 for the seasonal component. Additionally, autocorrelation at lag 7 is positive, so it is likely to have SAR terms. For the non-seasonal component, there seems to be a sharp cutoff at lag 1 or 2 in the ACF plot and having negative autocorrelation at lag 1 implies that it is likely to have MA terms. As it is possible for AR term and MA term to cancel each other's effects, we hypertuned for p and q in both the seasonal and non-seasonal components.
        * Model with least error is SARIMA(2,1,1)x(2,1,1,7) with mean error of 190 MW.
    - SARIMAX
        * Explore individual and a combination of the 5 features (i.e. quarterly GDP of 1-year lag, week, day of week/is_weekday, % WFH due to COVID-19)
        * Model with least error is SARIMAX(2,1,1)x(2,1,1,7) using 4 features (i.e. quarterly GDP of 1-year lag, week, day of week, % WFH due to COVID-19) with mean error of 121 MW.

Performance of the family of ARIMA models
|Model|Tuning parameters|Selected Model|Mean Error|
|-----|----------|----------|-----------|
|ARIMA| p = (0,1,..., 5); d = 1; q = (0,1,..., 5)|ARIMA (3,1,3)|444 MW|
|SARIMA| p = (0,1,2); d = 1; q = (0,1,2); P = (0,1,2); D = 1; Q = (0,1,2); S = 7|SARIMA (2, 1, 1)x(2,1,1,7)|190 MW|
|SARIMAX| 1-year lag GDP, Week, Day of Week, Is_weekday, % WFH|SARIMA (2, 1, 1)x(2,1,1,7) with 1-year lag GDP, Week, Day of Week, % WFH|121 MW|

* 
    - LSTM (long short term memory) RNN with recurive strategy ("many-to-one" model)
        * Explore using LSTM (long short term memory) RNN with recurive strategy, also known as the many-to-one model.
        * Tune for a few architecture e.g. the number of neurons, layers, epoch and batch size
        * Mean error about 173MW (lower than SARIMA but higher than SARIMAX). However, this model has yet to be tuned excessively, which could be why it does not outperform SARIMAX.

---

### Final Model

* Chose SARIMAX(2,1,1)x(2,1,1,7) using 4 features (i.e. quarterly GDP of 1-year lag, week, day of week, % WFH due to COVID-19) considering its explanatory power.

* Further tune for p and q of both seasonal and non-seasonal component while fixing the 4 exogenous predictors.

* Final model yield a mean error of 24 MW.

* Predicted peak demand in 2019, 2020 and 2021 are close to actual peak demand.

---

### Prediction

* Based on the final model and using train data from 2003-2021, predicted peak demand for 2022 is 7,751 MW.

* Predicted demand is quite close to actual demand from 1 Jan to 18 Apr 2022 which was not used for prediction. There is a spike in predicted demand in Apr due to further easing of COVID measures from 26 Apr.

* While we do not expect the actual demand to spike on 26 Apr, we expect the peak demand to reach about 7,750 MW in the later part of the year (similar to the predictions vs actual in the train-test data).

---

### Next Steps

* Feature Engineering
    - Incorporate public holidays considering that demand is affected by whether it is a working day or not
    - Since day and week are important features, transform them to sin and cos functions to better model their cyclical patterns
* Model
    - Increasing testing period to 5 years (instead of 3) to see if the model performs consistently well. We start off with 3 years for now because we do not have many years of historical data
    - Exploration: 
        * Further tune the LSTM RNN model
        * Multiple output strategy (one ‘many-to-many’ architecture) for RNN e.g. using 365 days to predict 365 days ahead simultaneously (instead of sliding windows)^
        * GRU, CNN, etc.
* Application
    - Predict total annual demand which will be useful for computing electricity tariff
    - Predict peak demand/total annual demand for a longer forecast period e.g. 10 years



