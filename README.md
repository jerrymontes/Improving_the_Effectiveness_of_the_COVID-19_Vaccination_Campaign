## The Shot in the Arm: Improving the Effectiveness of the COVID-19 Vaccination Campaign

---

### Problem Statement

To date, there have been over 81 million confirmed cases of COVID-19 in this country.  It has killed more people in the U.S. than in any other country in the world, at almost 1 million.   Since the start of the pandemic, the Food and Drug Administration has authorized the emergency use of 3 vaccines for the prevention of COVID-19: the Pfizer-BioNTech vaccine, the Moderna vaccine, and Johnson & Johnson’s Jannsen vaccine.  As of April 19, 2021, every adult in this country has been eligible to receive a COVID-19 vaccine.  As of November 19, 2021, all American adults have been eligible for a booster shot starting 6 months after their initial vaccination.  On March 29th of this year, the FDA authorized a second booster dose for immunocompromised individuals and those older than 50 starting 4 months after their first booster dose.  Despite the widespread availability of these vaccines, only 2 out of every 3 Americans have been fully vaccinated.  Although hundreds of millions of dollars have been spent on COVID-19 vaccine education campaigns since December 2020, there is still a significant portion of the population that is hesitant, skeptical, and misinformed about the COVID-19 vaccines.  

The U.S. Department of Health and Human Services is interested in improving the online component of its COVID-19 Public Education Campaign by identifying the terms and phrases most commonly used online to both promote and discourage vaccination.  This project seeks to identify the best model for classifying posts as belonging to either the CovidVaccinated subreddit, which is a forum for those who have received or are considering receiving the COVID-19 vaccine, or the VaccineDebate subreddit, which is a vaccine debate forum whose members were largely critical of vaccination.  The model with the highest accuracy and precision scores will be identified as the best for its ability to identify posts belonging to the pro-vaccine subreddit.  The most popular words used to promote vaccination on this subreddit will be incorporated into the online component of the COVID-19 Public Education Campaign.  The most popular words and phrases used to critique the vaccines on the other subreddit will inform the Department about the myths it should work to debunk in the next iteration of its campaign.  In order to dissect the user’s own summary of their post and analyze which words the users were making use of to attract and persuade others to adopt their view, the title of each post will be analyzed.  

---

### Data Collection

I began by importing all of the modules I would need in order to collect all of the data from both of my selected subreddits: 'CovidVaccinated' and 'DebateVaccines'.  First I requested the total number of posts in Subreddit A (CovidVaccinated) from Reddit's Application Programming Interface.  I then requested the total number of self-text posts in Subreddit A (CovidVaccinated) from Reddit's Application Programming Interface, and found this number was over 24,000.  I proceeded to request the total number of posts in Subreddit B (DebateVaccines) from Reddit's Application Programming Interface along with the total number of self-text posts and discovered that number was over 7,000.  Given the imbalanced self-text post counts, I immediately knew that I would be dealing with imbalanced classes if I did not select the same number of posts from each.  Given that Subreddit B only had 7,000 while Subreddit A had more than three times as many posts, it became clear that I would only be able to pull the 7,000 posts most recent posts from each.  

I then created a variable named 'url' and initialized it to include the address of Reddit's API endpoint for searching submissions.  The function I defined next, called 'get_posts', had two parameters: 'subreddit' and 'utc'.  Its purpose was to scrape the specified post count (7,000) from the specified subreddit starting from the specified utc backwards before creating a DataFrame with the results.  The first thing I did was to call the get_posts function with Subreddit A and a UTC from Thursday, April 28, 2022.  After verifying that the result of calling the function was of type DataFrame, I looked at the shape of the DataFrame created by attempting to scrape 7000 posts from Subreddit A.  There were 73 columns, one of which was 'title'.  I then calculated the sum of the duplicate titles in that DataFrame, and found there to be 255 duplicates.  Since this amounted to such a minute portion of the data, I was able to drop them without having to pull more posts from Subreddit A.  After confirming that these duplicates had been dropped by looking at the shape of the DataFrame, I then verified that the only value of the 'subreddit' column in this DataFrame was "CovidVaccinated" before saving this duplicate-free DataFrame to a .csv file.

