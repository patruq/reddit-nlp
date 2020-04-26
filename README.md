# Natual Language Processing

### How accurately can the words in a subreddit's post predict whether the post was intended for a 'good advice' or 'bad advice' subreddit?

-------------------------------------------------

***Executive Summary***

### Notes on the Data and Subreddits:

**LifeProTips: (LPT)**
- "Tips that improve your life in one way or another"
- A subreddit dedicated to sharing 'helpful' user-provided advice for navigating a plethora of sitautions.

**UnethicalLifeProTips: (ULPT)**
- "An Unethical Life Pro Tip (or ULPT) is a tip that improves your life in a meaningful way, perhaps at the    
expense of others and/or with questionable legality. Due to their nature, do not actually follow any of these
tips–they're just for fun. Share your best tips you've picked up throughout your life, and learn from others!"
- A subreddit dedicated to sharing mocking, 'joke' user-provided 'advice on a number of subjects and situations

### Predictors and Target Variable:

- The predictor variable is `title`.
- The target variable is `subreddit`.

***Contents:***

- <b>Notes on Data & Import</b>

  - Imported data directly from Reddit using the Pushshift API. For the final model testing, 1000 posts were pulled from both the LPT and ULPT subreddits. **Notes on the raw data files:**
    - <b>Raw Posts</b>
      - A  custom function was create to automate the API pull from Reddit.
        1) Variabilizes the 'base' url of the Pushshift API
        2) Uses two editable dictionaries of parameters to add-on to the API URL
        3) Assign the` HTTP Response` request and variabilize the `status code`.
        4) An if statement checks that the value of the `status_code` for both subreddits are within the bounds of `Success 2XX`.
        5) If successful:
          - The `JSON` is read-in for each subreddit,
          - The posts are saved from each `JSON`
      - Many posts contain a user-provided ‘subreddit-name’ call, which effectively identifies the subject of the post, and the subreddit on which the post is being made.
      - A large number of posts also only contain post-content in the title section of the post. Although some posts on either subreddit do have text passed into the ‘body’ of the post (for our purposes this was the `selftext` section of the JSON we pulled from via API), a lot of what I was seeing had the majority of content in the title.

- <b>Data Cleaning</b>
  - Although there it is otherwise sensible to consider the text contained in the body of a post to be relevant, we found it easier (logistically) to omit the body from the modeling and focus on the posts' `title`.\
    - One API pull, for example, had almost 60% of posts containing only a string of `[removed]` or `[deleted]`, as well as `NaN` for the `selftext` field. Due to the fact that so many posts relay their content in the title itself, we decided to move forward with that.
  - Some obvious words popped up as candidates for stopwords. Because almost every post contained some sort of ‘key-word’ that would make predictions too easy, the LifeProTips and UnethicalLifeProTips ‘tags’ were discarded as stopwords.
    - A common typo or other pattern that became evident was the ‘lpt’/’ulpt’ identifier to be attached as one word to the word ‘request’, so these were removed as well.
  - The title of each post was lemmatizing, stripped of HTML artifacts, and  removed of stopwords. In some cases, lemmatization helped to 'correct' typos.

- <b>Feature Engineering</b>
  - The target variable `subreddit` was binarized as `1` for posts belonging to LPT, and `0` for posts belonging to ULPT.
- <b>Modeling</b>
  - Every model tested was evaluated based on model accuracy-
  - The models tested: **Logistic Regression**, **K-Nearest Neighbors**, and **Naive Bayes Multinomial**. Each model was almost tested with both the **CountVectorizer** and **TfidfVectorizer** transformers.
  - `Baseline Accuracy` was checked, to evaluate what the models were measuring against/ compared to.
  - A `Pipeline` was created for each model, each of which were `GridSearch`'d over to test, evaluate, and tune the `Hyperparameters` of the models.  

-------------------------------------------------
### <b>Data Dictionary of Included Variables in Model Feature Set</b>:

|Feature|Type|Process|Description|
|---|---|---|---|
|`title`|Vectorized String Object|Using the Vectorizers, the counts of words from each post was passed into the models.|Lemmatized, scrubbed of HTML, and checked for stopwords.|
|`subreddit`|`binarized` object|The target variables in the  dataset|Binarized as `1` for LPT, `0` for ULPT|



-------------------------------------------------
### ***Conclusions***

- **The `Multinomial Naive Bayes` model, with a `CountVectorizer` had the best `accuracy` in predicting the subreddit based on the words in a given post. On one test, the model had a mean `CV` score of `0.80` `accuracy`.**
- **All models tested proved to be overfit, often performing far worse on `Test` data than `Train` data.**


### ***Additional Info Desired***
- Some deeper analysis into the comparative performances across each model type would add more insight.
