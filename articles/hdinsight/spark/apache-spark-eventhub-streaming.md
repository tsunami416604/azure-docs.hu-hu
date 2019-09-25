---
title: 'Oktatóanyag: Adatok feldolgozása az Azure Event Hubs és az Apache Spark a HDInsight'
description: Oktatóanyag – Apache Spark összekapcsolását az Azure HDInsight az Azure-ban Event Hubs és feldolgozza a folyamatos átviteli adatátvitelt.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: be21b809272a132ee6e63582036c36ad5dcdf4ad
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266198"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Oktatóanyag: Tweetek feldolgozása az Azure Event Hubs és a HDInsight-Apache Spark használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy [Apache Spark](https://spark.apache.org/) streaming-alkalmazást, hogy tweeteket küldjön egy Azure Event hub-ba, és hozzon létre egy másik alkalmazást, hogy beolvassa a tweeteket az Event hub-ból. A Spark streaming részletes ismertetését lásd: [Apache Spark streaming áttekintése](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). A HDInsight ugyanazokat a folyamatos átviteli funkciókat nyújtja az Azure-beli Spark-fürtön.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Üzenetek küldése az Azure Event hub-nak
> * Üzenetek olvasása az Azure Event hub-ból

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. Lásd: [Apache Spark-fürt létrehozása](./apache-spark-jupyter-spark-sql-use-portal.md).

* A Jupyter-notebookok és a HDInsighton futó Spark használatának ismerete. További információ: [adatok betöltése és lekérdezések futtatása Apache Spark a HDInsight-on](./apache-spark-load-data-run-query.md).

