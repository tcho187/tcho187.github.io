---
layout: post
title: Hype vs. Fundamental - Can sentiment drive stock prices?
tags: [bert, jekyll, blog, stocks]
featured: true
image: '/images/posts/pexels-marius-mann.jpg'
---

# Introduction

March was **bad**. SPY dropped 100 points. Market melted down with 3 circuit breakers. Suddenly, people were in lockdown. It seemed like Covid 19 was the catalyst for the next economic recession.

![this-is-fine](/images/posts/this-is-fine.jpg)

That was 7 months ago. So, how are we doing now?

If I told you how much tech stocks have risen since March, I can probably convince you that America is doing great. SPY is back up to 337 points. Tesla is up 428% YTD. Zoom is up 609% YTD. Apple split 4-for-1.

If I told you about the unemployment numbers and the dozens of restaurants that can't pay their next rent, I can definitely convince you that America is **NOT** doing ok. 22 million jobs have been lost. 200,000 Americans have died of covid 19. Half of restaurants in NYC may close down permanently. People desperately need that next stimulus check.


!['main-street-wall-street](/images/posts/main-street-wall-street.jpg)
{David Fitzsimmons | The Arizona Star}

It's a tale of two stories. So I begin to question. How is this happening? How can Wall Street be doing so well when Main Street is not ok. Shouldn't these two move in sync? You can't expect businesses to generate money if people don't have jobs and therefore don't have money to spend. Fundamental traders would agree with me here. 

> The current economic landscape is just awful. But that's an erroneous assumption to make. Why?

<div align="center"><strong>Maybe the answer lies in the Federal Reserve.</strong></div><br>


In response to an economic shutdown that could place a lot of American businesses insolvent, the Fed poured record breaking money into the financial markets. The Fed printed $3 trillion to ensure that liquidity is not an issue. Get the ball rolling no matter how expensive it be. The central bank isn't confined to the same fiscal austerity that you and I are. It can simply print more money if necessary. Implications of the Fed's actions isn't what I want to talk about here. The main takeaway is while the Fed did its job to provide liquidity, under its charter, the Fed cannot provide this liquidity directly. This power is rather controlled by Congress, and bipartisan issues have kept Congress **busy**. So, that means giant corporations use this new liquidity in the form of loans to increase asset prices such as stocks.

<div align="center"><strong>Fundamentals are just not important now. What I call momentum trading is driving the insane valuations of tech stocks.</strong></div><br>


One measure of what's driving stock prices is the earnings call. The market knows fundamentals are bad across the board. If the company c-suite can convince investors that the future looks bright, well then I think stock prices go up regardless of what the fundamental metrics like Earnings Per Share (EPS) say otherwise.

So, to see if my hypothesis is right, I collect a bunch of earnings call transcripts, run sentiment analysis, and compare it to the stock prices before and after the earnings call.


**Hypothesis**

It's important to setup my hypothesis before I conduct this experiment. It's easy to find patterns in the data that don't mean much. Confirmation bias is **scary**. 

>Null Hypothesis (H₀): X₀ = X₁

>Alternate Hypothesis (H₁): X₀ ≠ X₁

>X₀ = Stock price difference before and after earnings call for positive earnings call
>X₁ = Stock price difference before and after earnings call for negative earnings call

> Significance level: α = 0.05

__Layman's translation:__ 
I conduct statistical tests. If the tests have significance, then they supports my hypothesis- the alternate hypothesis. Stock prices are different when the earnings calls are positive vs when the earnings calls are negative.



## Data Collection

__Earnings call transcripts__

First, I need to collect the earnings call transcripts. I couldn't find a free repository of earnings call transcripts. There are sites such as Seeking Alpha and Motley Fool that publish earnings call transcripts. So, I decide to write a python script to scrape their websites. The easiest method is to use a library like requests and retrieve the HTTP response from the url. However, urls with lots of daily traffic often add security layers to prevent sending data from HTTP requests. Sites prevent bots from clogging traffic with infinite url requests and prevent hackers from stealing data.

Seeking Alpha has a login page that prevents me from making requests to its earnings call transcript section. So, I decide to use another library called Selenium to parse the earnings call transcripts on Motley Fool. Selenium renders the browser and allows me to interact with the DOM of the page. It's slower than retrieving responses with requests but I'll run into less problems with Selenium. I'm not worried about performance here.


I parse out the HTML using BeautifulSoup4. I retrieve the following information:
* Ticker
* Company Name
* Stock Exchange
* Date of earnings call
* Time of earnings call
* Date of next market day
* Date of previous market day
* Earnings call transcript
* Date of Earnings Quarter

__Finnhub API__

I also need information about the ticker prices before and after earnings calls. So, I use [Finnhub API](https://finnhub.io/docs/api). It's free to use for most of the methods. The only downside is a limit on the number of api calls. I retrieve the follow information:
* Opening Price of day before earnings call
* Closing Price of day before earnings call
* High Price of day before earnings call
* Low Price of day before earnings call
* Volume of day before earnings call
* Opening Price of day during/after earnings call
* Closing Price of day during/after earnings call
* High Price of day during/after earnings call
* Low Price of day during/after earnings call
* Volume of day during/after earnings call
* Estimated EPS
* Actual EPS
* Date of EPS
* Industry


## Model

To analyze sentiment analysis, I add a classification layer on top the Transformer output for the [CLS] token. Fortunately, I don't need to do this because someone else has already fine-tuned BERT for financial statements. FinBERT takes the pretrained language model on generic text such as Wikipedia and further trains the model on a financial corpus called TRC2-financial, a subset of Reuter's TRC2 dataset. Roughly 46,000 news articles from 2008 to 2010 are used to build the language model. 

The classification layer that FinBERT builds from is the Financial PhraseBank dataset. Roughly 5,000 labeled sentences are used to train and validate the classification layer.

You can find the article [here](https://arxiv.org/pdf/1908.10063.pdf).

[reference](https://medium.com/prosus-ai-tech-blog/finbert-financial-sentiment-analysis-with-bert-b277a3607101)



## Prediction

`python3 predict.py --text_path earnings_call.txt --output_dir output/ --model_path models/classifier_model/finbert-sentiment`

I collect X tickers from March 2020 to September 2020. I run each earnings call transcript through FinBERT and get the corresponding softmax probabilities for the sentiment label, sentiment label, and the sentiment score, which is the probability of a positive sentiment - probability of a negative sentiment. 

![sentiment-output](/images/posts/finbert-output.png)

Now, I have a model that predicts the sentiment of the earnings call transcripts. 


I believe that in certain sectors such as technology, the underlying earnings numbers such as EPS do not predict the movements of stock prices. Fundamentals are out. The influx of cheap money allows growth company ticker prices to rise as long as companies provide investors a positive outlook in their earnings call. So, I hope to see a big difference in stock price after earnings call for companies that provide positive sentiment regardless they beat estimated numbers or not.



**Analysis**

I use a package called [pandas_profiling](https://github.com/pandas-profiling/pandas-profiling) to calculate basic information such as histograms, summary statistics, and correlations.

![summary](/images/posts/summary.png)

There's 2,291 observations and 40 variables.

![correlation](/images/posts/correlation.png)
The report shows that several variables are highly correlated.

But I wouldn't worry because the correlations make sense. I expect earnings per share estimate to follow earnings per share actual. I expect closing price to follow opening price.

**Since I'm not building a prediction model or causal model, I don't have to worry about the effects of correlation.**

Let's breakdown some variables. 

![ticker_time_series](/images/posts/ticker_time_series.png)
1. We're looking at a timeframe between Feb-25-2020 and July-21-2020.
2. 1,058 stocks come from NASDAQ and 1,219 come from NYSE.
3. 997 elect to announce its earnings after trading day and 1,294 elect to announce before trading day.

![sentiment_score](/images/posts/sentiment_score.png)
Here's a breakdown of the sentiment score. We see that the tails are long an d very few are in the middle. 

![pprice_diff_sentiment](/images/posts/price_diff_sentiment.png)

Those with green show a positive earnings call sentiment whereas those with red show a negative earnings call sentiment. CCI had a positive sentiment but prices dumped. 

![price_diff_eps_beat](/images/posts/price_diff_eps_beat.png)

Those with green beat earnings per share estimates whereas those with red miss the estimates. Again, there's no pattern between earnings per share performance and the stock price movement. CCI beat the estimate and yet stock price plummeted. 


![violin_plot](/images/posts/violin_plot.png)
Here's a violin plot of the price difference. Overall the interquartile range is around $2. This doesn't say much. It'd be more effective if we look at the percentage change. One ticker had a massive rise of $49 and one had a drop of $178. I wonder if those are outliers.


Here's several more violin plots of top 10 most seen sectors.
![violin_plot_tech](/images/posts/violin_plot_tech.png)
![violin_plot_real_estate](/images/posts/violin_plot_real_estate.png)
![violin_plot_health_care](/images/posts/violin_plot_health_care.png)
![violin_plot_biotechnology](/images/posts/violin_plot_biotechnology.png)
![violin_plot_banking](/images/posts/violin_plot_banking.png)
![violin_plot_energy](/images/posts/violin_plot_energy.png)
![violin_plot_financial_services](/images/posts/violin_plot_financial_services.png)


May shows a bigger range of eps difference, largely due to q2 earnings being a mystery due to covid.

![eps_time_series](/images/posts/eps_time_series.png)


No time pattern in the beat or miss graph.

![beat_or_miss_time_series](/images/posts/beat_or_miss_time_series.png)


Again, May shows a volatile month of price swings after earnings call.

![stock_price_time_series](/images/posts/stock_price_time_series.png)

Mekko plots are nice to look at the ratio of categories across several industries. Tech and real estate show a large percentage of the data and Tech had a 2 to 1 ratio of beats to misses. 

![mekko_beat_or_miss](/images/posts/mekko_beat_or_miss.png)

It's interesting to see that across all industries, executives had positive sentiment. Who wouldn't during a pandemic?

![mekko_sentiment](/images/posts/mekko_sentiment.png)


While Tech see a 2 to 1 ratio of beats to misses, stock price difference is overall negative.
Let's look at the mekko plot to see the sentiment of earnings call across the top sectors.
![mekko_price_difference](/images/posts/mekko_price_difference.png)



**Hypothesis Testing**

Let's see if there's statistically significant difference in the price actions between tickers with positive earnings call sentiment and negative earnings call sentiment. 

![t_test](/images/posts/t_test.png)

I perform a t test. T test is a parametric test used to test for a significant difference in the means between 2 groups. 

Parametric test assumptions

* population distrubtions are normal
* samples have equal variances
* the two samples are independent

![pos_sent_dist](/images/posts/pos_sent_dist.png)
![neg_sent_dist](/images/posts/neg_sent_dist.png)
![pos_sentiment_shap](/images/posts/pos_sentiment_shap.png)
![neg_sentiment_shap](/images/posts/neg_sentiment_shap.png)
We assume price change to be fairly normal distributed. However, visually and statistically, the distribution of price difference between the positive sentiment group and the negative sentiment group is not normal. 

Staistically, I apply the Shapiro-Wilk test method. The test is significant and I reject the null hypothesis. So, the distributions are not normal.

![leven](/images/posts/leven.png)

I apply the Levene method to check for homogeneity of variance. A p-value of .3 means the test is not significant and the variances are equal. 

![mannwhitney](/images/posts/mannwhitney.png)

Instead of the independent t-test, let's try the nonparametric Mann-Whitney U test. 

The test has a p-value of .9 so I can't reject the null hypothesis, which says the distributions are equal. 

The takeaway then is sentiment does not have statistical significance in price difference distributions or more correctly, I can't reject the hypothesis,which says that the distributions are the same.


![chisquare](/images/posts/chisquare.png)
Let's see if there's correlation between a few categorical variables using Chi squared test of indepedence.

The relationship between beat or miss and sentiment has a chi square of 5.4 and a p-value of .019. I can reject the null hypothesis and say that the two are dependent. So, there is a relationship between sentiment and beat or miss. Most likely, the executives want to relay a particular sentiment when the company beats or misses its earnings per share. 

I can't reject the hypothesis that beat or miss is independent of price difference or that sentiment is independent of price difference. 


## Takeaway
The nonparametric Whitney-Mann U test and Chi Squared test of independence both fail to reject the null hypothesis of any relationship between the stock price movement and the sentiment of the earnings call. My initial hypothesis is incorrect. I can think of other factors that complicate the hypothesis. With the recent surge, I've seen stocks double or triple the March lows and dump after earnings even after showing positive eps because the company didn't blow out the earnings. On the other hand, companies with misses see a rise in stock price because of forward guidance from the executives in the earnings call. Earnings call is one piece of the puzzle of stock price movement.


**Improvements**

1. Dive into model evaluation. I don't have labels for the earnings call transcripts I parsed. So, I don't know how well my model predicts the sentiment. 
2. Compare training set token distribution and the earnings call token distribution. I don't know what the training set looks like. It's not public. I want to see how much overlap there is between the training set and the earnings call. 



---


