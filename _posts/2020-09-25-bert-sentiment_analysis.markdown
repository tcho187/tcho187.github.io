---
layout: post
title: Hype vs. Fundamental - Can sentiment drive stock prices?
tags: [frontpage, jekyll, blog]
image: '/images/posts/8.jpg'
---

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

To analyze sentiment analysis, I add a classification layer on top the Transformer output for the [CLS] token. Fortunately, I don't need to do this because someone else has already fine-tuned BERT for financial statements. FinBERT takes the pretrained language model on generic text such as Wikipedia and further trains the model on a financial corpus called TRC2-financial, a subset of Reuter's TRC2 dataset. Roughly 46,000 news articles from 2008 to 2010 are used to build the language model. 

The classification layer that FinBERT builds from is the Financial PhraseBank dataset. Roughly 5,000 labeled sentences are used to train and validate the classification layer.

You can find the article [here](https://arxiv.org/pdf/1908.10063.pdf).

Now, I have a model that predicts the sentiment of the earnings call transcripts. 

First, I need to collect the earnings call transcripts. I couldn't find a free repository of earnings call transcripts that I can programatically pull from. There are sites such as Seeking Alpha and Motley Fool that publish earnings call transcripts. So, I decide to write a python script to scrape their websites. The easiest method is to use a library like requests and retrieve the HTTP response from the url. However, urls with lots of daily traffic often add security layers to prevent sending data from HTTP requests. Sites prevent bots from clogging traffic with infinite url requests and prevent hackers from stealing data.

Seeking Alpha has a login page that prevents me making requests to its earnings call trascript section. So, I decide to use Selenium to parse the earnings call transcripts on Motley Fool. Selenium renders the browser and allows me to interact with the DOM of the page. It's much slower than retrieving responses with requests but I'll run into less problems with Selenium.
![test](/images/posts/1.jpg)
### Todo

Duis id ante elit. Aliquam quis tellus id orci eleifend finibus. Donec consequat justo ligula, eget sodales purus hendrerit at.

1. Ut at interdum nunc. Maecenas commodo turpis quis elementum gravida.
2. Nunc ac sapien tellus. Quisque risus enim, tempus eget porttitor in, pellentesque vel urna.
    Donec nibh massa, rutrum a sollicitudin eu,
lacinia in lorem.

### Graphic design

> Graphic design is the paradise of individuality, eccentricity, heresy, abnormality, hobbies, and humors. - George Santayana

Vim te case nihil oblique, has partem interpretaris ne, ad admodum accusamus nam. Usu utinam dissentias referrentur ne, vim accusam voluptua pertinacia no. Est no posse utinam inermis, brute errem mentitum et ius, te prompta albucius quo. In pro simul soleat regione.

![alt](https://images.unsplash.com/photo-1433785567155-bf5530cab72c?ixlib=rb-0.3.5&q=80&fm=jpg&crop=entropy&w=1080&fit=max&s=1348aea714b9493fa61a09a8c01113e6)

Ne reque offendit singulis mea, ad eos ferri doming nostrud. Duis suscipit usu ut, fuisset pericula ex est, et porro prompta his. Audire definiebas voluptatibus et duo, aperiam ocurreret ad nec. Vel ad nostrud principes. Ad liber congue iracundia sed, eirmod erroribus eam te, has veniam epicurei ea.

Pri probo alterum aliquando an. Duo appetere laboramus intellegat ea, ex suas diam exerci vix. Mel simul debitis id, est nusquam fuisset mentitum in. Te mei iudico iisque.