* Egy [Twitter-fiók](https://twitter.com/i/flow/signup).

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

A valós idejű tweetstream fogadásához létre kell hoznia egy alkalmazást a Twitteren. Kövesse a Twitter-alkalmazás létrehozása című témakört, és jegyezze fel az oktatóanyag elvégzéséhez szükséges értékeket.

1. Tallózással keresse meg a [Twitter-alkalmazások kezelését](https://apps.twitter.com/).

1. Válassza az **új alkalmazás létrehozása**lehetőséget.

1. Adja meg a következő értékeket:

    |Tulajdonság |Value |
    |---|---|
    |Name (Név)|Adja meg az alkalmazás nevét. Az oktatóanyaghoz használt érték a **HDISparkStreamApp0423**. A névnek egyedi névnek kell lennie.|
    |Leírás|Adja meg az alkalmazás rövid leírását. Az oktatóanyaghoz használt érték **egy egyszerű HDInsight Spark streaming-alkalmazás**.|
    |Webhely|Adja meg az alkalmazás webhelyét. Nem kell érvényes webhelynek lennie.  Az oktatóanyaghoz `http://www.contoso.com`használt érték.|
    |Visszahívási URL|Üresen hagyhatja.|

1. Válassza **az igen, elolvastam és elfogadom a Twitter fejlesztői szerződést**, majd válassza a **Twitter-alkalmazás létrehozása**lehetőséget.

1. Válassza a **kulcsok és hozzáférési tokenek** fület.

1. A lap végén válassza a **saját hozzáférési jogkivonat létrehozása** elemet.

1. Jegyezze fel a következő értékeket az oldalról.  Ezekre az értékekre később szüksége lesz az oktatóanyagban:

    - **Fogyasztói kulcs (API-kulcs)**    
    - **Fogyasztói titok (API Secret)**  
    - **Hozzáférési jogkivonat**
    - **Hozzáférési jogkivonat titka**   

## <a name="create-an-azure-event-hubs-namespace"></a>Azure Event Hubs-névtér létrehozása

Ezt az Event hub-t használja a tweetek tárolásához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. A bal oldali menüben válassza a **minden szolgáltatás**lehetőséget.  

3. **A dolgok internete**területen válassza a **Event Hubs**lehetőséget. 

    ![Event hub létrehozása Spark streaming] -példaként (./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Event hub létrehozása Spark streaming") -példaként

4. Válassza a **+ Hozzáadás** lehetőséget.

5. Adja meg a következő értékeket az új Event Hubs névtérhez:

    |Tulajdonság |Value |
    |---|---|
    |Name (Név)|Adja meg az Event hub nevét.  Az oktatóanyaghoz használt érték a **myeventhubns20180403**.|
    |Tarifacsomag|Válassza a **standard**lehetőséget.|
    |Subscription|Válassza ki a megfelelő előfizetést.|
    |Resource group|Válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása** lehetőséget egy új erőforráscsoport létrehozásához.|
    |Location|A késés és a költségek csökkentése érdekében válassza ki ugyanazt a **helyet** , mint a Apache Spark-fürtöt a HDInsight.|
    |Automatikus felfújás engedélyezése (nem kötelező) |Az automatikus feltöltés automatikusan méretezi a Event Hubs-névtérhez rendelt átviteli egységek számát, ha a forgalom túllépi a hozzá rendelt átviteli egységek kapacitását.  |
    |Maximális átviteli egységek automatikus felfújása (nem kötelező)|Ez a csúszka csak akkor jelenik meg, ha bejelöli az **automatikus feltöltés engedélyezése**.  |

    ![Adjon meg egy Event hub-nevet a Spark streaming példához](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Adjon meg egy Event hub-nevet a Spark streaming példához")

6. A névtér létrehozásához válassza a **Létrehozás** elemet.  Az üzembe helyezés néhány percen belül befejeződik.

## <a name="create-an-azure-event-hub"></a>Azure Event hub létrehozása
Hozzon létre egy Event hubot az Event Hubs névtér üzembe helyezése után.  A portálról:

1. A bal oldali menüben válassza a **minden szolgáltatás**lehetőséget.  

1. **A dolgok internete**területen válassza a **Event Hubs**lehetőséget.  

1. Válassza ki a Event Hubs névteret a listából.  

1. A **Event Hubs névtér** lapon válassza a **+ Event hub**elemet.  
1. Adja meg a következő értékeket az **Event hub létrehozása** lapon:

    - **Név**: Adja meg az Event hub nevét. 
 
    - **Partíciók száma**: 10.  

    - **Üzenetek megőrzése**: 1.   
   
      ![Az Event hub részletes adatainak megadása a Spark streaming példához](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Az Event hub részletes adatainak megadása a Spark streaming példához")

1. Kattintson a **Létrehozás** gombra.  A központi telepítésnek néhány másodpercen belül el kell végeznie, és a rendszer visszaküldi a Event Hubs névtér oldalára.

1. A **Beállítások**területen válassza a **megosztott hozzáférési házirendek**elemet.

1. Válassza ki **RootManageSharedAccessKey**.
    
     ![Event hub-szabályzatok beállítása a Spark streaming példához](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Event hub-szabályzatok beállítása a Spark streaming példához")

1. Mentse az **elsődleges kulcs** és a **kapcsolódási karakterlánc** elsődleges kulcsának értékeit az oktatóanyag későbbi részében való használatra.

     ![Az Event hub-szabályzatok kulcsainak megtekintése a Spark streaming példához](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Az Event hub-szabályzatok kulcsainak megtekintése a Spark streaming példához")


## <a name="send-tweets-to-the-event-hub"></a>Tweetek küldése az Event hub-nak

Hozzon létre egy Jupyter-jegyzetfüzetet, és nevezze el **SendTweetsToEventHub**. 

1. A külső Apache Maven-kódtárak hozzáadásához futtassa a következő kódot:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Szerkessze az alábbi kódot a `<Event hub name>`megfelelő `<Event hub namespace connection string>`értékekkel `<CONSUMER SECRET>`: `<ACCESS TOKEN>` `<CONSUMER KEY>`,, `<TOKEN SECRET>` ,, és. A szerkesztett kód futtatásával tweeteket küldhet az Event hubhoz:

    ```scala
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

3. Nyissa meg az Event hub-t a Azure Portal.  Az **Áttekintés**során látnia kell néhány diagramot, amely az Event hub-nak küldött üzeneteket jeleníti meg.

## <a name="read-tweets-from-the-event-hub"></a>Tweetek olvasása az Event hub-ból

Hozzon létre egy másik Jupyter-jegyzetfüzetet, és nevezze el **ReadTweetsFromEventHub**. 

1. A következő kód futtatásával vegyen fel egy külső Apache Maven-tárat:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. Szerkessze az alábbi kódot a `<Event hub name>`helyére, `<Event hub namespace connection string>` és a megfelelő értékekkel. A szerkesztett kód futtatásával olvashatja a tweeteket az Event hub-ból:

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
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

A HDInsight az adatait az Azure Storage vagy a Azure Data Lake Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Ha azt tervezi, hogy azonnal a következő oktatóanyagban dolgozik, érdemes megtartania a fürtöt, ellenkező esetben pedig törölje a fürtöt.

Nyissa meg az Azure Portalon a fürtöt, és válassza a **Törlés** lehetőséget.

![HDInsight Azure Portal – fürt törlése](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésekor a rendszer a HDInsight Spark-fürtöt és az alapértelmezett tárfiókot is törli.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy Apache Spark streaming-alkalmazást, hogy tweeteket küldjön egy Azure Event hub-ba, és létrehozott egy másik alkalmazást, hogy beolvassa a tweeteket az Event hub-ból.  A következő cikkből megtudhatja, hogyan hozhat létre gépi tanulási alkalmazást.

> [!div class="nextstepaction"]
> [Machine learning-alkalmazás létrehozása](./apache-spark-ipython-notebook-machine-learning.md)
