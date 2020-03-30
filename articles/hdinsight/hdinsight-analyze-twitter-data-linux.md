---
title: Twitter-adatok elemzése az Apache Hive segítségével – Azure HDInsight
description: Ismerje meg, hogyan használhatja az Apache Hive és az Apache Hadoop a HDInsight-on a nyers TWitter-adatok kereshető Hive-táblává történő átalakítását.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: f3705170be28f33e5994bd00e363dc7ec7f94642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435616"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>A Twitter-adatok elemzése Apache Hive és Apache Hadoop használatával a HDInsighton

Ismerje meg, hogyan használhatja az [Apache Hive](https://hive.apache.org/) segítségével a Twitter-adatok feldolgozását. Az eredmény egy lista a Twitter felhasználók, akik küldték a legtöbb tweets tartalmazó egy bizonyos szót.

> [!IMPORTANT]  
> A jelen dokumentum lépéseit a HDInsight 3.6-os teszten teszteltük.

## <a name="get-the-data"></a>Az adatok lekérése

Twitter lehetővé teszi, hogy letölteni az adatokat minden csipog, mint a JavaScript Object Notation (JSON) dokumentum egy REST API-t. [OAuth](https://oauth.net) szükséges az API-hitelesítéshez.

### <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

1. Webböngészőből jelentkezzen be [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/)a alkalmazásba. Válassza a **Regisztráció most** hivatkozást, ha nincs Twitter-fiókja.

2. Válassza **az Új alkalmazás létrehozása**lehetőséget.

3. Írja be **a nevet**, **a leírás**, **a webhelyet**. A Webhely mező URL-címét is **létrehozhatja.** Az alábbi táblázat a használandó mintaértékeket mutatja be:

   | Mező | Érték |
   |--- |--- |
   | Név |SajátHDInsightApp |
   | Leírás |SajátHDInsightApp |
   | Webhely |`https://www.myhdinsightapp.com` |

4. Válassza az **Igen, egyetértek**, majd **válassza a Twitter alkalmazás létrehozása**lehetőséget.

5. Válassza az **Engedélyek** lapot. Az alapértelmezett engedély az **Írás védett ség**.

6. Válassza a **Kulcsok és hozzáférési jogkivonatok** lapot.

7. Válassza **a Hozzáférési jogkivonat létrehozása lehetőséget.**

8. Válassza az Oldal jobb felső sarkában válassza az **OAuth** tesztelése lehetőséget.

9. Írja le **a fogyasztói kulcsot**, a fogyasztói titkos **kulcsot**, a **hozzáférési jogkivonatot**és **az Access jogkivonat titkos kulcsát.**

### <a name="download-tweets"></a>Letöltés tweets

A következő Python-kód 10 000 tweetet tölt le a Twitterről, és egy **tweets.txt**nevű fájlba menti őket.

> [!NOTE]  
> A következő lépéseket a HDInsight-fürtön hajtják végre, mivel a Python már telepítve van.

1. Az [ssh paranccsal](./hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A [Tweepy,](https://www.tweepy.org/) [a Folyamatjelző sáv](https://pypi.python.org/pypi/progressbar/2.2)és más szükséges csomagok telepítéséhez használja az alábbi parancsokat:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. A következő paranccsal **hozzon**létre egy gettweets.py nevű fájlt:

   ```bash
   nano gettweets.py
   ```

1. Az alábbi kódot a `Your consumer secret` `Your consumer key`, `Your access token`, `Your access token secret` és a Twitter alkalmazásból származó releváns információk kal szerkesztheti. Ezután illessze be a szerkesztett kódot a **gettweets.py** fájl tartalmaként.

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
   max_tweets=100

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

    > [!TIP]  
    > Állítsa be a témakörszűrőt az utolsó sorban a népszerű kulcsszavak nyomon követéséhez. Kulcsszavak használata népszerű idején futtatja a szkript lehetővé teszi a gyorsabb adatok rögzítését.

1. A fájl mentéséhez használja a **Ctrl + X**billentyűt, majd az **Y** billentyűt.

1. A fájl futtatásához és a tweetek letöltéséhez használja a következő parancsot:

    ```bash
    python gettweets.py
    ```

    Megjelenik egy folyamatjelző. Ez számít akár 100%, mint a tweets letölthető.

   > [!NOTE]  
   > Ha a folyamatjelző sáv előrejutása hosszú időt vesz igénybe, módosítsa a szűrőt a felkapott témakörök nyomon követésére. Ha sok tweets a témáról a szűrőt, akkor gyorsan kap a 100 tweets szükséges.

### <a name="upload-the-data"></a>Az adatok feltöltése

Az adatok HDInsight-tárolóba való feltöltéséhez használja a következő parancsokat:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Ezek a parancsok olyan helyen tárolják az adatokat, amelyhez a fürt összes csomópontja hozzáférhet.

## <a name="run-the-hiveql-job"></a>A HiveQL feladat futtatása

1. A következő paranccsal hozzon létre egy [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) utasítást tartalmazó fájlt:

   ```bash
   nano twitter.hql
   ```

    A fájl tartalmaként a következő szöveget használja:

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

1. Nyomja **le a Ctrl + X**billentyűkombinációt, majd az **Y** billentyűt a fájl mentéséhez.

1. A fájlban található HiveQL parancs futtatásához használja a következő parancsot:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Ez a parancs a **twitter.hql fájlt futtatja.** A lekérdezés befejezése után megjelenik egy `jdbc:hive2//localhost:10001/>` kérdés.

1. A beeline promptból a következő lekérdezéssel ellenőrizze az adatok importálását:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Ez a lekérdezés legfeljebb 10 tweetet ad vissza, amelyek tartalmazzák az **Azure** szót az üzenet szövegében.

    > [!NOTE]  
    > Ha módosította a szűrőt a `gettweets.py` parancsfájlban, cserélje le az **Azure-t** a használt szűrők egyikére.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan alakíthatja át a strukturálatlan JSON-adatkészletet strukturált [Apache Hive-táblává.](https://hive.apache.org/) Ha többet szeretne megtudni a HIVe-ról a HDInsight-on, olvassa el az alábbi dokumentumokat:

* [Ismerkedés a HDInsight szolgáltatással](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Járatkésleltetési adatok elemzése a HDInsight segítségével](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
