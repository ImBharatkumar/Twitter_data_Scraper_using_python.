# Twitter_data_Scraper_using_python.
I have created a web app named TwitterdataScraper using Streamlit framework and python.

## =>Why snscrape?
'''Snscrape is another approach for scraping information from Twitter that does not require the use of an API. Snscrape allows you to scrape basic information such as a user's profile, tweet content, source, and so on.
Snscrape is not limited to Twitter, but can also scrape content from other prominent social media networks like Facebook, Instagram, and others.
Its advantages are that there are no limits to the number of tweets you can retrieve or the window of tweets (that is, the date range of tweets). So Snscrape allows you to retrieve old data.
'''
## =>work flow
#### step 1) install streamlit in conda environment and make a directory.
install using- cmd => pip install streamlit

#### step2) creat a python file in same directory.

#### step3)code=>
```# Import the all necessary libraries

import snscrape.modules.twitter as sntwitter
import pandas as pd
import pymongo
import streamlit as st
import json
import csv

#Establishing connection with mongodb
client = pymongo.MongoClient("mongodb://localhost:27017")
#creating database and collection to store data
db = client["Tweets_data"]
col = db.Scrape_data

#GUI interface
st.image('./kpk.png', width=100)
st.header("Twitter data scraper ")
limit = st.number_input("enter number of data you want to scrape")
query = st.text_input("Enter a keyword you want to scrape")


tweets = [] # list to store scraped data
cols = ['date', 'id', 'url', 'tweet_content', 'reply_count', 'retweet_count', 'language', 'source', 'like_count']

st.header("Hit the button to scrape data")

#below code will scrape data from twitter
if st.button("Scrape"):
    def tweet_scraper(limit, query):
        for tweet in sntwitter.TwitterSearchScraper("query" + ' since:2023-01-01 lang:pt').get_items():
            if len(tweets) == limit:
                break
            else:
                tweets.append(
                    [tweet.date, tweet.id, tweet.url, tweet.content, tweet.replyCount, tweet.retweetCount, tweet.lang,
                     tweet.source, tweet.likeCount])
        df = pd.DataFrame(tweets, columns=cols)
        data = df.to_dict(orient="records")
        db.Scrape_data.insert_many(data)#
        st.subheader("scraped data")

    tweet_scraper(limit,query)

df = pd.DataFrame(tweets, columns=cols)
#scraped data will be displayed
if st.checkbox("show_data"):
    st.subheader('Data')
    st.write(df)

#fetching the data from mongodb and converting into Dataframe
cursor = col.find()
cursor_list = list(cursor)
df = pd.DataFrame(cursor_list)

st.header("Download the data")
#number of data need to be downloaded is selected and displayed
no_of_datas = st.slider("select max_data ", 10, 1000, step=10)
df = df.head(no_of_datas)
if st.checkbox('Data is ready'):
    st.subheader("downloaded data")
    st.write(df)

#data is downloaded in JSON format
if st.button("Download JSON"):
    json = df.to_json(orient='records',lines=True,default_handler=str)
    with open('temp.json', 'w') as f:
        f.write(json)
if st.checkbox("Display"):
    with open('temp.json', 'r') as f:
        st.write(f.read())

#data is downloaded in csv format
if st.button("Download csv"):
    csv = df.to_csv()
    with open('temp1.csv', 'w', encoding='utf-8') as f:
        f.write(csv)
if st.checkbox("Display."):
    with open('temp1.csv', 'r') as f:
        st.write(f.read()) .
```



#### step4) open terminal from conda environment where you have installed streamlit.
enter cmd=>streamlit run name.py file

#### Result:
#you will be directed to a interactive website , where you can scrape and download the twitter data in json and csv format