Following that I called the 'get_posts' function with Subreddit B and the same UTC from Thursday, April 28, 2022 that I utilized for pulling posts from Subreddit A.  Once again I looked at the shape of the DataFrame created by attempting to scrape 7,000 posts from Subreddit B.  There were 88 columns, one of which was 'title'.  I then obtained the sum of duplicate titles in the DataFrame, which was 136.  After dropping this small amount of duplicates, I checked which columns were only present in one of the DataFrames I had just created and not the other so that I could then drop these columns such that later on both DataFrames could be concatenated.  It was then that I dropped all of the columns that were not present in both DataFrames, after which I confirmed that the only value of the 'subreddit' column in this DataFrame was "DebateVaccines" before saving this duplicate-free DataFrame to a .csv file.

---

### Data Cleaning & Exploratory Data Analysis

I began the process of Data Cleaning by importing all of the modules I would need to utilize, including Plotly, which I commented out unless I was accessing it using the environment that had Plotly installed.   First I created a DataFrame from the .csv file I saved with Subreddit A's pulled posts.  Then I created a DataFrame from the .csv file I saved with Subreddit B's pulled posts.  It was no easy to concatenate both of the DataFrames and save the resulting DataFrame as "reddit".  After looking at the shape of the concatenated DataFrame, I reset the index of this concatenated DataFrame, saved the results inplace, and confirmed that the shape of the DataFrame had not changed.  I proceeded to create a new DataFrame named "subreddits" which contained only two of the 73 columns in the concatenated "reddit" DataFrame.  These two columns would serve as my feature and my label, respectively.  After verifying that the "subreddits" data type was a DataFrame, I counted the number of missing values in either of the DataFrame's two columns and realized there were none.  Finally, I was able to save this new DataFrame (with only the feature and target columns) as a .csv file.

In order to perform Exploratory Data Analysis without altering the "subreddits" DataFrame, I created a copy of it and named it "df_subreddits".  I verified that "df_subreddits" was of type DataFrame and looked at the value counts of the "subreddit" column.  I then looked at the shape of the "df_subreddits" DataFrame and made use of the .info() method in order to obtain information about the DataFrame and its two columns.  After looking at the top 10 and last 10 rows of the newly created "df_subreddits" DataFrame, I generated a new column named 'title_char_length' which contained the character length of each title.  Only after I confirmed the presence of the newly created column in the DataFrame did I implement a function named "check_space" that counted the number of spaces in each title and added 1 to obtain the total number of words.  This enabled me to create a new column named 'title_word_count' which contained each title's word count.  Once again I looked at the top 10 rows of the df_subreddits DataFrame to confirm the presence of the newly created column.  

At this point I was able to obtain the longest and shortest titles by word count along with the longest and shortest titles by character length.  At this point I plotted the distribution of title_char_length and title_word_length to get a feel for the text that would be passed through the vectorizers.  I was finally ready to instantiate a CountVectorizer object that split the documents in the corpus into unigrams.  I then fit and transformed subreddit A's titles before obtaining the 10 most common unigrams.  Fitting and transforming subreddit B's titles allowed me to obtain its 10 most common unigrams as well.  I repeated this process for both subreddits until I had obtained the 10 most common bigrams, trigrams, 4 n-grams, and 5 n-grams for both.

The most common n-grams in the COVIDVaccinated subreddit revealed a heavy emphasis on the vaccine manufacturer, which might indicate that the HHS should draw attention to the fact that all 3 of the FDA-cleared vaccines are manufactured by American pharmaceutical firms.  Many Americans already take and depend on medications manufactured by Pfizer and Johnson and Johnson, and this is an angle the HHS Department has not yet explored in its campaigns.

The most common n-grams in the DebateVaccines subreddit reveal a necessity to debunk the myth that vaccines cause autism, along with the myth that athletes around the world are suffering cardiac arrest caused by the COVID-19 vaccines.  Such prevalent use of the words “anti” and “vaxxers” seems to confirm that many Americans now refer to themselves in this way.


