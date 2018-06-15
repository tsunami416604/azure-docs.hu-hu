---
title: 'Oktatóanyag: Azure Event hubs az Apache Spark on Azure HDInsight adatok feldolgozása |} Microsoft Docs'
description: Apache Spark on Azure HDInsight csatlakozni az Azure Event Hubs és a streamelési adatok feldolgozása.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 9b59f5d58234aaf8f8385f722d6659548e066933
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787254"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>Oktatóanyag: Folyamat Twitter-üzeneteket Azure Event Hubs és a Spark on hdinsight használatával

Ebben az oktatóanyagban elsajátíthatja, hogyan hozzon létre egy Apache Spark streamelési alkalmazás Twitter-üzeneteket küld egy Azure event hubs eseményközpontot, és a Twitter-üzeneteket beolvasni az event hubs egy másik alkalmazás létrehozásához. A Spark streamelési részletes ismertetése, [Apache Spark streaming áttekintése](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight biztosítható a az Azure Spark-fürt azonos adatfolyam-továbbítási funkciókat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Üzenetek küldése az Azure Event Hubs
> * Az Azure Event Hubs üzenetek olvasása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* **Fejezze be a cikk [oktatóanyag: adatok betöltése és lekérdezéseket futtathat a Azure HDInsight az Apache Spark-fürt](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

A valós idejű tweetstream fogadásához létre kell hoznia egy alkalmazást a Twitteren. Kövesse az utasításokat egy Twitter-alkalmazás létrehozása, és írja le az értékeket, hogy szeretné-e az oktatóanyag elvégzéséhez.

1. Keresse meg a [Alkalmazáskezelés Twitter](https://apps.twitter.com/).
2. Válassza ki **új alkalmazás létrehozása**.
3. Adja meg a következő értékeket:

    - Name: Adja meg az alkalmazás nevét. Ebben az oktatóanyagban használt értéke **HDISparkStreamApp0423**. Ez a név nem lehet egy egyedi nevet.
    - Leírás: Adja meg az alkalmazás rövid leírását. Ebben az oktatóanyagban használt értéke **egy egyszerű HDInsight Spark streaming alkalmazás**.
    - Webhely: Adja meg az alkalmazás webhelyet. Nem kell lennie egy érvényes webhely.  Ebben az oktatóanyagban használt értéke **http://www.contoso.com**.
    - A visszahívási URL-címe: akkor üresen is hagyhatja.

4. Válassza ki **Igen, elolvastam és fogadja el a Twitter fejlesztői szerződés**, majd válassza ki **az Twitter-alkalmazás létrehozása**.
5. Válassza ki a **kulcsok és a hozzáférési jogkivonatok** fülre.
6. Válassza ki **a hozzáférési jogkivonat létrehozása** a lap végén.
7. Jegyezze fel a következő értékeket a lapról.  Ezeket az értékeket az oktatóanyag későbbi részében szüksége:

    - **(API-kulcs) kulcsa**    
    - **Felhasználói titok (API titkos kulcs)**  
    - **Hozzáférési jogkivonat**
    - **Hozzáférési jogkivonat titkos kulcs**   

## <a name="create-an-azure-event-hub"></a>Azure Event Hub létrehozása

Az event hubs használatával Twitter-üzenetek tárolásához.

1. Jelentkezzen be az [Azure Portal](https://ms.portal.azure.com).
2. Válassza ki **hozzon létre egy erőforrást** , a képernyő bal felső.
3. Válassza ki **az eszközök internetes hálózatát**, majd jelölje be **Event Hubs**.

    ![Spark streaming példa eseményközpont létrehozása](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Spark streamelési példa eseményközpont létrehozása")
4. Adja meg az új event hub névtér a következő értékeket:

    - **Név**: Adjon meg egy nevet az eseményközpontba.  Ebben az oktatóanyagban használt értéke **myeventhubns20180403**.
    - **Árlista réteg**: válasszon **szabványos**.
    - **Erőforráscsoport**: hozzon létre egy új, vagy válassza ki az erőforráscsoportot, a Spark-fürt esetében lehetőség van. 
    - **Hely**: a azonos kiválasztása **hely** , az Apache Spark-fürt hdinsightban késés és a költségek csökkentése érdekében.

    ![Adjon meg a Spark streamelési példa event hub nevet](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "adja meg az eseményközpont neveként a Spark streamelési – példa")
5. Válassza ki **létrehozása** a névtér létrehozása.

6. Nyissa meg az esemény hub névtér a következő útmutatás szerint:

    1. Válassza ki a portál **minden szolgáltatás**.
    2. A Szűrő mezőbe írja be a **az event hubs**.
    3. Kattintson duplán a létrehozott névtér.
    4. Válassza ki **+ Eseményközpont**.

6. Az Event Hubs névtér listában válassza ki az újonnan létrehozott névtér.      
5. Válassza ki **Event Hubs**, majd válassza ki **+ Eseményközpont** egy új Eseményközpont létrehozásához.
  

6. Írja be a következő értékeket:

    - Name: Adjon nevet az Eseményközpont.
    - Count partícióazonosító: 10
    - Üzenet-megőrzési: 1. 
   
    ![Adja meg a Spark streaming példa event hub-adatok](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Spark streamelési példa event hub-adatok megadása")

7. Kattintson a **Létrehozás** gombra.
8. Válassza ki **megosztott elérési házirendek** a névtér (vegye figyelembe, hogy nem a event hub megosztott hozzáférési házirendek), és válassza a **RootManageSharedAccessKey**.
    
     ![Az Event Hubs házirendjeinek beállítása a Spark streaming példa](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "állítsa be az Event Hubs házirendeket a Spark streaming – példa")

9. Mentés értékének **elsődleges kulcs** és **kapcsolati karakterlánc elsődleges kulcs** használatára az oktatóanyag későbbi részében.

     ![Az Event Hubs házirend kulcsok megtekintéséhez a Spark streaming példa](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "a Spark streamelési például kulcsok megtekintése az Event Hubs házirend")


## <a name="send-tweets-to-the-event-hub"></a>Twitter-üzenetek küldése az event hubs

A Jupyter notebook létrehozása, és adjon neki nevet kell **SendTweetsToEventHub**. 

1. Futtassa a következő kódot a külső Maven-kódtárak hozzáadása:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Futtassa a következő kódot a Twitter-üzenetek küldése az eseményközpont:

    ```
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Nyissa meg az event hubs az Azure portálon.  A **áttekintése**, ekkor megjelenik az egyes üzeneteket az eseményközpontba bemutató diagramok.

## <a name="read-tweets-from-the-event-hub"></a>Az eseményközpontból olvasási Twitter-üzenetek

Egy másik Jupyter notebook létrehozása, és adjon neki nevet kell **ReadTweetsFromEventHub**. 

1. Futtassa a következő kódot a Maven külső osztálykönyvtárak hozzáadásához:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Futtassa az eseményközpont Twitter-üzeneteket beolvasni a következő kódot:

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connectionString).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A hdinsight eszközzel az adatok tárolja az Azure Storage vagy az Azure Data Lake Store, így biztonságosan törölhető egy fürt amikor nincs használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. Ha közvetlenül elérje a következő oktatóanyaga azt tervezi, akkor előfordulhat, hogy szeretné megőrizni a fürt.

Nyissa meg a fürt az Azure portálon, és válassza ki **törlése**.

![HDInsight-fürt törlése](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "törlése HDInsight-fürt")

Jelölje ki az erőforráscsoport neve erőforrás csoport lapjának megnyitásához, és válassza **erőforrás csoport törlése**. Az erőforráscsoport törlésével törli a HDInsight Spark-fürt és az alapértelmezett tárfiók.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Az eseményközpontban lévő üzenet olvasása.
Előzetes megtekintéséhez a machine learning-alkalmazást hozhat létre a következő cikket. 

> [!div class="nextstepaction"]
> [Machine learning-alkalmazás létrehozása](./apache-spark-ipython-notebook-machine-learning.md)


