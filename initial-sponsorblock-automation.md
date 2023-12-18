Title: Initial Sponsorblock automation
Date: 2023-11-25
Tags: sponsorblock

Many youtube channels create videos with constant intro and outro segments. Common examples are news channels or sport highlight channels. These channels are the perfect usecase to automatically create sponsorblock segments.

Below contains key parts of the current codebase.

Store metadata about channels and segments. Below indicates that for the given youtube channel, I want to create 2 segments for each video - an intro segment from 0 to 3 seconds and an outro segment from t-5 to 5 seconds.

```json
SEGMENT_METAS={
    "youtube_channel":[
         {
             "category":"intro",
             "seconds_from_start":3
         },
         {
             "category":"outro",
             "seconds_from_end":5
         },
     ]
}
```

Get latest videos from a youtube channel. 

```python
import scrapetube

def get_latest_videos(channel_url:str,limit:int=10):
    videos = scrapetube.get_channel(
        channel_url=channel_url
        , limit=limit
        , sort_by="newest"
        , content_type="videos"
    )
    return videos
```

Add segment with sponsorblock API.

```python
def add_segment(youtube_video_id: str, category: str, start_time:int, end_time:int):
    post_endpoint = "https://sponsor.ajay.app/api/skipSegments"

    params = {
        "videoID": youtube_video_id,
        "userID": private_id,
        "userAgent": USER_AGENT",
        "segments": [{
            "segment": [start_time,end_time],
            "category": category
        }]
    }
    res = requests.post(post_endpoint, json = params)
    return res
```

I have metadata set up for around 10 channels. Current code is in a google colab notebook and I run it when I have a chance. The ideal state would be automated scheduling - some possibilities include automated databricks notebook, state machine / lambda functions, airflow etc. Databricks will probably require the least effort to migrate. 

I also need to add support for more segment metadata. The current setup assumes that all of a channel's videos have the same segments. This is not always true - videos could be in a specific series and have different segments. I need to create conditions for each segment like video name, duration, etc. 

