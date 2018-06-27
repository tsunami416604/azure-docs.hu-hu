---
title: Az Apache Hive - Azure HDInsight Twitter-adatok elemzése |} Microsoft Docs
description: Megtudhatja, hogyan használja a use Hive és a hdinsight Hadoop nyers TWitter-adatok átalakítása kereshető Hive táblákat.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1e249ed-5f57-40d6-b3bc-a1b4d9a871d3
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f48aa68838ff8cac0119f66f168fce6008d388bc
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36959022"
---
# <a name="analyze-twitter-data-using-hive-and-hadoop-on-hdinsight"></a>A HDInsight Hive és a Hadoop használatával Twitter-adatok elemzése

Útmutató Apache Hive segítségével folyamat Twitter-adatok. Twitter-felhasználók, akik a legtöbb Twitter-üzeneteket, amelyek tartalmaznak egy adott szó küldött listája eredménye.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések a HDInsight 3.6 teszteltük.
>
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="get-the-data"></a>Az adatok lekérése

Twitter lehetővé teszi az egyes tweetet adatok lekéréséhez a JavaScript Object Notation (JSON)-dokumentumként egy REST API-n keresztül. [OAuth](http://oauth.net) az API-hitelesítés szükséges.

### <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

1. Egy webböngészőből, jelentkezzen be [ https://apps.twitter.com/ ](https://apps.twitter.com/). Kattintson a **előfizetési most** hivatkozásra, ha egy Twitter-fiók nem rendelkezik.

2. Kattintson a **új alkalmazás létrehozása**.

3. Adja meg **neve**, **leírás**, **webhely**. Hogy fel egy URL-címet a **webhely** mező. A következő táblázatban néhány példa értékeket:

   | Mező | Érték |
   |:--- |:--- |
   | Name (Név) |MyHDInsightApp |
   | Leírás |MyHDInsightApp |
   | Honlap |http://www.myhdinsightapp.com |

4. Ellenőrizze **Igen, elfogadom**, és kattintson a **az Twitter-alkalmazás létrehozása**.

5. Kattintson a **engedélyek** fülre. Az alapértelmezett engedély **csak olvasható**.

6. Kattintson a **kulcsok és a hozzáférési jogkivonatok** fülre.

7. Kattintson a **a hozzáférési jogkivonat létrehozása**.

8. Kattintson a **teszt OAuth** a lap jobb felső sarkában.

9. Írja le **kulcsa**, **felhasználói titok**, **hozzáférési jogkivonat**, és **Access token titkos**.

### <a name="download-tweets"></a>Töltse le a Twitter-üzenetek

A következő Python kódját letölti a 10 000 Twitter-üzeneteket Twitter, és mentse őket nevű fájl **tweets.txt**.

> [!NOTE]
> Az alábbi lépéseket kell végrehajtani a HDInsight-fürt, óta Python már telepítve van.

1. Csatlakozzon SSH-val a HDInsight-fürthöz:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Az alábbi parancsokkal telepítéséhez [Tweepy](http://www.tweepy.org/), [Progressbar](https://pypi.python.org/pypi/progressbar/2.2), és más szükséges csomagokat:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

4. Az alábbi parancs segítségével hozzon létre egy fájlt **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

5. Használja a következő szöveget a tartalmát a **gettweets.py** fájlt:

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=10000

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!IMPORTANT]
    > Cserélje le a helyőrzőket a következő elemek a twitter-alkalmazás adatait:
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

    > [!TIP]
    > Állítsa be úgy a témakörök szűrő nyomon követéséhez a népszerű kulcsszavak utolsó sorában. Népszerű kulcsszavak használatával futtassa a parancsfájlt időpontjában lehetővé teszi, hogy az adatok gyorsabb rögzítési.

6. Használjon **Ctrl + X**, majd **Y** fájl mentéséhez.

7. Az alábbi parancs segítségével futtassa a fájlt, és töltse le a Twitter-üzeneteket:

    ```bash
    python gettweets.py
    ```

    Egy folyamatjelző jelenik meg. A Twitter-üzeneteket letöltöttként megszámlálja akár 100 %.

   > [!NOTE]
   > Ha a folyamatjelző ahhoz, hogy hosszú ideig tart, módosítania kell a szűrő nyomon követéséhez trendekkel kapcsolatos témakörök. Ha sok Twitter-üzeneteket a témakör a szűrőben lévő, gyorsan megjeleníthet a szükséges 10000 Twitter-üzeneteket.

### <a name="upload-the-data"></a>Az adatok feltöltése

Az adatok feltöltése a HDInsight-tárolóba, használja a következő parancsokat:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Ezek a parancsok a fürt összes csomópontja által elérhető helyen tárolja az adatokat.

## <a name="run-the-hiveql-job"></a>A HiveQL feladat futtatása

1. Az alábbi parancs segítségével hozzon létre egy HiveQL utasításokat tartalmazó fájlt:

   ```bash
   nano twitter.hql
   ```

    A fájl tartalmát a következő szöveg használata:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

2. Nyomja le az ENTER **Ctrl + X**, majd nyomja le az **Y** fájl mentéséhez.
3. A következő paranccsal futtassa a HiveQL a fájlban található:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Ez a parancs futtatható a a **twitter.hql** fájlt. Miután a lekérdezés befejeződött, megjelenik egy `jdbc:hive2//localhost:10001/>` kérdés.

4. Beeline parancssorába a következő lekérdezés segítségével győződjön meg arról, hogy adatok importálása:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    A lekérdezés által visszaadott legfeljebb 10 Twitter-üzeneteket, amelyek tartalmazzák a word **Azure** az az üzenet szövege.

    > [!NOTE]
    > Ha módosította a szűrőt a `gettweets.py` parancsfájlt, hogy lecseréli **Azure** egy szűrőt használja.

## <a name="next-steps"></a>További lépések

Rendelkezik megtudta, hogyan egy strukturálatlan JSON adatkészlet átalakítása strukturált Hive táblákat. A HDInsight Hive kapcsolatos további tudnivalókért tekintse meg a következő dokumentumokat:

* [Első lépései a hdinsight eszközzel](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight eszközzel repülési késleltetés adatok elemzése](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