|Feature|Type|Dataset|Description|
|---|---|---|---|
|**title**|*string*|subreddits|The title of one of the 6900 most recent posts| 
|**subreddit**|*category*|subreddits|The subreddit that the post title is from (either 'CovidVaccinated' or 'DebateVaccines')| 
|**title_char_length**|*int*|subreddits|The number of characters in the title| 
|**title_word_count**|*int*|subreddits|The number of words in the title| 


---

### Preprocessing and Modeling

In order to begin preprocessing, I once again imported all of the necessary modules before setting the notebook's random seed to 22 in order to ensure reproducibility. Immediately I created a DataFrame from the subreddits.csv file, which contained the cleaned data.  I then looked at the DataFrame and confirmed its size and columns were correct before creating a function named "stemming_tokenizer" which would later be thrown into the range of tokenizer hyperparameters I performed a RandomizedSearchCV on.  I then incorporated a function named "lemma_tokenizer" which would later be thrown into the range of tokenizer hyperparameters I performed a RandomizedSearchCV on as well.

After defining 'title' as my X and 'subreddit' as the target vector, I proceeded to perform a train/test split with a random_state of 42 while stratifying on the label.  I then looked at the noramlized value_counts in the target vector.  In order to calcualte the baseline accuracy score I instantiated a DummyClassifier object and proceeded to fit it on the X_train and y_train before scoring both the X_train and y_train along with the X_test and y_test.  I then utilized the fitted DummyClassifier object to predict the target and saved the results under a variable named "baseline_preds".  Utilizing this DummyClassifier estimator I generated a ConfusionMatrixDisplay before calculating the precision, accuracy, and f1 scores with 'CovidVaccinated' set as the positive label.

I was now ready to begin modeling.  To start, I created a pipeline object with a CountVectorizer transformer and Logistic Regression classifier with a max_iter of 10,000.  I proceeded to get a list of all available hyperparameters using the .get_params() method and then created a dictionary of hyperparameter ranges that I would then perform a RandomizedSearchCV over.  Next I created a RandomizedSerachCV object that contained the pipeline and the dictionary of hyperparameter ranges I had defined and ensured that the jobs ran in parallel.  Only after fitting this RandomizedSearchCV object on X_train and y_train before scoring both X_train, y_train and X_test, y_test was I able to look at the hyperparameters identified as best by the RandomizedSearchCV.

Paying close attention to the hyperparameters identified as optimal, I then created a new parameter grid that continaed a narrow range around the results obtained from the RandomizedSearchCV.  Following this, I created a GridSerachCV object that contained the pipeline along with the revised dictionary of hyperparameter ranges that surrounded the hyperparameters identified as optimal by the RandomizedSearchCV and ensured that the jobs ran in parallel.  Once again, I fit this GridSearchCV object on X_train and y_train before scoring both X_train, y_train and X_test, y_test.  I was now ready to use the fitted pipeline object to predict the target and saved the results under variable named "cvec_logreg_preds" before generating a ConfusionMatrixDisplay utilizing that Logistic Regression estimator.

As I had done with the baseline (null) model, I calcualted the precision, recall, and accuracy scores after setting 'CovidVaccinated' as the positive label and then analyzed the hyperparameters identified as best by the GridSearchCV.

I repeated this process a total of 15 times until I had 16 models.  Eight of them had had the titles converted into a matrix representation by using the CountVectorizer, while the other eight matrices were the product of the TfidfVectorizer.  I then employed the use of the following seven models for both vectorizers: Logistic Regression, KNeighbors Classifier, SVC, Decision Tree Classifier, Extra Trees Classifier, Random Forest Classifier, and Gradient Boosting Classifier.  Only the CountVectorized titles were passed through the Multinomial Naive Bayes Classifier, while only the TFIDF Vectorized titles were passed through the Gaussian Naives Bayes Classifier.

---

### Evaluation

