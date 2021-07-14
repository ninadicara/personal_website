---
layout: post
title: Tweet Semantics in Python
description: Explaining why maths isn't neutral
date: 2019-03-06 15:34:30
---

One of my PhD aims will be around analysing data from Tweets using machine learning/ NLP (natural language processing, **not** neuro-linguistic programming – social work problems).

I’m aiming to achieve this in Python (partly to learn new things, and because it appears to be the industry standard) and so I thought I’d have a go at building a mini-project where I download tweets from Twitter around a certain topic and categorise them as being ‘positive’ or ‘negative’ based on simple semantic analysis. This is my first foray into the world of APIs, semantics and rate-limiting – it was educational.

## Initial thoughts on Python…

As it was my first use of Python for anything beyond experimenting in a Jupyter Notebook, it was also the first time I’ve had to really think about how to structure my code properly. While I’ve done some programming in Java, it’s been a while and I found the class/function structure in Python a bit different – there don’t seem to be getters and setters in quite the same way and classes are used differently… Though I do respect that fact that Python is a lot more concise AND the white space parsing at least means that everyone’s code is formatted properly (in theory).

The biggest bugbear I have with Python at the moment though is the poor documentation for so many packages/modules (and why is there no standard terminology??), and the nightmare of conflicting dependencies. At least with R I know all the packages I use are documented in the same place, in the same format so it’s easy to find answers when I need them. As well as this I’ve never had problems with dependencies in R, though it can happen.

I can’t see myself becoming a Python evangelist, and I’m really not convinced (as some seem to be) that Python is the be-all and end-all of programming languages.

I digress.

What I came up with

## 1. Setting up the API 

First of all I had to sign up to Twitter Dev and register myself to get the keys and tokens needed to run Tweepy.

I chose Tweepy because it seemed pretty straightforward to do the things I wanted to do, the documentation was well laid out and easy to find, and there were lots of examples of it online.

In a poor attempt to bring some structure to my code I put pretty much everything in functions. As a result, the first function literally returns an API object.

```python
def api_setup():

""" Uses keys and tokens from dev_logins file to setup API access. Returns Tweepy API object """

#Use imported keys/tokens to set up API. 
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
api = tweepy.API(auth)

return api
```

So far so good.

## 2. Mining for Tweets

I decided to go for 1000 tweets, as I wasn’t really sure what I was rate-limited to. I decided to allow a search-term argument so I could re-purpose the function.

```python
def collect_tweets(searchterm):

""" Collects 1000 tweets matching searchterm through the api and returns a dataframe with a column of tweets """


text_lst = []
time_lst = []

for tweet in tweepy.Cursor(api_setup().search, q=searchterm).items(1000):
text_lst.append(tweet.text)
time_lst.append(tweet.created_at)

#Turn list into pandas dataframe object
tweets = pd.DataFrame()
tweets['text'] = text_lst
tweets['time'] = time_lst

return tweets
```

I introduce two empty lists, then for each result found through the api I append the actual text of the tweet and the time/date stamp to each list. This leaves me with with two lists of 1000 items, one of tweet text and one of the time and date of the corresponding tweet. Lovely.

If you try to just append each tweet (without calling a specific attribute such as ‘text’) you will get all the attributes of the tweet in a dictionary of Java-Script Object Notation, or JSON. Unless you’re familiar with JSON or need it for some reason then I would recommend calling the specific attributes you need.

Twitter Dev has a very handy page where it lists all the attributes you can call off a tweet object in a nice table. 10/10 would bookmark.

If you’re wondering, the ‘Cursor’ part of the Tweepy search avoids having to iterate though ‘pages’ of tweets. See the documentation here.

## 3. Data Cleaning 

This was the more interesting part of my mini-project. Having studied data-mining before I knew I’d need to get rid of the stop words in the tweets, but how to also get rid of the annoying links, hashtags and punctuation?

I’ve been learning about regular expressions/regex recently and found that you can achieve removal of special characters and links using regex which is super handy and uses much less computational time than writing a Python function to search through for explicit characters.

```python
def remove_specials(df):

""" For tweets in a Pandas dataframe under a column called 'text', will remove special characters, hyperlinks"""

for row in df.index:
msg = df.loc[row,'text']
msg = msg.lower()
#Regex statement to remove hyperlinks and special characters
msg = ' '.join(re.sub("(@[A-Za-z0-9]+)|([^0-9A-Za-z \t])|(\w+:\/\/\S+)", " ", msg).split())
df.loc[row,'text'] = msg

return df


def remove_stops(df):

""" For tweets in a Pandas dataframe under a column called 'text' will remove all stop words from 'many_stop_words' package """

stop_words = list(get_stop_words('en')) 
stop_words.append('rt') #add RT as a stopword because it does not add value to the dataset.

for row in df.index:

#split message into list of individual words
msg = df.loc[row,'text'].split(" ")
newmsg = ""

#for each word in the list, if it's not in stop_words then keep in by adding to newmsg. 
for word in msg:
if word not in stop_words:
newmsg = newmsg + " " + word

#reassign value of df cell as newmsg which has no stop words 
df.loc[row,'text'] = newmsg

return df
```

Another decision to make was whether to just have one function which cleaned data, but I felt that splitting it down into the separate operations was better form, and then I just needed another function that called them.

## 4. Semantics

The last thing to do was then to apply semantic analysis. I chose TextBlob as it was simple to apply to the data and was recommended on a few sites. In reality I’m looking at libraries such as LIWC for more advanced semantic analysis, and eventually hoping to move beyond semantics into applying machine learning.

The function takes a dataframe as an argument, then adds two new columns. It fills those columns with the corresponding tweet polarity and subjectivity scores from TweetBlob for each tweet. Simples!

```python
def sentiment_analysis(df):

""" Given dataframe with tweets in 'text' column, returns dataframe with sentiment polarity and subjectivity for each tweet """

df["Polarity"] = ""
df["Subjectivity"] = ""

for row in df.index: 
df.loc[row,'Polarity'] = TextBlob(df.loc[row,'text']).sentiment.polarity
df.loc[row,'Subjectivity'] = TextBlob(df.loc[row,'text']).sentiment.subjectivity

return df
```

## 5. To do next…

The next thing I’m trying to do is present the polarity data in a bar chart showing the number of ‘positive’, ‘negative’ and ‘neutral’ tweets about the chosen topic, and maybe then trying to do some nice time-based visualization.

E.G – Did the number of positive and negative tweets around Islam change before and after the reporting of Shamima Begum’s bid to return to the UK? (There have been anecdotal reports of an increase in Islamophobia associated with the aggressive reporting).

Unfortunately this won’t be too easy to find out since there is a limit on the tweets I am able to collect and over what time frame I can collect them – still trying to understand this rate limiting thing!
