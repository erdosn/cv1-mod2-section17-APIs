
### Questions
* how do we put our secret keys into our environment? 

### Objectives
YWBAT
* hide permissions in your notebook
* use permissions to access data on twitter API
* store this data in a csv

### Outline
* Questions
* Setting up our permissions
* doing a basic twitter search
* ??? more fun twitter stuff


```python
import os
import json
import pandas as pd
import numpy as np
import tweepy as tw

from pprint import pprint

import matplotlib.pyplot as plt
import seaborn as sns
```


```python
os.mkdir("data")
```

### Setting up api for twitter


```python
with open("/Users/rafael/.ssh/twitter_app00.json") as f:
    d = json.load(f)
d.keys()
```




    dict_keys(['consumer_key', 'consumer_secret', 'access_token', 'access_token_secret'])




```python
auth = tw.OAuthHandler(consumer_key=d["consumer_key"], consumer_secret=d["consumer_secret"])
```


```python
auth.set_access_token(key=d["access_token"], secret=d["access_token_secret"])
```


```python
api = tw.API(auth, wait_on_rate_limit=True, wait_on_rate_limit_notify=True)
```

### Basic Search


```python
res = api.search("elon musk")
```


```python
len(res)
```




    15




```python
for r in res:
    pprint(r._json["text"])
```

    ('RT @bony: 25k retweets and elon musk will make anime girls real '
     'https://t.co/xVB0YPu1Rk')
    ("RT @nowthisnews: Two Teslas raced, one above ground the other in Elon Musk's "
     'new tunnel — and the winner won by a landslide https://t.co/mb…')
    ('RT @libshipwreck: If you had a nickel for every techno-utopian fantasy that '
     'generated a ton of excited attention (and which you would get c…')
    ('📢 Elon Musk planea mudarse a Marte dentro de siete años #Viajes '
     'https://t.co/oVHv6KMKuE')
    ('RT @davidcnswanson: Stargazers worried by lights in the sky from Elon Musk’s '
     'new satellite network - https://t.co/4nFmaNgUK7 -- You misspel…')
    ("RT @nowthisnews: Two Teslas raced, one above ground the other in Elon Musk's "
     'new tunnel — and the winner won by a landslide https://t.co/mb…')
    ("RT @CNET: Elon Musk's new Starlink satellites will make it harder to study "
     'the sky with ground-based telescopes.\n'
     'https://t.co/Bs2aOAxmaI')
    ('RT @IBJIYONGI: It is surreal watching astronomers get pissed about Elon Musk '
     'colonizing the night sky while so many of the same people were…')
    ("RT @nowthisnews: Two Teslas raced, one above ground the other in Elon Musk's "
     'new tunnel — and the winner won by a landslide https://t.co/mb…')
    ("RT @nowthisnews: Two Teslas raced, one above ground the other in Elon Musk's "
     'new tunnel — and the winner won by a landslide https://t.co/mb…')
    ('@CGasparino @PAH78573620 @notabigdeal111 @willy71334976 Hey could you maybe '
     "compliment Elon Musk's stock pumping ab… https://t.co/4JFN2OkCOw")
    ("SpaceX Starlink satellites won't outshine science insists Elon Musk "
     'https://t.co/Q832RUnykY')
    ("RT @nowthisnews: Two Teslas raced, one above ground the other in Elon Musk's "
     'new tunnel — and the winner won by a landslide https://t.co/mb…')
    'RT @serinide: elon musk made catgirls real https://t.co/BbISkxIZtT'
    ("RT @nowthisnews: Two Teslas raced, one above ground the other in Elon Musk's "
     'new tunnel — and the winner won by a landslide https://t.co/mb…')



```python
followers = api.followers_ids()
```


```python
class MyStreamListener(tw.StreamListener):
    
    def on_status(self, status):
        dlist.append(status._json)
        if len(dlist)%20==0:
            print("gathered {} tweets".format(len(dlist)))
            

    def on_error(self, status_code):
        if status_code == 420:
            #returning False in on_error disconnects the stream
            print("D/C sorry :/")
            return False
```


