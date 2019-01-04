---
title: 'Oktatóanyag: Az Apache Spark on Azure HDInsight az Azure Event Hubsból származó adatok feldolgozása '
description: Csatlakozás az Apache Spark on Azure HDInsight az Azure Event Hubs és a streamelési adatok dolgozhatók.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: conceptual
ms.date: 12/28/2018
ms.openlocfilehash: 81104c7b206d4fe158df1ae9d329084ad88c3bdd
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976630"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Oktatóanyag: Tweetek feldolgozása a HDInsight az Azure Event Hubs és az Apache Spark használatával

Ebben az oktatóanyagban elsajátíthatja, hogyan hozhat létre egy [Apache Spark](https://spark.apache.org/) streaming-alkalmazás tweetek küldése az Azure event hub, és hozhat létre a tweetek beolvasására az event hubs egy másik alkalmazás. A Spark Stream részletes leírását lásd: [Apache Spark-streamelés – áttekintés](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight Spark-fürt az Azure-ban az adatfolyam-továbbítási szolgáltatást biztosít.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Üzenetek küldése az Azure Event Hubs
> * Üzenetek olvasása az Azure Event Hubs

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* **Végezze el a cikk [oktatóanyag: Adatok betöltése, és lekérdezéseket futtathat az Azure HDInsight az Apache Spark-fürt](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

A valós idejű tweetstream fogadásához létre kell hoznia egy alkalmazást a Twitteren. Kövesse az utasításokat egy Twitter-alkalmazás létrehozása, és írja le az értékeket, hogy ez az oktatóanyag elvégzéséhez szükséges.

1. Keresse meg a [Twitter-alkalmazás felügyeleti](https://apps.twitter.com/).

1. Válassza ki **új alkalmazás létrehozása**.

1. Adja meg a következő értékeket:

    - Name: Adja meg az alkalmazás nevét. Az ebben az oktatóanyagban használt érték **HDISparkStreamApp0423**. Ez a név nem lehet egy egyedi nevet.
    - Leírás: Adja meg az alkalmazás rövid leírását. Az ebben az oktatóanyagban használt érték **egy egyszerű HDInsight Spark streaming-alkalmazás**.
    - Webhely: az alkalmazás webhelyének adja meg. Nem kell lennie egy érvényes webhely.  Az ebben az oktatóanyagban használt érték **http://www.contoso.com**.
    - Visszahívási URL-címe:, üresen is hagyhatja.

1. Válassza ki **Igen, elolvastam és a Twitter-fejlesztői feltételeinek elfogadása**, majd válassza ki **Twitter-alkalmazás létrehozása**.

1. Válassza ki a **kulcsok és hozzáférési tokenek** fülre.

1. Válassza ki **saját hozzáférési token létrehozása** az oldal alján.

1. Jegyezze fel a következő értékeket a oldaláról.  Az oktatóanyag későbbi részében szüksége ezekre az értékekre:

    - **Fogyasztói kulcs (API-kulcs)**    
    - **Fogyasztói titkos kulcs (API titkos kódot)**  
    - **Hozzáférési jogkivonat**
    - **Hozzáférési jogkivonat titkos kulcsa**   

## <a name="create-an-azure-event-hubs-namespace"></a>Az Azure Event Hubs-névtér létrehozása

Az eseményközpont használatával tárolja a tweeteket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. A bal oldali menüben válassza ki a **minden szolgáltatás**.  

1. A **IOT-** válassza **az Event Hubs**. 

    ![Az eseményközpont létrehozása Spark streamelési példa](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Create event hubs Spark streamelési példa")

4. Válassza a **+ Hozzáadás** lehetőséget.
5. Adja meg az új Event Hubs-névtér a következő értékeket:

    - **Név**: Adjon meg egy nevet az eseményközpontnak.  Az ebben az oktatóanyagban használt érték **myeventhubns20180403**.

    - **A tarifacsomag**: Válassza ki **Standard**.

    - **Előfizetés**: Válassza ki a megfelelő előfizetést.

    - **Erőforráscsoport**: A legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válasszon **új létrehozása** egy új erőforráscsoport létrehozásához.

    - **Hely**: Válassza ki ugyanazt **hely** , a késés és a költségek csökkentése érdekében a HDInsight az Apache Spark-fürt.

    - **Engedélyezze az automatikus feltöltési**: (Nem kötelező)  Automatikus feltöltés az Event Hubs-Namespace rendelt, ha az adatforgalom túllépi a hozzárendelt átviteli egységek kapacitását átviteli egységek számát automatikusan skálázza a kapacitást.  

    - **Az automatikus feltöltési kapacitásegységek maximális**: (Nem kötelező)  Ez a csúszka csak akkor jelenik meg, ellenőrizze, hogy ha **engedélyezze az automatikus feltöltési**.  

      ![Adjon meg egy eseményközpont neve Spark streamelési példa](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "adja meg a Spark streamelési példa egy eseményközpont neve")
6. Válassza ki **létrehozás** a névteret létre kívánja hozni.  Az üzembe helyezés néhány perc múlva fog befejeződni.

## <a name="create-an-azure-event-hub"></a>Az Azure event hub létrehozása
Létrehoz egy eseményközpontot, az Event Hubs-névtér üzembe helyezése után.  A portálról:

1. A bal oldali menüben válassza ki a **minden szolgáltatás**.  

1. A **IOT-** válassza **az Event Hubs**.  

1. Válassza ki az Event Hubs-névtér a listából.  

1. Az a **Event Hubs-Namespace** lapon jelölje be **+ Event Hub**.  
1. Adja meg a következő értékeket a **Eseményközpont létrehozása** oldalon:

    - **Név**: Nevezze el az Event hub. 
 
    - **Partíció száma**: 10.  

    - **Üzenetmegőrzés**: 1.   
   
      ![Adja meg az event hub-adatok a Spark streamelési példa](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Spark streamelési példa event hub-adatok megadása")

1. Kattintson a **Létrehozás** gombra.  Az üzembe helyezés néhány másodpercet vesz igénybe, és visszatér az Event Hubs-Namespace oldal.

1. A **beállítások**válassza **megosztott elérési házirendek**.

1. Válassza ki **RootManageSharedAccessKey**.
    
     ![Event Hub-házirendek beállítása a Spark streamelési példa](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Eseményközpont beállítása házirendek a Spark streamelési példa")

1. Mentse az értékeket a **elsődleges kulcs** és **kapcsolati karakterlánc – elsődleges kulcs** az oktatóanyag későbbi részében használni.

     ![Eseményközpont-szabályzat kulcsok megtekintéséhez a Spark streamelési példa](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "nézet Eseményközpontba szabályzat a kulcsok a Spark streamelési példa")


## <a name="send-tweets-to-the-event-hub"></a>Tweetek küldése az event hubs

Jupyter notebook létrehozása, és adja neki **SendTweetsToEventHub**. 

1. Futtassa a következő kódot a külső Apache Maven-kódtárak hozzáadása:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Futtassa a következő kódot a tweetek küldése az eseményközpont:

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

3. Nyissa meg az event hubs az Azure Portalon.  A **áttekintése**, meg kell jelennie az üzeneteket az eseményközpontba küldött egyes diagramok.

## <a name="read-tweets-from-the-event-hub"></a>Tweetek beolvasása az event hubs

Hozzon létre egy másik Jupyter notebookot, és adja neki **ReadTweetsFromEventHub**. 

1. Futtassa a következő kódot az Apache Maven külső osztálykönyvtárak hozzáadásához:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Futtassa a tweetek olvasásához az event hubs az alábbi kódot:

    ```
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

HDInsight az adatok az Azure Storage vagy az Azure Data Lake Storage tárolja, akkor nyugodtan törölheti az fürt, ha nem használja. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Ha azt tervezi, azonnal dolgozhat a következő oktatóanyaggal, érdemes a fürtöt megtarthatja, ellenkező esetben lépjen tovább, és törölje a fürtöt.

Nyissa meg az Azure Portalon a fürtöt, és válassza a **Törlés** lehetőséget.

![HDInsight-fürt törlése](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésekor a rendszer a HDInsight Spark-fürtöt és az alapértelmezett tárfiókot is törli.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Olvassa el az üzenetet egy adott eseményközpontból.
Folytassa a következő cikkben létrehozhat egy machine learning-alkalmazás megtekintéséhez. 

> [!div class="nextstepaction"]
> [Machine learning-alkalmazás létrehozása](./apache-spark-ipython-notebook-machine-learning.md)


