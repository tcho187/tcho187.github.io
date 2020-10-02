---
layout: post
title: Hype vs. Fundamental - Can sentiment drive stock prices?
tags: [frontpage, jekyll, blog]
image: '/images/posts/8.jpg'
---

# Introduction

March was **bad**. SPY dropped 100 points. Market melted down with 3 circuit breakers. Suddenly, people were in lockdown. It seemed like Covid 19 was the catalyst for the next economic recession.

![this-is-fine](/images/posts/this-is-fine.jpg)

That was 7 months ago. So, how are we doing now?

If I told you how much tech stocks have risen since March, I can probably convince you that America is doing great. SPY is back up to 337 points. Tesla is up 428% YTD. Zoom is up 609% YTD. Apple split 4-for-1.

If I told you about the unemployment numbers and the dozens of restaurants that can't pay their next rent, I can definitely convince you that America is **NOT** doing ok. 22 million jobs have been lost. 200,000 Americans have died of covid 19. Half of restaurants in NYC may close down permanently. People desperately need the next stimulus check.


!['main-street-wall-street](/images/posts/main-street-wall-street.jpg)
{David Fitzsimmons | The Arizona Star}

It's a tale of two stories. So I begin to question. How is this happening? How can Wall Street be doing so well when Main Street is not ok. Shouldn't these two move in sync? You can't expect businesses to generate money if people don't have jobs and therefore don't have money to spend. Fundamental traders would agree with me here. The current economic landscape is just awful. But that's an erroneous assumption to make. Why?

<div align="center"><strong>Maybe the answer lies in the Federal Reserve.</strong></div>


In response to an economic shutdown that could place a lot of American businesses insolvent, the Fed poured record breaking money into the financial markets. The Fed printed $3 trillion to ensure that liquidity is not an issue. Get the ball rolling no matter how expensive it be. The central bank isn't confined to the same fiscal austerity that you and I are. It can simply print more money if necessary. Implications of the Fed's actions isn't what I want to talk about here. The main takeaway is while the Fed did its job to provide liquidity, under its charter, the Fed cannot provide this liquidity directly. This power is rather controlled by Congress, and bipartisan issues have kept Congress **busy**. So, that means giant corporations use this new liquidity in the form of loans to increase asset prices such as stocks.

<div align="center"><strong>Fundamentals are just not important now. What I call momentum trading is driving the insane valuations of tech stocks.</strong></div>



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
![test](/images/posts/1.jpg)

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

![sentiment-output](/images/posts/finbert-output.jpg)

It's been 7 months since we saw one of the most dramatic stock market crashes due to Covid-19. Stock prices plumetted with the S&P 500 falling more than 12% in March of 2020. After weeks of record breaking decline and circuit breakers in the market, Wall Street has made a meteroric recovery. The S&P 500 closed at an all-time high in August. It took only 5 months to rise from the pandemic hysteria selloff.

Main street tells a different story. The economy lost an estimate of 22 million jobs in March and April due to the coronavirus shutdown according to the Labor Department's survey of business establishments. With retail shops and restaurants closing permanently and the GDP predicted to shrink, regular people living from paycheck to paycheck are finding it difficult to pay for necessities. 

How can Wall Street be doing so well at a time when unemployment numbers are at the Great Depression levels? Maybe the answer lies in the Federal Reserve. In response to an economic shutdown that could place a lot of American businesses insolvent, the Fed poured record breaking money into the financial markets. The Fed printed $3 trillion to ensure that liquidity is not an issue during the pandemic. The Fed convinced Wall Street. Implications of the Fed's actions isn't what I want to talk about here. It's more appropriate for another time.

While the Fed took monetary policy to ensure the money printer keeps printing, it cannot directly pass the money onto the people. Congress has the fiscal power to provide stimulus directly to small businesses. However, bipartisan issues have kept Congress busy. 

So if the money isn't going directly to small businesses, then where is the money going? It's going directly into assets such as stocks. This is where the divergence plays out. My hypothesis is the influx of new capital rushing in at record volume is poured into stocks, particularly certain industries such as Technology, despite horrible earnings per share numbers.

To see if my hypothesis is anywhere correct, I use language model called BERT to predict the sentiment of the earning calls transcripts. What is BERT?

BERT or Bidirectional Encoder Representations from Transformer is an open source language model built from Google AI researchers in 2018, and it's popular among the NLP (Natural Language Processing) community for achieving one of the best performances in solving NLP problems. Why it works so well?

Other language models such as word2vec and GloVe build word embedding representations by taking into consideration the words around. They provide semantic knowledge so context like king to man is queen to woman is interpretable. However, the big issue with these models is the context of th embedding is lost because each word has one embedding. Words carry different meaning in different sentences, and so word embedding representations should do so also. ELMo and ULMFit language models remedy the issue of context. Both models apply a recurrent network called LSTM (Long Short-Term Memory) network to "remember" words before and recalculate the embedding, yielding contexual-dependent embeddings.

BERT uses a masking technique which randomly hides words in an input sentence and predicts the hidden word. It's model is bidirectional. ELMo and ULMFit are not truly bidirectional. The use of Transformer which allows BERT to learn contextual relations in any direction should yield more accurate representations. Another benefit to the use of Transformer is the ability for parallel computing. LSTM is sequential because of its recursive nature and that is a big worry for large computing.

Look at this article for an in-depth post of BERT's architecture.


Now, I have a model that predicts the sentiment of the earnings call transcripts. 


I believe that in certain sectors such as technology, the underlying earnings numbers such as EPS do not predict the movements of stock prices. Fundamentals are out. The influx of cheap money allows growth company ticker prices to rise as long as companies provide investors a positive outlook in their earnings call. So, I hope to see a big difference in stock price after earnings call for companies that provide positive sentiment regardless they beat estimated numbers or not.



**Analysis**

![pprice_diff__sentiment](/images/posts/price_diff__sentiment.png)

Let's look at the top X percentile of tickers that have the biggest price difference before and after earnings call. Those with green show a positive earnings call sentiment whereas those with red show a negative earnings call sentiment.

![price_diff_eps_beat](/images/posts/price_diff_eps_beat.png)

Let's look at the top X percentile of tickers that have the biggest price difference before and after earnings call. Those with green beat earnings per share estimates whereas those with red miss the estimates.


Let's look at the data!

Here's a violin plot of the price difference. 

Here's several more violin plots of top 10 most seen sectors.

Let's look at some time series data!

Here's a time series of the eps difference.

Here's a time series of number of earnings estimations

Here's a time series of the ticker price difference

Let's look at the mekko plot to see the beat or miss counts across the top sectors. 

Let's look at the mekko plot to see the sentiment of earnings call across the top sectors.

Let's look at the mekko plot to see the stock price difference across the top sectors.

Let's look ath the time series of number of earnings calls.

Let's see the breakdown of what the model predicts.



**Hypothesis Testing**

Let's see if there's a statistically significant difference in the price actions between tickers with positive earnings call sentiment and negative earnings call sentiment. 

I perform a t test. T test is a parametric test used to test for a significant difference in the means between 2 groups. 

Parametric test assumptions

* population distrubtions are normal
* samples have equal variances
* the two samples are independent


We expect price change to be fairly normal distributed.

Two samples are indepedent because each record is a ticker price change. There's no way for a ticker to appear in both.

Let's test to see if the population distribution is normal using Shapiro-Wilk test. Let's visualize it on a quantile plot. 

Lastly, let's check for homoscedasticity.


Let's see if there's correlation between a few categorical variables using Chi squared test of indepedence

**Improvements**

1. Dive into model evaluation. I don't have labels for the earnings call transcripts I parsed. So, I don't know how well my model predicts the sentiment. 
2. Compare training set token distribution and the earnings call token distribution. I don't know what the training set looks like. It's not public. I want to see how much overlap there is between the training set and the earnings call. 


https://github.com/pandas-profiling/pandas-profiling
https://www.ahmedbesbes.com/blog/end-to-end-machine-learning
https://towardsdatascience.com/everything-you-need-to-know-about-hypothesis-testing-part-i-4de9abebbc8a
https://analyticsindiamag.com/importance-of-hypothesis-testing-in-data-science/

---