```python
dlist = []
global dlist
```


```python
listener = MyStreamListener()
```


```python
myStream = tw.Stream(auth = api.auth, listener=listener)
```


```python
myStream.filter(track=["data science", "data scientist", "machine learning", "deep learning", '#machinelearning', '#deeplearning', '#data', 'data'])
```

    gathered 20 tweets
    gathered 40 tweets
    gathered 60 tweets
    gathered 80 tweets
    gathered 100 tweets
    gathered 120 tweets
    gathered 140 tweets
    gathered 160 tweets
    gathered 180 tweets
    gathered 200 tweets
    gathered 220 tweets
    gathered 240 tweets
    gathered 260 tweets
    gathered 280 tweets
    gathered 300 tweets
    gathered 320 tweets
    gathered 340 tweets
    gathered 360 tweets
    gathered 380 tweets
    gathered 400 tweets
    gathered 420 tweets
    gathered 440 tweets
    gathered 460 tweets
    gathered 480 tweets
    gathered 500 tweets
    gathered 520 tweets



    --------------------------------------------------------------------------

    WantReadError                            Traceback (most recent call last)

    /anaconda3/lib/python3.6/site-packages/urllib3/contrib/pyopenssl.py in recv_into(self, *args, **kwargs)
        279         try:
    --> 280             return self.connection.recv_into(*args, **kwargs)
        281         except OpenSSL.SSL.SysCallError as e:


    /anaconda3/lib/python3.6/site-packages/OpenSSL/SSL.py in recv_into(self, buffer, nbytes, flags)
       1714             result = _lib.SSL_read(self._ssl, buf, nbytes)
    -> 1715         self._raise_ssl_error(self._ssl, result)
       1716 


    /anaconda3/lib/python3.6/site-packages/OpenSSL/SSL.py in _raise_ssl_error(self, ssl, result)
       1520         if error == _lib.SSL_ERROR_WANT_READ:
    -> 1521             raise WantReadError()
       1522         elif error == _lib.SSL_ERROR_WANT_WRITE:


    WantReadError: 

    
    During handling of the above exception, another exception occurred:


    KeyboardInterrupt                        Traceback (most recent call last)

    <ipython-input-81-f322930f6294> in <module>()
    ----> 1 myStream.filter(track=["data science", "data scientist", "machine learning", "deep learning", '#machinelearning', '#deeplearning', '#data', 'data'])
    

    /anaconda3/lib/python3.6/site-packages/tweepy/streaming.py in filter(self, follow, track, async, locations, stall_warnings, languages, encoding, filter_level)
        448         self.session.params = {'delimited': 'length'}
        449         self.host = 'stream.twitter.com'
    --> 450         self._start(async)
        451 
        452     def sitestream(self, follow, stall_warnings=False,


    /anaconda3/lib/python3.6/site-packages/tweepy/streaming.py in _start(self, async)
        362             self._thread.start()
        363         else:
    --> 364             self._run()
        365 
        366     def on_closed(self, resp):


    /anaconda3/lib/python3.6/site-packages/tweepy/streaming.py in _run(self)
        264                     self.snooze_time = self.snooze_time_step
        265                     self.listener.on_connect()
    --> 266                     self._read_loop(resp)
        267             except (Timeout, ssl.SSLError) as exc:
        268                 # This is still necessary, as a SSLError can actually be


    /anaconda3/lib/python3.6/site-packages/tweepy/streaming.py in _read_loop(self, resp)
        314             length = 0
        315             while not resp.raw.closed:
    --> 316                 line = buf.read_line().strip()
        317                 if not line:
        318                     self.listener.keep_alive()  # keep-alive new lines are expected


    /anaconda3/lib/python3.6/site-packages/tweepy/streaming.py in read_line(self, sep)
        179             else:
        180                 start = len(self._buffer)
    --> 181             self._buffer += self._stream.read(self._chunk_size)
        182         return six.b('')
        183 


    /anaconda3/lib/python3.6/site-packages/urllib3/response.py in read(self, amt, decode_content, cache_content)
        382             else:
        383                 cache_content = False
    --> 384                 data = self._fp.read(amt)
        385                 if amt != 0 and not data:  # Platform-specific: Buggy versions of Python.
        386                     # Close the connection when no data is returned


    /anaconda3/lib/python3.6/http/client.py in read(self, amt)
        447             # Amount is given, implement using readinto
        448             b = bytearray(amt)
    --> 449             n = self.readinto(b)
        450             return memoryview(b)[:n].tobytes()
        451         else:


    /anaconda3/lib/python3.6/http/client.py in readinto(self, b)
        481 
        482         if self.chunked:
    --> 483             return self._readinto_chunked(b)
        484 
        485         if self.length is not None:


    /anaconda3/lib/python3.6/http/client.py in _readinto_chunked(self, b)
        576         try:
        577             while True:
    --> 578                 chunk_left = self._get_chunk_left()
        579                 if chunk_left is None:
        580                     return total_bytes


    /anaconda3/lib/python3.6/http/client.py in _get_chunk_left(self)
        544                 self._safe_read(2)  # toss the CRLF at the end of the chunk
        545             try:
    --> 546                 chunk_left = self._read_next_chunk_size()
        547             except ValueError:
        548                 raise IncompleteRead(b'')


    /anaconda3/lib/python3.6/http/client.py in _read_next_chunk_size(self)
        504     def _read_next_chunk_size(self):
        505         # Read the next chunk size from the file
    --> 506         line = self.fp.readline(_MAXLINE + 1)
        507         if len(line) > _MAXLINE:
        508             raise LineTooLong("chunk size")


    /anaconda3/lib/python3.6/socket.py in readinto(self, b)
        584         while True:
        585             try:
    --> 586                 return self._sock.recv_into(b)
        587             except timeout:
        588                 self._timeout_occurred = True


    /anaconda3/lib/python3.6/site-packages/urllib3/contrib/pyopenssl.py in recv_into(self, *args, **kwargs)
        290                 raise
        291         except OpenSSL.SSL.WantReadError:
    --> 292             rd = util.wait_for_read(self.socket, self.socket.gettimeout())
        293             if not rd:
        294                 raise timeout('The read operation timed out')


    /anaconda3/lib/python3.6/site-packages/urllib3/util/wait.py in wait_for_read(socks, timeout)
         31     or optionally a single socket if passed in. Returns a list of
         32     sockets that can be read from immediately. """
    ---> 33     return _wait_for_io_events(socks, EVENT_READ, timeout)
         34 
         35 


    /anaconda3/lib/python3.6/site-packages/urllib3/util/wait.py in _wait_for_io_events(socks, events, timeout)
         24             selector.register(sock, events)
         25         return [key[0].fileobj for key in
    ---> 26                 selector.select(timeout) if key[1] & events]
         27 
         28 


    /anaconda3/lib/python3.6/site-packages/urllib3/util/selectors.py in select(self, timeout)
        511 
        512             kevent_list = _syscall_wrapper(self._kqueue.control, True,
    --> 513                                            None, max_events, timeout)
        514 
        515             for kevent in kevent_list:


    /anaconda3/lib/python3.6/site-packages/urllib3/util/selectors.py in _syscall_wrapper(func, _, *args, **kwargs)
         62         and recalculate their timeouts. """
         63         try:
    ---> 64             return func(*args, **kwargs)
         65         except (OSError, IOError, select.error) as e:
         66             errcode = None


    KeyboardInterrupt: 



```python
myStream.session.close()
```


```python
tweets_df = pd.DataFrame(dlist)
tweets_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contributors</th>
      <th>coordinates</th>
      <th>created_at</th>
      <th>display_text_range</th>
      <th>entities</th>
      <th>extended_entities</th>
      <th>extended_tweet</th>
      <th>favorite_count</th>
      <th>favorited</th>
      <th>filter_level</th>
      <th>...</th>
      <th>quoted_status_permalink</th>
      <th>reply_count</th>
      <th>retweet_count</th>
      <th>retweeted</th>
      <th>retweeted_status</th>
      <th>source</th>
      <th>text</th>
      <th>timestamp_ms</th>
      <th>truncated</th>
      <th>user</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>None</td>
      <td>None</td>
      <td>Tue May 28 16:53:43 +0000 2019</td>
      <td>NaN</td>
      <td>{'hashtags': [], 'urls': [], 'user_mentions': ...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>low</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>False</td>
      <td>{'created_at': 'Tue May 28 08:14:16 +0000 2019...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @GobKittipos: มธ เปิด ป.โท ออนไลน์มา 3 หลัก...</td>
      <td>1559062423616</td>
      <td>False</td>
      <td>{'id': 773701115040980993, 'id_str': '77370111...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>None</td>
      <td>None</td>
      <td>Tue May 28 16:53:47 +0000 2019</td>
      <td>NaN</td>
      <td>{'hashtags': [], 'urls': [{'url': 'https://t.c...</td>
      <td>NaN</td>
      <td>{'full_text': 'IIIT-Naya Raipur Announces Inte...</td>
      <td>0</td>
      <td>False</td>
      <td>low</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://into.AI" rel="nofollow"&gt;into.A...</td>
      <td>IIIT-Naya Raipur Announces Integrated https://...</td>
      <td>1559062427492</td>
      <td>True</td>
      <td>{'id': 3243551873, 'id_str': '3243551873', 'na...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>None</td>
      <td>None</td>
      <td>Tue May 28 16:53:47 +0000 2019</td>
      <td>NaN</td>
      <td>{'hashtags': [], 'urls': [], 'user_mentions': ...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>low</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>False</td>
      <td>{'created_at': 'Tue May 28 08:14:16 +0000 2019...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @GobKittipos: มธ เปิด ป.โท ออนไลน์มา 3 หลัก...</td>
      <td>1559062427988</td>
      <td>False</td>
      <td>{'id': 775954909, 'id_str': '775954909', 'name...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>None</td>
      <td>None</td>
      <td>Tue May 28 16:53:50 +0000 2019</td>
      <td>NaN</td>
      <td>{'hashtags': [], 'urls': [], 'user_mentions': ...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>low</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>False</td>
      <td>{'created_at': 'Tue May 28 08:14:16 +0000 2019...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @GobKittipos: มธ เปิด ป.โท ออนไลน์มา 3 หลัก...</td>
      <td>1559062430533</td>
      <td>False</td>
      <td>{'id': 735350071, 'id_str': '735350071', 'name...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>None</td>
      <td>None</td>
      <td>Tue May 28 16:53:52 +0000 2019</td>
      <td>NaN</td>
      <td>{'hashtags': [], 'urls': [], 'user_mentions': ...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>low</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>False</td>
      <td>{'created_at': 'Tue May 28 08:14:16 +0000 2019...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @GobKittipos: มธ เปิด ป.โท ออนไลน์มา 3 หลัก...</td>
      <td>1559062432367</td>
      <td>False</td>
      <td>{'id': 3167873611, 'id_str': '3167873611', 'na...</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 36 columns</p>
</div>




```python
entities_df = pd.DataFrame(list(tweets_df["entities"]))
entities_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>hashtags</th>
      <th>media</th>
      <th>symbols</th>
      <th>urls</th>
      <th>user_mentions</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[]</td>
      <td>NaN</td>
      <td>[]</td>
      <td>[]</td>
      <td>[{'screen_name': 'GobKittipos', 'name': 'GB', ...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[]</td>
      <td>NaN</td>
      <td>[]</td>
      <td>[{'url': 'https://t.co/NYcZFeUVMt', 'expanded_...</td>
      <td>[]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[]</td>
      <td>NaN</td>
      <td>[]</td>
      <td>[]</td>
      <td>[{'screen_name': 'GobKittipos', 'name': 'GB', ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[]</td>
      <td>NaN</td>
      <td>[]</td>
      <td>[]</td>
      <td>[{'screen_name': 'GobKittipos', 'name': 'GB', ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[]</td>
      <td>NaN</td>
      <td>[]</td>
      <td>[]</td>
      <td>[{'screen_name': 'GobKittipos', 'name': 'GB', ...</td>
    </tr>
  </tbody>
</table>
</div>




```python
user_df = pd.DataFrame(list(tweets_df["user"]))
user_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contributors_enabled</th>
      <th>created_at</th>
      <th>default_profile</th>
      <th>default_profile_image</th>
      <th>description</th>
      <th>favourites_count</th>
      <th>follow_request_sent</th>
      <th>followers_count</th>
      <th>following</th>
      <th>friends_count</th>
      <th>...</th>
      <th>profile_text_color</th>
      <th>profile_use_background_image</th>
      <th>protected</th>
      <th>screen_name</th>
      <th>statuses_count</th>
      <th>time_zone</th>
      <th>translator_type</th>
      <th>url</th>
      <th>utc_offset</th>
      <th>verified</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>False</td>
      <td>Thu Sep 08 01:54:55 +0000 2016</td>
      <td>True</td>
      <td>False</td>
      <td>None</td>
      <td>14558</td>
      <td>None</td>
      <td>36</td>
      <td>None</td>
      <td>166</td>
      <td>...</td>
      <td>333333</td>
      <td>True</td>
      <td>False</td>
      <td>evemtrd</td>
      <td>17694</td>
      <td>None</td>
      <td>none</td>
      <td>None</td>
      <td>None</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>False</td>
      <td>Sat May 09 15:19:08 +0000 2015</td>
      <td>False</td>
      <td>False</td>
      <td>get into AI. The AI Knowledge and Global Commu...</td>
      <td>808</td>
      <td>None</td>
      <td>18514</td>
      <td>None</td>
      <td>1743</td>
      <td>...</td>
      <td>333333</td>
      <td>True</td>
      <td>False</td>
      <td>into_AI</td>
      <td>403473</td>
      <td>None</td>
      <td>none</td>
      <td>http://get.into.AI</td>
      <td>None</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False</td>
      <td>Thu Aug 23 13:20:03 +0000 2012</td>
      <td>False</td>
      <td>False</td>
      <td>☆ I'M PRIMADONNA ☆\n♥ FT.ISLAND • BTS ♥\n》เพ้อ...</td>
      <td>2504</td>
      <td>None</td>
      <td>926</td>
      <td>None</td>
      <td>199</td>
      <td>...</td>
      <td>333333</td>
      <td>True</td>
      <td>False</td>
      <td>PoPpY_PriTH</td>
      <td>79793</td>
      <td>None</td>
      <td>none</td>
      <td>https://www.facebook.com/#!/popchana</td>
      <td>None</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>False</td>
      <td>Fri Aug 03 18:44:47 +0000 2012</td>
      <td>False</td>
      <td>False</td>
      <td>None</td>
      <td>14973</td>
      <td>None</td>
      <td>345</td>
      <td>None</td>
      <td>175</td>
      <td>...</td>
      <td>000000</td>
      <td>True</td>
      <td>False</td>
      <td>narathawong</td>
      <td>83278</td>
      <td>None</td>
      <td>none</td>
      <td>None</td>
      <td>None</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>False</td>
      <td>Wed Apr 22 19:41:50 +0000 2015</td>
      <td>False</td>
      <td>False</td>
      <td>#ChanHun 🐯🐣| อี้ชิงและคุณหมี 🐑🐻 | โดแทเตนล์ 🐰🐱...</td>
      <td>8989</td>
      <td>None</td>
      <td>225</td>
      <td>None</td>
      <td>237</td>
      <td>...</td>
      <td>333333</td>
      <td>True</td>
      <td>False</td>
      <td>ltrabbied</td>
      <td>110051</td>
      <td>None</td>
      <td>none</td>
      <td>None</td>
      <td>None</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 39 columns</p>
</div>




```python
tweets_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contributors</th>
      <th>coordinates</th>
      <th>created_at</th>
      <th>display_text_range</th>
      <th>entities</th>
      <th>extended_entities</th>
      <th>extended_tweet</th>
      <th>favorite_count</th>
      <th>favorited</th>
      <th>filter_level</th>
      <th>...</th>
      <th>quoted_status_permalink</th>
      <th>reply_count</th>
      <th>retweet_count</th>
      <th>retweeted</th>
      <th>retweeted_status</th>
      <th>source</th>
      <th>text</th>
      <th>timestamp_ms</th>
      <th>truncated</th>
      <th>user</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>None</td>
      <td>None</td>
      <td>Tue May 28 16:53:43 +0000 2019</td>
      <td>NaN</td>
      <td>{'hashtags': [], 'urls': [], 'user_mentions': ...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>low</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>False</td>
      <td>{'created_at': 'Tue May 28 08:14:16 +0000 2019...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @GobKittipos: มธ เปิด ป.โท ออนไลน์มา 3 หลัก...</td>
      <td>1559062423616</td>
      <td>False</td>
      <td>{'id': 773701115040980993, 'id_str': '77370111...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>None</td>
      <td>None</td>
      <td>Tue May 28 16:53:47 +0000 2019</td>
      <td>NaN</td>
      <td>{'hashtags': [], 'urls': [{'url': 'https://t.c...</td>
      <td>NaN</td>
      <td>{'full_text': 'IIIT-Naya Raipur Announces Inte...</td>
      <td>0</td>
      <td>False</td>
      <td>low</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>False</td>
      <td>NaN</td>
      <td>&lt;a href="http://into.AI" rel="nofollow"&gt;into.A...</td>
      <td>IIIT-Naya Raipur Announces Integrated https://...</td>
      <td>1559062427492</td>
      <td>True</td>
      <td>{'id': 3243551873, 'id_str': '3243551873', 'na...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>None</td>
      <td>None</td>
      <td>Tue May 28 16:53:47 +0000 2019</td>
      <td>NaN</td>
      <td>{'hashtags': [], 'urls': [], 'user_mentions': ...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>low</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>False</td>
      <td>{'created_at': 'Tue May 28 08:14:16 +0000 2019...</td>
      <td>&lt;a href="http://twitter.com/download/android" ...</td>
      <td>RT @GobKittipos: มธ เปิด ป.โท ออนไลน์มา 3 หลัก...</td>
      <td>1559062427988</td>
      <td>False</td>
      <td>{'id': 775954909, 'id_str': '775954909', 'name...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>None</td>
      <td>None</td>
      <td>Tue May 28 16:53:50 +0000 2019</td>
      <td>NaN</td>
      <td>{'hashtags': [], 'urls': [], 'user_mentions': ...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>low</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>False</td>
      <td>{'created_at': 'Tue May 28 08:14:16 +0000 2019...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @GobKittipos: มธ เปิด ป.โท ออนไลน์มา 3 หลัก...</td>
      <td>1559062430533</td>
      <td>False</td>
      <td>{'id': 735350071, 'id_str': '735350071', 'name...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>None</td>
      <td>None</td>
      <td>Tue May 28 16:53:52 +0000 2019</td>
      <td>NaN</td>
      <td>{'hashtags': [], 'urls': [], 'user_mentions': ...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0</td>
      <td>False</td>
      <td>low</td>
      <td>...</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>False</td>
      <td>{'created_at': 'Tue May 28 08:14:16 +0000 2019...</td>
      <td>&lt;a href="http://twitter.com/download/iphone" r...</td>
      <td>RT @GobKittipos: มธ เปิด ป.โท ออนไลน์มา 3 หลัก...</td>
      <td>1559062432367</td>
      <td>False</td>
      <td>{'id': 3167873611, 'id_str': '3167873611', 'na...</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 36 columns</p>
</div>




```python
tweets_df.to_csv("~/tweets.csv")
```

### Assessment
