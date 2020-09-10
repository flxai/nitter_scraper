# Nitter Scraper

This library is a work around for anyone who enjoyed the simplicity of the [twitter-scraper](https://github.com/bisguzar/twitter-scraper/) library and needs a quick replacement until it comes back up.  Nitter Scraper leverages running a docker instance of [nitter](https://github.com/zedeus/nitter) to scrape tweets and profile information. I attempted to make the api work as closely as possible to the original to minimize refactoring of other projects.

## Use Nitter Scraper with docker
If you have docker installed you can use the NitterDockerContainer manager. It takes care of starting/stoping a local nitter docker container and provides the address to scrape from. If you don't use the NitterDockerContainer the get_tweets and get_profile methods will resolve to scraping from https://nitter.net

```
from nitter_scraper import NitterDockerContainer, get_tweets

with NitterDockerContainer(host="0.0.0.0", port=8008) as nitter:
    for tweet in get_tweets("dgnsrekt", address=nitter.address):
        print(tweet.json(indent=4))

```

## Prerequisites

* Docker
* Python ^3.7

## Install
```
pip install nitter-scraper
```

## Examples

#### Run
```
from nitter_scraper import NitterDockerContainer, get_profile

with NitterDockerContainer(host="0.0.0.0", port=8008) as nitter:
    profile = get_profile("dgnsrekt", address=nitter.address)
    print(profile.json(indent=4))

```
#### Output
```
2020-09-09 21:07:33.025 | INFO     | nitter_scraper.nitter:get_client:33 - Docker connection successful.
2020-09-09 21:07:34.992 | INFO     | nitter_scraper.nitter:start:89 - Running container dreamy_bhaskara a812e3d41f.
{
    "username": "DGNSREKT",
    "name": "DGNSREKT",
    "profile_photo": "/pic/profile_images%2F1303625659399516160%2FjLtzJMAr.png",
    "tweets_count": 2898,
    "following_count": 904,
    "followers_count": 119,
    "likes_count": 4995,
    "is_verified": false,
    "banner_photo": "/pic/profile_banners%2F2474416796%2F1599643557%2F1500x500",
    "biography": "TOO THE MOON!!!",
    "user_id": 2474416796,
    "location": "Moon",
    "website": "https://github.com/dgnsrekt"
}
2020-09-09 21:07:35.920 | INFO     | nitter_scraper.nitter:stop:92 - Stopping container dreamy_bhaskara a812e3d41f.
2020-09-09 21:07:41.335 | INFO     | nitter_scraper.nitter:stop:95 - Container dreamy_bhaskara a812e3d41f Destroyed.

```

## Profile & Tweet objects
The Profile and Tweet classes inherit from pydantic's BaseModel. This makes it easy to convert to a dict or json.
```
profile = get_profile("dgnsrekt")

profile.json(indent=4)

profile.dict()
```

### Profile Schema
These are the fields uses in the Profile class which is returned from the get_profile() method.
```
{
    "title": "Profile",
    "type": "object",
    "properties": {
        "username": {
            "title": "Username",
            "type": "string"
        },
        "name": {
            "title": "Name",
            "type": "string"
        },
        "profile_photo": {
            "title": "Profile Photo",
            "type": "string"
        },
        "tweets_count": {
            "title": "Tweets Count",
            "type": "integer"
        },
        "following_count": {
            "title": "Following Count",
            "type": "integer"
        },
        "followers_count": {
            "title": "Followers Count",
            "type": "integer"
        },
        "likes_count": {
            "title": "Likes Count",
            "type": "integer"
        },
        "is_verified": {
            "title": "Is Verified",
            "type": "boolean"
        },
        "banner_photo": {
            "title": "Banner Photo",
            "type": "string"
        },
        "biography": {
            "title": "Biography",
            "type": "string"
        },
        "user_id": {
            "title": "User Id",
            "type": "integer"
        },
        "location": {
            "title": "Location",
            "type": "string"
        },
        "website": {
            "title": "Website",
            "type": "string"
        }
    },
    "required": [
        "username",
        "name",
        "profile_photo",
        "tweets_count",
        "following_count",
        "followers_count",
        "likes_count",
        "is_verified"
    ]
}


```

### Tweet Schema
These are the fields uses in the Tweet class which is returned from the get_tweets() method.

```
{
    "title": "Tweet",
    "type": "object",
    "properties": {
        "tweet_id": {
            "title": "Tweet Id",
            "type": "integer"
        },
        "tweet_url": {
            "title": "Tweet Url",
            "type": "string"
        },
        "username": {
            "title": "Username",
            "type": "string"
        },
        "is_retweet": {
            "title": "Is Retweet",
            "type": "boolean"
        },
        "is_pinned": {
            "title": "Is Pinned",
            "type": "boolean"
        },
        "time": {
            "title": "Time",
            "type": "string",
            "format": "date-time"
        },
        "text": {
            "title": "Text",
            "type": "string"
        },
        "replies": {
            "title": "Replies",
            "type": "integer"
        },
        "retweets": {
            "title": "Retweets",
            "type": "integer"
        },
        "likes": {
            "title": "Likes",
            "type": "integer"
        },
        "entries": {
            "$ref": "#/definitions/Entries"
        }
    },
    "required": [
        "tweet_id",
        "tweet_url",
        "username",
        "is_retweet",
        "is_pinned",
        "time",
        "text",
        "replies",
        "retweets",
        "likes",
        "entries"
    ],
    "definitions": {
        "Entries": {
            "title": "Entries",
            "type": "object",
            "properties": {
                "hashtags": {
                    "title": "Hashtags",
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                },
                "cashtags": {
                    "title": "Cashtags",
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                },
                "urls": {
                    "title": "Urls",
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                },
                "photos": {
                    "title": "Photos",
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                },
                "videos": {
                    "title": "Videos",
                    "type": "array",
                    "items": {
                        "type": "string"
                    }
                }
            },
            "required": [
                "hashtags",
                "cashtags",
                "urls",
                "photos",
                "videos"
            ]
        }
    }
}

```


## Limitation

* So far i've been able to pull about 800 tweets per user without issue.
* Unable to implement a way to scrape trends.
* birthday and is_private are not implemented in the profile.
* If the user does not have a banner the user_id cannot be scraped.
* The user_id cannot be scraped from tweets.

More docs coming soon.

## TODO
* setup.py
* pypi

## Contact Information
Telegram = Twitter = Tradingview = Discord = @dgnsrekt

Email = dgnsrekt@pm.me