The baseline model's accuracy score was 50.36%, and all models surpassed that score.  The only model with both accuracy and precision scores above .86 was the Multinomial Naives Bayes Classifier model.  Its accuracy score of .87 meant that 87% of the predictions it made were accurate, making it the model with the highest accuracy score.  Its precision score of .88 meant that 88% of the posts classified by the model as belonging to the CovidVaccinated subreddit were actually from that subreddit.  The Multinomial Naives Bayes model does not have interpretable coefficients, as it calculates the probability of each token before returning the token with the highest probability.  The GridSearchCV found the best hyperparameters to be an alpha of 0.4, a lemma tokenizer, no Stop words, and an ngram range of (1,1).

---

### Conclusion and Recommendations

The purpose of the model was to identify flagged posts that came from the CovidVaccinated subreddit in an attempt to identify the most popular terms and phrases used to promote the vaccine and later incorporate them into the online component of the COVID-19 Public Education Campaign.  As such, I was interested not only in the model’s accuracy, but also in its precision with regards to predicting the positive class, which was set to the CovidVaccinated subreddit.  

Both subreddits shared many of the same words, and although the Multinomial Naive Bayes Classifer had fairly high accuracy and precision scores, it would be interesting to analyze its performance with a stop words list that included this set of shared words that ultimately reduced the Multinomial Naive Bayes’ model’s performance.  It was important for me to introduce as little bias as possible to the models and instead have the hyperparameters narrowed down by the RandomizedSearchCVs and then whittled down further by the GridSearchCVs, but this is an avenue worth pursuing in the future.  Additionally, sentiment analysis could be integrated to the completed analysis, and given the strong feelings on the the subject, would surely result in useful insights.

When it comes to media, we are what we consume.  That’s why it’s important for the U.S. Department of Health and Human Services to understand what misinformation is currently going viral online.  Of course, not every Reddit post containing the word “vaccine” is critical of vaccines. By correctly classifying a Subreddit post as either belonging to the pro-vaccine subreddit 'CovidVaccinated' or belonging to the vaccine debate forum 'DebateVaccines', we can determine whether or not it is likely to be a post that can shed light on the language used in unvaccinated circles that would inform what our campaign should focus on debunking next or a post that can shed light on what might be the most persuasive arguments for vaccination.  The latter set of words did not come from a public health official’s mouth, but instead are the words actually used by Americans to refer to the vaccine they have made an informed decision to receive.  The HHS should focus on debunking any claims that the vaccines are tied to autism or cardiac arrest and promote the fact that the vaccines available in our country are made by the same American pharmaceutical firms already trusted by millions for their necessary medications.  By including these components in the next release of its COVID-19 Education campaign, the HHS can hope to increase its effectiveness so that every American gets vaccinated and later boosted and no one dies from COVID-19 ever again.

---

#### Citations

* https://abcnews.go.com/Health/adults-now-eligible-covid-19-vaccines/story?id=77163212
* https://www.cdc.gov/media/releases/2021/s1119-booster-shots.html
* https://www.cnn.com/2020/12/04/politics/hhs-ad-campaign/index.html
* https://coronavirus.jhu.edu/region/united-states
* https://www.fda.gov/emergency-preparedness-and-response/coronavirus-disease-2019-covid-19/covid-19-vaccines
* https://www.bbc.com/news/world-51235105


#### Image Citations

-Us Dept of Health and Human Services: https://www.ispecimen.com/wp-content/uploads/2017/01/2000px-US-DeptOfHHS-Seal-3D.svg_.png 
-Vaccines: https://www.gannett-cdn.com/presto/2021/03/03/NDNJ/aaf752d6-c732-4278-9656-aa0762edae8b-DTB_Three_vaccines.jpg?crop=5097,2867,x0,y0&width=3200&height=1800&format=pjpg&auto=webp
-Image 4-30-22: https://www.reddit.com/r/DebateVaccines/new/
-Second image 4-30-22: https://www.reddit.com/r/CovidVaccinated/new/
-5-1-22 image: https://www.reddit.com/r/NoNewNormal/
-Vaccine card: https://dmn-dallas-news-prod.cdn.arcpublishing.com/resizer/Zt8tJpi1QTXtgLHvdXdZSepI1TM=/1660x0/smart/filters:no_upscale()/cloudfront-us-east-1.images.arcpublishing.com/dmn/OEPTGAUTR5BSLKZPO7YKIYS254.jpg
