# Project 3: NLP for Right-Classifying Reddit Posts


### Problem Statement


With increasing number of working moms, and dads having to play an increasing role in taking care of children, child rearing becomes increasing a shared responsibility.

As an employee of theAsianparent, we help parents experience healthy pregnancies and raise healthy families. Through this project, we hope to:
* Automate the classification and monitoring of posts and discussions by moms and dads
* Better understand concerns and topics of interest amongst them to create targeted and interesting articles to better support parenthood
    
As want to curate topics and articles for both moms and dads, we will use accuracy, sensitivity (proportion of /mommit posts being right classified) and specificity (proportion of /daddit posts being right classified) to measure the best performing model.

We will be exploring using Logistic Regression, Multinomial Naive Bayes and Random Forest with count vectorizer and TF-IDF vectorizer.

Target Audience: Moms and dads

---

### Datasets

* DESCRIPTIVE ABSTRACT: 
    - r/mommit is a subreddit community for "moms mucking through the ickier parts of child raising. It may not always be pretty, fun and awesome, but we do it. We want to be here for other moms who are going through the same experiences and offer a helping hand".
    - r/daddit is a subreddit community for "dads. Single Dads, new Dads, Step-Dads, tall Dads, short Dads, and any other kind of Dad. If you've got kids in your life that you love and provide for, come join us as we discuss everything from birth announcements to code browns in the shower".

* Web-scrap title and selftext of 10,000 posts each from r/mommit and r/daddit.

