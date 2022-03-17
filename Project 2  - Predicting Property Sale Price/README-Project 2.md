# ![] Project 2: Standardized Test Analysis


### Problem Statement

As a property consultancy firm, we help property owners maximize the value and selling price of their properties. Through this project, we hope to:
* Help property owners identify the features which are most important to predict sales price (which can be done by ranking the coefficients of each of the features in a regression model); and
* Provide our customers with a tool which can (i) provide quick estimates of their property potential selling prices, and (ii) help them identify which aspects of their properties they can improve on to enhance their selling prices.

Target Audience: Customers (i.e. property owners)


---

### Datasets

* DESCRIPTIVE ABSTRACT: Data set contains information from the Ames Assessor’s Office used in computing assessed values for individual residential properties sold in Ames, IA from 2006 to 2010.

* SOURCES: Ames, Iowa Assessor’s Office 

* VARIABLE DESCRIPTIONS: Tab characters are used to separate variables in the data file. The data has 82 columns which include 23 nominal, 23 ordinal, 14 discrete, and 20 continuous variables (and 2 additional observation identifiers)

* More details can be found [*source*](http://jse.amstat.org/v19n3/decock/DataDocumentation.txt)

					

---

### Cleaning and EDA

* Missing values are treated.

* Regrouping is performed on categorical features based on similarity in their sale price distributions (or psf distribution for neighbourhood) across groups. This is performed on the following variables:
    - proximity to positive off-site feature (condition_2): PosA and PosN in one group and the rest in another group
    - type of foundation (foundation): poured_contrete in one group and the rest in another group
    - masonry veneer type (mas_vnr_type): stone in one group, brickface in second group and the rest in the third group
    - neighbourhood:
        * NridgHt, GrnHill and StoneBr as premium estate
        * Edwards, OldTown, BrDale, IDOTRR, MeadowV, SWISU and BrkSide as low-range estate
        * The rest as mid-range estate

* Outliers are identified and removed, there appropriate:
    - gross living area - there are 2 outliers where gross living area are more than 4,000 square feet. Since they are rare, we remove them as they can skew results.
    - garage year built - there is 1 outlier where garage year built is 2207 but house was sold in 2007. This is likely to be entry error and we set value of garage year built to 2007, same as the year sold/remodelled

* Sale price is right-skewed with outliers above $350,000.
    - Based on histogram and boxplot, we observed quite a number of outliers which do not deviate too much from the boxplot maximum. This is due to the sale price being right-skewed.
    - As there are many of such cases and they do not deviate too much from the boxplot maximum, we keep them in the dataset to see if we can identify features/characteristics which attribute to these high sale prices.

* Numeric Features
    - Some features which are highly correlated with sales price (i.e. correlation >0.5 or <-0.5) have severe multicollinearity with each other (i.e. correlation >0.8) and similar distributions, so they should not be concurrently added to model. We drop one of these features in Model 1:
        * garage area and garage cars (drop garage cars): As garage size in square feet increases, the garage size in terms of car capacity increases
        * 1st flr sf and total bsmt sf (drop total bsmt sf): As first floor square feet increases, total square feet of basement area increases
        * gr liv area and totrms abvgrd (drop totrms abvgrd): As the above ground living area in square feet increases, the total rooms above ground increases
    - We can further simplify the model by dropping more features which have high collinearity (+-0.6 < correlation <+-0.8) for Model 2:
        * overall qual and exter qual (drop exter qual): Rating of overall material and finish of the house is likely to be similar to the quality of the material on the exterior 
        * overall qual and bsmt qual (drop bsmt qual): As the rating for the overall material and finish of the house increases, it is likely that the basement quality increases
        * gr living area and full bath (drop full bath): As the above ground living area in square feet increases, the full bathrooms above ground increases
        * age sold and year remod add (drop year remod add): Negatively correlated as remodel date would be the construction date if no remodeling or additions is done, and the age of house is derived from year built    
    - Some features have non-linear relationship with sale price (e.g. overall quality). As such, we will explore a third model which includes polynomial features to help address these non-linearity (Model 3).
   
* Categorical Features
    - From the distributions in bar charts and boxplots, we drop utilities as close to 100% of the data points are of the same value (AllPub), and this is not informative.
    - From the boxplots, we can also drop features which have similar distribution of sale price across groups and logically, should not affect sale price (the resultant features are used in Model 1). These features are:
        * alley
        * lot shape (general shape of property)
        * lot config (Lot configuration)
        * land slope (Slope of property)
        * misc feature (Miscellaneous feature not covered in other categories)
    - Features with varying distribution of sale price across groups and have dummy features with high correlation with sale price (correlation >0.4 or <-0.4) should be included in the model (these features are used in Model 2 and 3).


   

---

### Model Exploration

**Model 1 Feature Selection: Predict sale price using:
(i) 10 numeric features with strong correlation with sale price (i.e. correlation > 0.5 or <-0.5) which do not have severe collinearity with each other (-0.8 < correlation < 0.8)
    - Overall qual, gross living area, garage area, 1st floor sqft, basement qual, year remodeled, fireplace qual, number of full bath, masonry veneer area, and age sold
(ii) All categorical variables (transforming to 156 dummies) except those which have similar sale price distribution across groups and, logically, should not affect on sale price.

* Observation: Linear Regression performs badly when there are many features used in the model (the CV and MSE are ridiculously high). On the other hand, Ridge, Lasso and Elastic Net perform much better than linear regression, when there are many features used in the model. Regularisation shrinks coefficients closer to 0 (or even to 0 in Lasso), simplifying the model. About 50% of the coefficients are shrank to 0 in Lasso.

    * Linear Regression
        - Average Root Mean Squared Error (RMSE) based on Cross Validation (CV) on training data: 2.68e+17 (average MSE: 7.19e+34)
        - Root MSE on holdout data: 34,146,119,829,919 (MSE: 1.17e+27)

    * Ridge Regression
        - Average RMSE based on CV on training data: 32,887 (average MSE: 1,081,611,616)
        - RMSE on holdout data: 26,494 (MSE: 701,958,577)

    * Lasso Regression
        - Average RMSE based on CV on training data: 31,790 (average MSE: 1,010,659,113)
        - RMSE on holdout data: 26,361 (MSE: 694,904,047)

    * Elastic Net Regression
        - Average RMSE based on CV on training data: 32,711 (average MSE: 1,070,018,975)
        - RMSE on holdout data: 27,289 (MSE: 744,716,493)

**Model 2 Feature Selection: Predict sale price using:
(i) 7 numeric features with strong correlation with sale price (i.e. correlation > 0.5 or <-0.5) which do not have high collinearity with each other (-0.6 < correlation < 0.6) (i.e. overall qual, gross living area, garage area, 1st floor sqft, fireplace qual, masonry veneer area, and age)
(ii) 5 categorical features (transforming to 12 dummies) with high correlation with sale price (correlation >0.4 or <-0.4) (i.e. neighborhood, foundation poured concrete, masonry veneer type, garage finish, kitchen qual)

* Observation: Linear Regression performs much better than that in Model 1 as there are fewer yet important features used in Model 2. Ridge, Lasso and Elastic Net perform comparable to linear regression, as there is lesser regularisation/penalisation on fewer and important features.

    * Linear Regression
        - Average RMSE based on CV on training data: 33,454 (average MSE: 1,119,190,553)
        - RMSE on holdout data: 28,311 (MSE: 801,519,924)

    * Ridge Regression
        - Average RMSE based on CV on training data: 33,443 (average MSE: 1,118,463,806)
        - RMSE on holdout data: 28,308 (MSE: 801,393,428)

    * Lasso Regression
        - Average RMSE based on CV on training data: 33,454 (average MSE: 1,119,186,930)
        - RMSE on holdout data: 28,309 (MSE: 801,441,855)

    * Elastic Net Regression
        - Average RMSE based on CV on training data: 33,442 (average MSE: 1,118,418,964)
        - RMSE on holdout data: 28,308 (MSE: 801,383,154)

**Model 3 Feature Selection: Predict sale price using:
(i) 7 numeric features (transforming to 35 polynomial features) with strong correlation with sale price (i.e. correlation > 0.5 or <-0.5) which do not have high collinearity with each other (-0.6 < correlation < 0.6) (i.e. overall qual, gross living area, garage area, 1st floor sqft, fireplace qual, masonry veneer area, and age sold)
(ii) 5 categorical features (transforming to 12 dummies) with high correlation with sale price (correlation >0.4 or <-0.4) (i.e. neighborhood, foundation poured concrete, masonry veneer type, garage finish, kitchen qual)

* Observation: Linear Regression performs better than that in Model 2 (based on CV and MSE on holdout), which is likely because non-linear relationship between some of the features (e.g. overall quality) and sale price are being accounted. For Ridge, Lasso and Elastic Net, this model performs better than that in Model and 2 (with lower CV and MSE). The evaluation scores are also slightly lower than Linear Regression in the Model 3, suggesting that some regularisation/penalisation is performed (but not as much as that in Model 1, as evident by smaller alpha (Model 1: 585; Model 3: 98)).

    * Linear Regression
        - Average RMSE based on CV on training data: 30,039 (average MSE: 902,391,596)
        - RMSE on holdout data: 25,696 (MSE: 660,317,698)

    * Ridge Regression
        - Average RMSE based on CV on training data: 29,655 (average MSE: 879,436,940)
        - RMSE on holdout data: 25,256 (MSE: 637,900,975)

    * Lasso Regression
        - Average RMSE based on CV on training data: 29,677 (average MSE: 880,756,019)
        - RMSE on holdout data: 25,328 (MSE: 641,535,763)

    * Elastic Net Regression
        - Average RMSE based on CV on training data: 29,651 (average MSE: 879,216,054)
        - RMSE on holdout data: 25,258 (MSE: 638,001,652)

   

---

### Final Model

**Predict sale price using Linear Regression on 7 numeric features and 5 categorical features (Model 2) as it performs relatively well and is easily interpretable for property owners.

* While features selected in Model 3 yield better performance than Model 1 and 2 (across Linear, Ridge, Lasso and Elastic Net), the coefficients in the model are hard to interpret.

* Linear Regression on 7 numeric features and 5 categorical features is easily interpretable for property owners. The model also performs reasonably well in providing home owners an estimate of their potential selling prices.
    - Prediction error is about 28,000 dollars (about 15% deviation from mean sale price of about 180,000 dollars).
    - This is not much higher than the prediction error of about 25,000 dollars (about 14% deviation from mean sale price) based on Lasso Regression on polynomial features (Model 3).
    
* Only 12 features required
    - Minimal features for home owners to quickly input into the tool to easily obtain reasonably accurate estimates of their potential selling prices.
   

---

### Recommendation

**Property owner can improve kitchen quality, fireplace quality, using the right materials for masonry veneer and foundation to enhance selling price.

* Based on the size of the coefficients, we can rank the importance of features and estimate the impact of sale price

* Features which affect sale price most (in decreasing importance):
    - Gross living area: Above ground living area square feet
    - Overall quality: Rating of overall material and finish of the house
    - Kitchen quality being excellent
    - Neighbourhood in premium location (i.e. NridgHt, GrnHill and StoneBr)
    
* Features which home owners can improve to enhance selling price:
    - Fireplace quality - improve rating
    - Kitchen quality - improve to excellent
    - Masonry veneer type - use brick common/cinder block, followed by stone (instead of brick face)
    - Foundation - use poured contrete
    
* The impact of features on sale price can also be quantified (e.g. for every unit increase in kitchen quality rating, average selling price increases by 5,000 dollar)


---

### Error Analysis

* Residuals plot for both train and holdout data seems to centre randomly around 0. However, there are 2 data points in train set which are not predicted well by the final model (i.e. deviation of more than 300,000 dollars).

* Further deep-dive into these shows that the 2 properties are under-valuated, i.e. predicted price about 500,0000 dollars but they were sold at less than 200,000 dollars.


---

### Trade-off

**Model found to perform the best may not be selected as final model to address the problem statement

* Lasso, Ridge and Elastic Net on polynomial transformation of the 7 numeric features and 5 categorical performs the best (Model 3).

* Linear Regression without polynomial transformation of numeric features is selected as final model due to interpretability with some trade off in terms of increased prediction error.
    - Size of the coefficient is more interpretable.
    - Can use to identify features which are most important to predict sales price and features which can be improved to enhance property selling prices.

---

### Learning Points

**Regularisation, through Ridge, Lasso and Elastic Net, can be used to shrink coefficients closer to 0, dropping out insignificant features.

* Pros of Regularisation:
    - Performs better than Linear regression especially when there are many features
    - Prevent overfitting of model, and a more simplified model, which can better generalise and predict unseen data
    - As number of features increases, the hyperparameter (⍺) increases, having more penalisation on the coefficients

* Cons of Regularisation:
    - Hard to interpret the coefficients of the model


---

### Areas for Future Work

* Explore more models with varying features to determine the linear regression model which can best predict property sale price




