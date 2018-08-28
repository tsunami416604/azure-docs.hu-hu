---
title: Az Apache Hive – Azure HDInsight Twitter-adatok elemzése
description: Megtudhatja, hogyan használható a Hive és a Hadoop HDInsight, nyers TWitter-adatok átalakítása a kereshető Hive-táblába.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: a20f9ef6e42027cf3f499654ac8a43eee7b41854
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43104078"
---
# <a name="analyze-twitter-data-using-hive-and-hadoop-on-hdinsight"></a>A HDInsight Hive és a Hadoop használatával Twitter-adatok elemzése

Ismerje meg, hogyan használhatja az Apache Hive folyamat Twitter-adatok. Ez a legtöbb tweeteket egy bizonyos szót tartalmazó küldő Twitter-felhasználók listája.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések tesztelt, a HDInsight 3.6-ot.
>
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="get-the-data"></a>Az adatok lekérése

Twitter lehetővé teszi, hogy az egyes tweetek adatainak betöltése a JavaScript Object Notation (JSON) dokumentumban REST API-n keresztül. [OAuth](http://oauth.net) az API-hitelesítés szükséges.

### <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

1. Egy webböngészőben, jelentkezzen be a [ https://apps.twitter.com/ ](https://apps.twitter.com/). Kattintson a **most regisztrál** hivatkozásra, ha nem rendelkezik egy Twitter-fiókkal.

2. Kattintson a **új alkalmazás létrehozása**.

3. Adja meg **neve**, **leírás**, **webhely**. Meghatározhat egy URL-címe be a **webhely** mező. Az alábbi táblázat néhány mintaértékeket használhatja:

   | Mező | Érték |
   |:--- |:--- |
   | Name (Név) |MyHDInsightApp |
   | Leírás |MyHDInsightApp |
   | Honlap |http://www.myhdinsightapp.com |

4. Ellenőrizze **Igen, elfogadom**, és kattintson a **Twitter-alkalmazás létrehozása**.

5. Kattintson a **engedélyek** fülre. Az alapértelmezett engedély **csak olvasható**.

6. Kattintson a **kulcsok és hozzáférési tokenek** fülre.

7. Kattintson a **saját hozzáférési token létrehozása**.

8. Kattintson a **teszt OAuth** az oldal jobb felső sarkában.

9. Írja le **fogyasztói kulcs**, **fogyasztói titkos kulcs**, **hozzáférési jogkivonat**, és **hozzáférési token titkos**.

### <a name="download-tweets"></a>Töltse le a Twitter-üzenetek

A következő Python-kód letölti 10 000 tweetek twitterről, és mentse őket egy fájlt **tweets.txt**.

> [!NOTE]
> Az alábbi lépéseket a HDInsight-fürtön történik, mivel a Python már telepítve van.

1. Csatlakozzon SSH-val a HDInsight-fürthöz:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Telepítse az alábbi parancsokkal [Tweepy](http://www.tweepy.org/), [folyamatjelző](https://pypi.python.org/pypi/progressbar/2.2), és a többi szükséges csomagot:

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

4. A következő paranccsal hozzon létre egy fájlt **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

5. Használja a következő szöveget a tartalmát, a **gettweets.py** fájlt:

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
    > Cserélje le a helyőrző szöveg a következő elemek twitter-alkalmazás adatait:
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

    > [!TIP]
    > A témakörök szűrő népszerű kulcsszavak nyomon követéséhez az utolsó sorban állítsa be. Népszerű kulcsszavakat, a parancsfájl futtatása során lehetővé teszi az adatok gyorsabb rögzítési.

6. Használat **Ctrl + X**, majd **Y** szeretné menteni a fájlt.

7. Az alábbi parancs segítségével futtassa a fájlt, és töltse le a Twitter-üzenetek:

    ```bash
    python gettweets.py
    ```

    Egy folyamatjelző jelenik meg. 100 %-a tweetek letöltöttként számolja.

   > [!NOTE]
   > Azt mutatja be, a folyamatjelző sáv kell hosszú ideig tart a vártnál, ha módosítania kell a szűrő Népszerű témakörök nyomon követéséhez. Ha a témakör a szűrő számos tweetekről, a szükséges 10000 tweeteket gyorsan kérheti le.

### <a name="upload-the-data"></a>Az adatok feltöltése

Az adatok feltöltése a HDInsight storage, a következő parancsokat használja:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Ezeket a parancsokat a fürt összes csomópontja által elérhető helyen tárolja az adatokat.

## <a name="run-the-hiveql-job"></a>A HiveQL-feladat futtatása

1. A következő paranccsal hozzon létre egy fájlt, amely a HiveQL utasításokat tartalmazza:

   ```bash
   nano twitter.hql
   ```

    Használja a fájl tartalmát a következő szöveget:

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

2. Nyomja le az **Ctrl + X**, majd nyomja le az **Y** szeretné menteni a fájlt.
3. Az alábbi parancs segítségével futtassa a HiveQL a fájlban található:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Ez a parancs futtatható a **twitter.hql** fájlt. Ha a lekérdezés befejeződött, megjelenik egy `jdbc:hive2//localhost:10001/>` parancssort.

4. A beeline használatával a következő lekérdezés használatával győződjön meg arról, hogy az adatok lettek importálva:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    A lekérdezés visszaad egy legfeljebb 10 szót tartalmazó tweeteket **Azure** az az üzenet szövege.

    > [!NOTE]
    > Ha módosította a szűrőt a `gettweets.py` parancsfájl, cserélje le **Azure** -egy szűrőt használt.

## <a name="next-steps"></a>További lépések

Megtanulhatta, hogyan alakíthat át egy strukturálatlan JSON adatkészletek strukturált Hive-táblába való. A HDInsight Hive kapcsolatos további információkért tekintse meg a következő dokumentumokat:

* [HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight használatával repülőjáratok késési adatainak elemzése](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