* SOURCES: 
    - r/mommit [*source*](https://www.reddit.com/r/mommit/)
    - r/daddit [*source*](https://www.reddit.com/r/daddit/)


					

---

### Cleaning and EDA

* Title and selftext are combined.

* Categorical target variable (r/mommit and r/daddit) are dummified.

* Hyperlinks and special characters are cleaned up.

* Stopwords based on frequently used english words, and obvious and associated words for both subreddits (e.g. dad, mom) are applied.

* Tokenization and lemmetizing are applied.

* Based on CountVectorizer of top words, we observed that there are many common words and presence of singular and plural form of the same words (e.g. feel like, feels like), as well as meaningless words (e.g. 'amp x200b').
    - About half of the top words are present in both r/mommit and r/daddit (e.g. feel like, make sure, month old, year old). TF-IDF may perform better as it scores the importance of words based on its relative occurence in the doc as compared to all other docs. Words which occur often in both subreddits will have lower importance while those which occur in one document but not in many documents are more important and contain more predictive power.
    - For singular and pural form of the same words, lemmatizing can be useful.
    - Meaningless words (e.g. 'amp x200b' which is an empty paragraph in the subreddit post) are present and should be removed.
        
* While TF-IDF reduces the importance of frequently-used words, many commonly used words still have with high 'scores' and remain as top words.
    - Some of these words have little contextual meaning and hinder our ability to understand the dads' and moms' top concerns and topics of interest.
        * Remove words with little contextual meaning through stopwords to better understand the distinctive topic of interests, and with intention to increase predictive power of model (for exploration).
        * Retain words which may provide insights of interests e.g. 'car seat' as dads and moms may have different concerns. Dads are more concern about the technical aspects (e.g. installation of car seats, recommendations for specific car models), while mom are more concern about the softer aspect (e.g. cost, getting baby to sit in the car seat).
        
* Moms’ concerns and topics of interest are on:
    - Baby or themself tested COVID positive
    - Taming crying baby (scream bloody murder)
    - Hand foot and mouth disease (HFMD)
    - Car seat and stroller travel system
    - Making baby fall asleep (on their own)
    - Potty training their child
    - Postpartum mental health
    - They also describe themselves as stay-home or working full-time moms/parents.
    
* Dads’ concerns and topics of interest are on:
    - HFMD
    - Playing video games with their children
    - Wife being pregnant or giving birth
    - 50/50 custody for divorcees
    - Tested COVID positive
    - Getting white noise machine to help baby sleep
    - Showing how they make their child fall asleep (and how they fall asleep with them)
    - Seeking advice and sharing success stories of potty training
    - Dads like to conduct polls
    - They also describe their wives and them as stay-home or working full-time parents.


---

### Model Exploration

We use Logistic Regression with CountVectorizer as our base model and explore other models (with hyperparameter tuning) to determine the best model in this section

Model 1: Logistic Regression with CountVectorizer and TF-IDF vectorizer

|Model|Vectorizer|Hyperparameters Tuned|Best Accuracy|Train Accuracy|Test Accuracy|Sensitivity|Specificity|
|-----|----------|----------|----------|-----------|--------------------|-----------|-----------|
|Logistic Regression|CVEC|max features, min and max no. of doc, n-gram range|77%|96%|78%|78%|78%|
|Logistic Regression|TVEC|max features, n-gram range|80%|88%|81%|82%|79%|

* Comparing with our base model of Logistic Regression with CountVectorizer, Logistic Regression with TF-IDF vectorizer shows a 3%-point increase in accuracy. Both sensitivity and specificity increase as well, by 4%-point and 1%-point respectively.

Model 2: MultinomialNB with CountVectorizer and TF-IDF vectorizer
|Model|Vectorizer|Hyperparameters Tuned|Best Accuracy|Train Accuracy|Test Accuracy|Sensitivity|Specificity|
|-----|----------|----------|----------|-----------|--------------------|-----------|-----------|
|Multinomial Naive Bayes|CVEC| max features, min and max no. of doc, n-gram range|78%|84%|78%|76%|80%|
|Multinomial Naive Bayes|TVEC| max features, n-gram range|77%|87%|78%|78%|78%|

* Between CountVectorizer and TF-IDF vectorizer, Multinomial Naive Bayes model performance is comparable. The accuracy is about the same (78%) with 2%-point trade off between sensitivity and specificity (i.e. when sensitivity is higher, specificity is lower, and vice versa).

Model 3: Random Forest with TF-IDF vectorizer

* Based on Logistic Regression and Multinomial Naive Bayes, we establish that TF-IDF vectorizer is comparable or, if not, outperform Count Vectorizer. This is likely due to presence of many common terms in both subreddits.

* Based on this understanding, we explore Random Forecast with TF-IDF only.

|Model|Vectorizer|Hyperparameters Tuned|Best Accuracy|Train Accuracy|Test Accuracy|Sensitivity|Specificity|
|-----|----------|----------|----------|-----------|--------------------|-----------|-----------|
|Logistic Regression|CVEC|[CVEC] max features, min and max no. of doc, n-gram range|77%|96%|78%|78%|78%|
|Logistic Regression|TVEC|[TVEC] max features, n-gram range|80%|88%|81%|82%|79%|
|Multinomial Naive Bayes|CVEC|[CVEC] max features, min and max no. of doc, n-gram range|78%|84%|78%|76%|80%|
|Multinomial Naive Bayes|TVEC|[TVEC] max features, n-gram range|77%|87%|78%|78%|78%|
|Random Forecast|TVEC|[TVEC] max features, n-gram range; [RF] no. of estimators, max depth, max features|79%|100%|79%|81%|78%|

* Logistic Regression with TF-IDF vectorizer is the best model as it has the highest  accuracy, sensitivity and specificity
    - Accuracy increased by 3%-pt compared to base model
    - Although specificity is marginally lower than Multinomial Naive Bayes with count vectorizer, both accuracy and sensitivity are higher

Model 1': Logistic Regression with TF-IDF vectorizer on basic english stopwords (including obvious and associated words) and extended list of stopwords

* Explore further tuning the best model on extended list of stopwords to remove words which are common but have little contextual meaning

|Model|Vectorizer|Hyperparameter Tuning|Best Accuracy|Train Accuracy|Test Accuracy|Sensitivity|Specificity|
|-----|----------|----------|----------|--------------------|-----------|-----------|-----------|
|Logistic Regression|TVEC|[TVEC] max features, n-gram range, basic english stopwords|80%|88%|81%|82%|79%|
|Logistic Regression|TVEC|[TVEC] max features, n-gram range, extended list of stopwords|79%|87%|80%|82%|78%|

* Extended list of stopwords does not help to increase accuracy, sensitivity and specificity of model. This is likely due to the removal of words with low feature importance (i.e. they have high occurrence across both subreddits).

* Based on accuracy, sensitivity and specificity, we choose Logistic Regression with TF-IDF vectorizer on basic english stopwords with max features of 9,000 and n-gram range of (1, 2) as the final model.


---

### Interpretation of Final Model

Based on coefficient of Logistic Regression, we can interpret the importance of features.

* Important features for classifying posts to r/mommit are husband, lo (little one), sahm (stay at home mother), pregnant, FTM (first time mother), postpartum and breastfeeding. These are more distinctive words used by moms, as compared to dads.
    - Logistic regression allows us to interpret the importance of each feature through the coefficient size (e.g. holding other variable constant, presence of one occurrence of ‘husband’ would increase the likelihood of classifying to r/mommit by ~60,000 times).

* Important features for classifying posts to r/daddit are wife, fellow, guy, hey, girlfriend, poll and game. These are more distinctive words used by dads, as compared to moms.
    - Logistic regression allows us to interpret the importance of each feature through the coefficient size (e.g. holding other variable constant, presence of one occurrence of ‘wife’ would increase the likelihood of classifying to r/mommit by close to 0 times, indicating high likelihood of classifying to r/daddit).

---

### Error Analysis

* Based on top words of wrongly classified posts, many contain important features (i.e. more distinctive discussion topics) from the other subreddit. For example:
    - A number of misclassified r/daddit posts are related to mental health (their wife or child or them having mental health issue) and their partners (wives or girlfriends) being pregnant. Postpartum mental health and pregnancy are common topics for moms.
    - Similarly, a number of misclassified r/mommit posts are related to video games (them or their child or husband playing video games), which is a common topic for dads.
    - There are also topics related to tested COVID positive, potty training, car seat and staying home which are commonly discussed by both dads and moms.

* Despite containing term ‘husband’ and ‘wife’, about 5% of posts from each subreddit are wrongly classified because of long length with topics of other subreddit.
    - Based on strength of feature importance, it is unlikely for posts containing ‘wife’ or 'husband' to be wrongly classified. However, about 5% of posts in each sub-reddits are misclassified.
    - Further examination below shows that these posts are usually long and contain discussion topics of the another subreddit (e.g. top words for wrongly classified r/daddit despite mentioning of 'wife' include words relating to breastfeeding and pregnancy, while that wrongly classified r/mommit include words relating to games).

* Feature distribution by importance is quite centralised around 0, indicating many features lack predictive power to right-classify the posts. There are also roughly same number of of important predictors used to classify posts to each subreddit.


---

### Conclusion and Recommendations

* Based on error analysis, posts are wrongly classified because they are typically long and contain words relating to discussion topics from the other sub-reddit (e.g. r/daddit posts talking about breastfeeding)
* Despite this, the model performs quite well with ~80% accuracy, sensitivity (proportion of /mommit posts being right classified) and specificity (proportion of /daddit posts being right classified)
    - Allow us to reasonably right classify 80% of posts for monitoring of discussions by moms and dads
* Possible targeted topics to focus in articles for moms and dads:
| |Moms|Dads|Both Parents|
|-----|----------|----------|----------|
|Topics|Pregnancy, Postpartum mental health, Breastfeeding, Getting husband’s help, Taming crying baby, Putting baby to sleep and sleep training, Car seat and stroller travel system|Video games with children, Helping wives who are pregnant or giving birth, White noise machine to help baby sleep, Custody for divorcees|Managing disease such as COVID and HFMD, Potty training, Stay home with children|
|Ways to engage|Providing advice|Including poll or other fun element| |


---

### Next Steps

* Further automate the curation of in-depth discussion areas for each topic of interests or concern for moms and dads separately (e.g. analysing posts classified as r/mommit and containing discussion on pregnancy to create content/articles relating to pregnancy).
* Explore deep-learning models to improve accurate, sensitivity and specificity as the discussions in the posts are more english- and contextual-based.




