---
title: Az Azure Databricks használata hangulatelemzéshez
description: Ismerje meg, hogyan használhatja az Azure Databricks-t az Event Hubs és a Cognitive Services API használatával, hogy közel valós időben futtathatja a streamelési adatok hangulatelemzését.
services: azure-databricks
author: lenadroid
ms.author: alehall
ms.reviewer: mamccrea
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 07/29/2019
ms.openlocfilehash: 382dff156c088f367200f0dd46c3758193ade189
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75889229"
---
# <a name="tutorial-sentiment-analysis-on-streaming-data-using-azure-databricks"></a>Oktatóanyag: Streamelési adatok hangulatelemzése az Azure Databricks használatával

Ebben az oktatóanyagban megtudhatja, hogyan futtathat hangulatelemzést az Azure Databricks használatával közel valós időben egy adatfolyamon. Az Azure Event Hubs használatával beállít egy adatbetöltési rendszert. Az Event Hubs szolgáltatás üzeneteit az Azure Databricks szolgáltatásban a Spark Event Hubs összekötő segítségével használhatja fel. Végül a Cognitive Service API-k használatával hangulatelemzés futtatásához a streamelt adatok.

Az oktatóanyag elvégzésével „Azure” kifejezéseket tartalmazó Twitter-tweetek streamelését valósította meg, és hangulatelemzést végzett a tweeteken.

Az alábbi ábrán az alkalmazásfolyam látható:

![Azure Databricks az Event Hubs és a Cognitive Services szolgáltatással](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-tutorial.png "Azure Databricks az Event Hubs és a Cognitive Services szolgáltatással")

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Databricks-munkaterület létrehozása
> * Spark-fürt létrehozása az Azure Databricksben
> * Twitter-alkalmazás létrehozása a streamadatok eléréséhez
> * Jegyzetfüzetek létrehozása az Azure Databricksben
> * Kódtárak csatlakoztatása az Event Hubshoz és a Twitter API-hoz
> * Cognitive Services-fiók létrehozása és a hozzáférési kulcs lekérése
> * Tweetek küldése az Event Hubsnak
> * Tweetek beolvasása az Event Hubsról
> * Hangulatelemzés futtatása tweeteken

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=sparkeventhubs-docs-alehall) mielőtt elkezdené.

> [!Note]
> Ez az oktatóanyag nem hajtható végre **az Azure ingyenes próba-előfizetésével.**
> Ha van ingyenes fiókja, nyissa meg a profilját, és módosítsa az előfizetését **a felosztó-kirovó szolgáltatásra.** További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket. Ezután [távolítsa el a költségkeretet,](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-azure-portal)és kérjen [kvótanövelést](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) a régióban a vCPU-khoz. Az Azure Databricks-munkaterület létrehozásakor kiválaszthatja a **próbaverziós (prémium – 14 napos ingyenes dbári)** díjszabási szintet, hogy a munkaterület 14 napig hozzáférést biztosítson az ingyenes prémium szintű Azure Databricks KBB-khoz.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt nekilát az oktatóanyagnak, ellenőrizze, hogy megfelel-e a következő feltételeknek:
- Egy Azure Event Hubs névtér.
- Egy eseményközpont a névtéren belül.
- Az Event Hubs-névtér elérésére szolgáló kapcsolati sztring. A kapcsolati sztring formátumának a következőhöz hasonlónak kell lennie: `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`.
- Megosztott hozzáférési szabályzat neve és szabályzatkulcs az Event Hubshoz.

Ezeket az előfeltételeket az [Azure Event Hubs-névtér és eseményközpont létrehozását](../event-hubs/event-hubs-create.md) ismertető cikk lépéseit követve teljesítheti.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall)

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks-munkaterületet fog létrehozni az Azure Portal használatával.

1. Az Azure Portalon válassza az Erőforrás > **adatok létrehozása + Analytics** > **Azure Databricks** **lehetőséget.**

    ![Databricks az Azure Portalon](./media/databricks-sentiment-analysis-cognitive-services/azure-databricks-on-portal.png "Databricks az Azure Portalon")

3. Az **Azure Databricks szolgáltatás** pontban adja meg az értékeket Databricks-munkaterület létrehozásához.

    ![Azure Databricks-munkaterület létrehozása](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-workspace.png "Azure Databricks-munkaterület létrehozása")

    Adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../azure-resource-manager/management/overview.md). |
    |**Helyen**     | Válassza az **USA 2. keleti régiója** lehetőséget. A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/?WT.mc_id=sparkeventhubs-docs-alehall).        |
    |**Árképzési szint**     |  Válassza a **Standard** vagy a **Prémium** előfizetést. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall).       |

    Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

4. A fiók létrehozása eltarthat néhány percig. A fiók létrehozása során a portál jobb oldalán megjelenik az **Üzembe helyezés beküldése a következőhöz: Azure Databricks** csempe. Lehetséges, hogy jobbra kell görgetnie az irányítópulton, hogy megjelenjen a csempe. Megjelenik egy folyamatjelző is a képernyő tetejéhez közel. Mindkét területen nyomon követheti a folyamat előrehaladását.

    ![Databricks telepítési csempe](./media/databricks-sentiment-analysis-cognitive-services/databricks-deployment-tile.png "Databricks telepítési csempe")

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

1. Az Azure Portalon lépjen a létrehozott Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A rendszer átirányítja az Azure Databricks portáljára. A portálon válassza a **Fürt** elemet.

    ![Databricks az Azure-ban](./media/databricks-sentiment-analysis-cognitive-services/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](./media/databricks-sentiment-analysis-cognitive-services/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

   * Adjon egy nevet a fürtnek.
   * Ebben a cikkben hozzon létre egy fürtet **6.0-s** futásidejű.
   * Győződjön meg arról, hogy bejelöli a **Percek \_ \_ utáni inaktivitás** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.

   Válassza ki a fürtfeldolgozó és az illesztőprogram-csomópont méretét, amely megfelel a technikai feltételeknek és a [költségvetésnek.](https://azure.microsoft.com/pricing/details/databricks/?WT.mc_id=sparkeventhubs-docs-alehall)

     Válassza a **Fürt létrehozása** lehetőséget. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

A valós idejű tweetstream fogadásához létre kell hoznia egy alkalmazást a Twitteren. Kövesse a Twitter-alkalmazás létrehozására vonatkozó utasításokat, és jegyezze fel az értékeket, amelyek az oktatóanyag elvégzéséhez szükségesek.

1. Webböngészőből nyissa meg a [Twitter for Developers (Fejlesztőknek)](https://developer.twitter.com/en/apps)webhelyet, és válassza **az Alkalmazás létrehozása**lehetőséget. Előfordulhat, hogy megjelenik egy üzenet, amely szerint egy Twitter-fejlesztői fiókra kell jelentkeznie. Nyugodtan tegye meg, és miután a kérelmet jóváhagyták, látnia kell egy visszaigazoló e-mailt. A fejlesztői fiók jóváhagyása több napig is eltarthat.

    ![Twitter fejlesztői fiók megerősítése](./media/databricks-sentiment-analysis-cognitive-services/databricks-twitter-dev-confirmation.png "Twitter fejlesztői fiók megerősítése")

2. Az **Alkalmazás létrehozása** oldalon adja meg az új alkalmazás adatait, majd válassza a **Twitter-alkalmazás létrehozása** parancsot.

    ![Twitter alkalmazás részletei](./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details.png "Twitter alkalmazás részletei")

    ![Twitter alkalmazás részletei](./media/databricks-sentiment-analysis-cognitive-services/databricks-provide-twitter-app-details-create.png "Twitter alkalmazás részletei")

3. Az alkalmazáslapon válassza a **Kulcsok és jogkivonatok** lapot, és másolja a **Fogyasztói API-kulcs** és a **fogyasztói API titkos kulcs értékeit.** A hozzáférési jogkivonat és a **hozzáférési jogkivonat titkos** kulcsa csoportban válassza a **Create(Create)** lehetőséget is a hozzáférési jogkivonatok létrehozásához. Másolja a **Hozzáférési token** és a **Hozzáférési token titkos kulcsa** mező értékeit.

    ![Twitter alkalmazás részletei](./media/databricks-sentiment-analysis-cognitive-services/twitter-app-key-secret.png "Twitter alkalmazás részletei")

Mentse a Twitter-alkalmazáshoz lekért értékeket. Az oktatóanyag későbbi részében szüksége lesz rájuk.

## <a name="attach-libraries-to-spark-cluster"></a>Kódtárak csatolása Spark-fürthöz

Ez az oktatóanyag bemutatja, hogyan küldhet tweeteket az Event Hubsnak a Twitter API-k segítségével. Ezenkívül az [Apache Spark Event Hubs-összekötő](https://github.com/Azure/azure-event-hubs-spark?WT.mc_id=sparkeventhubs-docs-alehall) segítségével adatokat olvashat be és írhat az Azure Event Hubsba. Ha ezeket az API-kat a fürt részeként szeretné használni, adja hozzá őket az Azure Databricks-hez, és társítsa őket a Spark-fürthöz. Az alábbi utasítások bemutatják, hogyan vehet fel könyvtárat.

1. Az Azure Databricks-munkaterületen válassza **a Fürtök**lehetőséget, és válassza ki a meglévő Spark-fürtöt. A fürt menüben válassza a **Könyvtárak parancsot,** majd kattintson **az Új telepítése parancsra.**

   ![Tár hozzáadása párbeszédpanel](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-locate-cluster.png "Tár hozzáadása a fürt megkereséséhez")

   ![Tár hozzáadása párbeszédpanel](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-install-new.png "Tár telepítése új")

2. Az Új könyvtár lapon a **Forrás** területen válassza a **Maven**lehetőséget. A **Koordináta**mezőben kattintson a hozzáadni kívánt csomag **csomagjának keresési csomagjai** elemre. Az oktatóanyagban használt kódtárak Maven-koordinátái a következők:

   * Spark Event Hubs-összekötő – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API – `org.twitter4j:twitter4j-core:4.0.7`

     ![Maven koordináták megadására](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search.png "Maven koordináták megadására")

     ![Maven koordináták megadására](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-search-dialogue.png "Maven koordinátáinak keresése")

3. Válassza az **Install** (Telepítés) lehetőséget.

4. A fürt menüben győződjön meg arról, hogy mindkét könyvtár megfelelően van telepítve és csatlakoztatva.

    ![Tárak ellenőrzése](./media/databricks-sentiment-analysis-cognitive-services/databricks-add-library-check.png "Tárak ellenőrzése")

6. Ismételje meg ezeket a lépéseket a Twitter-csomag (`twitter4j-core:4.0.7`) esetében is.

## <a name="get-a-cognitive-services-access-key"></a>Cognitive Services hozzáférési kulcs beszerzése

Ebben az oktatóanyagban az [Azure Cognitive Services szövegelemzési API-k](../cognitive-services/text-analytics/overview.md) használatával közel valós időben futtathatja a hangulatelemzést a tweetek folyamán. Az API-k használata előtt létre kell hoznia egy Azure Cognitive Services-fiókot az Azure-ban, és le kell kérnie egy hozzáférési kulcsot a Text Analytics API-k használatához.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/?WT.mc_id=sparkeventhubs-docs-alehall)

2. Válassza a **+ Erőforrás létrehozása** lehetőséget.

3. Az Azure Marketplace-en válassza az **AI + Cognitive Services** > **Text Analytics API-t.**

    ![Kognitív szolgáltatások fiók létrehozása](./media/databricks-sentiment-analysis-cognitive-services/databricks-cognitive-services-text-api.png "Kognitív szolgáltatások fiók létrehozása")

4. A **Létrehozás** párbeszédpanelen adja meg az alábbi értékeket:

    ![Kognitív szolgáltatások fiók létrehozása](./media/databricks-sentiment-analysis-cognitive-services/create-cognitive-services-account.png "Kognitív szolgáltatások fiók létrehozása")

   - Adja meg a Cognitive Services-fiók nevét.
   - Adja meg az Azure-előfizetés nevét, amelyben a fiókot létrehozta.
   - Válasszon egy Azure-beli helyet.
   - Válasszon egy tarifacsomagot a szolgáltatáshoz. További információ a Cognitive Services díjszabásáról: [díjszabási lap](https://azure.microsoft.com/pricing/details/cognitive-services/?WT.mc_id=sparkeventhubs-docs-alehall).
   - Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt kíván-e kijelölni.

     Kattintson a **Létrehozás** gombra.

5. A fiók létrehozása után az **Áttekintés** lapon válassza a **Hozzáférési kulcsok megjelenítése**lehetőséget.

    ![Hozzáférési kulcsok megjelenítése](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-get-access-keys.png "Hozzáférési kulcsok megjelenítése")

    Emellett másolja a végpont URL-címének egy részét a képernyőképen látható módon. Erre az URL-címre szükség lesz az oktatóanyagban.

6. A **Kulcsok kezelése** területen kattintson a használni kívánt kulcs mellett található másolás ikonra.

    ![Hozzáférési kulcsok másolása](./media/databricks-sentiment-analysis-cognitive-services/cognitive-services-copy-access-keys.png "Hozzáférési kulcsok másolása")

7. Mentse a végponti URL-cím és a hozzáférési kulcs ebben a lépésben beszerzett értékeit. Az oktatóanyag későbbi részében szüksége lesz rájuk.

## <a name="create-notebooks-in-databricks"></a>Jegyzetfüzetek létrehozása a Databricksben

Ebben a szakaszban két jegyzetfüzetet hoz létre a Databricks munkaterületen a következő nevekkel

- **SendTweetsToEventHub** – Előállítói jegyzetfüzet a tweetek beszerzésére a Twitterről, majd azok streamelésére az Event Hubsnak.
- **AnalyzeTweetsFromEventHub** – Fogyasztói jegyzetfüzet tweetek olvasásához az Event Hubsról, valamint hangulatelemzések futtatásához.

1. A bal oldali panelen válassza a **Munkaterület** elemet. A **Munkaterület** legördülő menüjében válassza a **Létrehozás**, majd a **Jegyzetfüzet** elemet.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/databricks-sentiment-analysis-cognitive-services/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

2. A **Jegyzetfüzet létrehozása** párbeszédpanelen írja be a **SendTweetsToEventHub** nevet, a nyelvnél válassza a **Scala** lehetőséget, majd válassza ki a korábban létrehozott Spark-fürtöt.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/databricks-sentiment-analysis-cognitive-services/databricks-notebook-details.png "Jegyzetfüzet létrehozása a Databricks-ben")

    Kattintson a **Létrehozás** gombra.

3. Az **AnalyzeTweetsFromEventHub** jegyzetfüzet létrehozásához ismételje meg ezeket a lépéseket.

## <a name="send-tweets-to-event-hubs"></a>Tweetek küldése az Event Hubsnak

A **SendTweetsToEventHub** notebook, illessze be a következő kódot, és cserélje le a helyőrzők az értékeket az Event Hubs névtér és a Twitter alkalmazás, amely korábban létrehozott. Ez a jegyzetfüzet valós időben streameli az „Azure” kifejezést tartalmazó tweeteket az Event Hubsba.

> [!NOTE]
> Twitter API bizonyos kérés korlátozások és [kvóták](https://developer.twitter.com/en/docs/basics/rate-limiting.html). Ha nem elégedett a normál sebesség korlátozása a Twitter API-ban, akkor szöveges tartalmat hozhat létre a Twitter API használata nélkül ebben a példában. Ehhez állítsa be a változó `test` **dataSource** `twitter` helyett, és feltölti a **listát testSource** preferált teszt bemenet.

```scala
    import scala.collection.JavaConverters._
    import com.microsoft.azure.eventhubs._
    import java.util.concurrent._
    import scala.collection.immutable._
    import scala.concurrent.Future
    import scala.concurrent.ExecutionContext.Implicits.global

    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val pool = Executors.newScheduledThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sleep(time: Long): Unit = Thread.sleep(time)

    def sendEvent(message: String, delay: Long) = {
      sleep(delay)
      val messageData = EventData.create(message.getBytes("UTF-8"))
      eventHubClient.get().send(messageData)
      System.out.println("Sent event: " + message + "\n")
    }

    // Add your own values to the list
    val testSource = List("Azure is the greatest!", "Azure isn't working :(", "Azure is okay.")

    // Specify 'test' if you prefer to not use Twitter API and loop through a list of values you define in `testSource`
    // Otherwise specify 'twitter'
    val dataSource = "test"

    if (dataSource == "twitter") {

      import twitter4j._
      import twitter4j.TwitterFactory
      import twitter4j.Twitter
      import twitter4j.conf.ConfigurationBuilder

      // Twitter configuration!
      // Replace values below with you

      val twitterConsumerKey = "<CONSUMER API KEY>"
      val twitterConsumerSecret = "<CONSUMER API SECRET>"
      val twitterOauthAccessToken = "<ACCESS TOKEN>"
      val twitterOauthTokenSecret = "<TOKEN SECRET>"

      val cb = new ConfigurationBuilder()
        cb.setDebugEnabled(true)
        .setOAuthConsumerKey(twitterConsumerKey)
        .setOAuthConsumerSecret(twitterConsumerSecret)
        .setOAuthAccessToken(twitterOauthAccessToken)
        .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

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
            sendEvent(status.getText(), 5000)
          }
          lowestStatusId = Math.min(status.getId(), lowestStatusId)
        }
        query.setMaxId(lowestStatusId - 1)
      }

    } else if (dataSource == "test") {
      // Loop through the list of test input data
      while (true) {
        testSource.foreach {
          sendEvent(_,5000)
        }
      }

    } else {
      System.out.println("Unsupported Data Source. Set 'dataSource' to \"twitter\" or \"test\"")
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
```

A jegyzetfüzet futtatásához használja a **SHIFT + ENTER** billentyűparancsot. Ekkor az alábbi kódrészlethez hasonló kimenetnek kell megjelennie. A kimenetben szereplő minden egyes esemény egy, az „Azure” kifejezést tartalmazó tweet, amely be lett töltve az Event Hubsba.

    Sent event: @Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence

    Sent event: Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure

    Sent event: 4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie

    Sent event: Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk

    Sent event: Top 10 Tricks to #Save Money with #Azure Virtual Machines https://t.co/F2wshBXdoz #Cloud

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Tweetek beolvasása az Event Hubsról

Illessze be a következő kódot az **AnalyzeTweetsFromEventHub** jegyzetfüzetbe, és a helyőrzőket cserélje le a korábban létrehozott Azure Event Hubs értékeire. Ez a jegyzetfüzet beolvassa a tweeteket, amelyeket korábban az Event Hubsba streamelt a **SendTweetsToEventHub** jegyzetfüzet segítségével.

```scala

    import org.apache.spark.eventhubs._
    import com.microsoft.azure.eventhubs._

    // Build connection string with the above information
    val namespaceName = "<EVENT HUBS NAMESPACE>"
    val eventHubName = "<EVENT HUB NAME>"
    val sasKeyName = "<POLICY NAME>"
    val sasKey = "<POLICY KEY>"
    val connStr = new com.microsoft.azure.eventhubs.ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey)

    val customEventhubParameters =
      EventHubsConf(connStr.toString())
      .setMaxEventsPerTrigger(5)

    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

    incomingStream.printSchema

    // Sending the incoming stream into the console.
    // Data comes in batches!
    incomingStream.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

A következő kimenetet kapja:


    root
     |-- body: binary (nullable = true)
     |-- offset: long (nullable = true)
     |-- seqNumber: long (nullable = true)
     |-- enqueuedTime: long (nullable = true)
     |-- publisher: string (nullable = true)
     |-- partitionKey: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+------+--------------+---------------+---------+------------+
    |body  |offset|sequenceNumber|enqueuedTime   |publisher|partitionKey|
    +------+------+--------------+---------------+---------+------------+
    |[50 75 62 6C 69 63 20 70 72 65 76 69 65 77 20 6F 66 20 4A 61 76 61 20 6F 6E 20 41 70 70 20 53 65 72 76 69 63 65 2C 20 62 75 69 6C 74 2D 69 6E 20 73 75 70 70 6F 72 74 20 66 6F 72 20 54 6F 6D 63 61 74 20 61 6E 64 20 4F 70 65 6E 4A 44 4B 0A 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 37 76 73 37 63 4B 74 76 61 68 20 0A 23 63 6C 6F 75 64 63 6F 6D 70 75 74 69 6E 67 20 23 41 7A 75 72 65]                              |0     |0             |2018-03-09 05:49:08.86 |null     |null        |
    |[4D 69 67 72 61 74 65 20 79 6F 75 72 20 64 61 74 61 62 61 73 65 73 20 74 6F 20 61 20 66 75 6C 6C 79 20 6D 61 6E 61 67 65 64 20 73 65 72 76 69 63 65 20 77 69 74 68 20 41 7A 75 72 65 20 53 51 4C 20 44 61 74 61 62 61 73 65 20 4D 61 6E 61 67 65 64 20 49 6E 73 74 61 6E 63 65 20 7C 20 23 41 7A 75 72 65 20 7C 20 23 43 6C 6F 75 64 20 68 74 74 70 73 3A 2F 2F 74 2E 63 6F 2F 73 4A 48 58 4E 34 74 72 44 6B]            |168   |1             |2018-03-09 05:49:24.752|null     |null        |
    +------+------+--------------+---------------+---------+------------+

    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

A bináris módú kimenetet a következő kódrészlettel konvertálhatja sztringgé.

```scala
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so we cast it to string to see the actual content of the message
    val messages =
      incomingStream
      .withColumn("Offset", $"offset".cast(LongType))
      .withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType))
      .withColumn("Timestamp", $"enqueuedTime".cast(LongType))
      .withColumn("Body", $"body".cast(StringType))
      .select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

A kimenetnek ezután a következő kódrészlethez hasonlónak kell lennie:

    root
     |-- Offset: long (nullable = true)
     |-- Time (readable): timestamp (nullable = true)
     |-- Timestamp: long (nullable = true)
     |-- Body: string (nullable = true)

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +------+-----------------+----------+-------+
    |Offset|Time (readable)  |Timestamp |Body
    +------+-----------------+----------+-------+
    |0     |2018-03-09 05:49:08.86 |1520574548|Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah
    #cloudcomputing #Azure          |
    |168   |2018-03-09 05:49:24.752|1520574564|Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |
    |0     |2018-03-09 05:49:02.936|1520574542|@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|
    |176   |2018-03-09 05:49:20.801|1520574560|4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |
    +------+-----------------+----------+-------+
    -------------------------------------------
    Batch: 1
    -------------------------------------------
    ...
    ...

Az Azure Event Hubs-ból az Azure Databricks-be közel valós időben streamelte az adatokat az Apache Spark Event Hubs-összekötőhasználatával. A Spark Event Hubs-összekötőinek használatáról az [összekötő dokumentációjában](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs?WT.mc_id=sparkeventhubs-docs-alehall) talál további információt.

## <a name="run-sentiment-analysis-on-tweets"></a>Hangulatelemzés futtatása tweeteken

Ebben a szakaszban hangulatelemzést futtat a Twitter API használatával fogadott tweeteken. Ebben a szakaszban a kódrészleteket fogja hozzáadni ugyanazon **AnalyzeTweetsFromEventHub** jegyzetfüzethez.

Első lépésként adjon hozzá egy új kódcellát a jegyzetfüzethez, és illessze be az alábbi kódrészletet. Ez a kódrészlet a Language and Sentiment API-val használható adattípusokat határozza meg.

```scala
import java.io._
import java.net._
import java.util._

case class Language(documents: Array[LanguageDocuments], errors: Array[Any]) extends Serializable
case class LanguageDocuments(id: String, detectedLanguages: Array[DetectedLanguages]) extends Serializable
case class DetectedLanguages(name: String, iso6391Name: String, score: Double) extends Serializable

case class Sentiment(documents: Array[SentimentDocuments], errors: Array[Any]) extends Serializable
case class SentimentDocuments(id: String, score: Double) extends Serializable

case class RequestToTextApi(documents: Array[RequestToTextApiDocument]) extends Serializable
case class RequestToTextApiDocument(id: String, text: String, var language: String = "") extends Serializable
```

Adjon hozzá egy új kódcellát, és illessze be az alábbi részletet. Ez a kódrészlet egy olyan objektumot tartalmaz, amely a Text Analysis API nyelvfelismerés és hangulatelemzés céljára való hívásához szükséges függvényeket tartalmaz. Győződjön meg arról, hogy lecserélte a helyőrzőt `<PROVIDE ACCESS KEY HERE>` a Cognitive Services-fiókhoz beolvasott értékre.

```scala
import javax.net.ssl.HttpsURLConnection
import com.google.gson.Gson
import com.google.gson.GsonBuilder
import com.google.gson.JsonObject
import com.google.gson.JsonParser
import scala.util.parsing.json._

object SentimentDetector extends Serializable {

    // Cognitive Services API connection settings
    val accessKey = "<PROVIDE ACCESS KEY HERE>"
    val host = "https://cognitive-docs.cognitiveservices.azure.com/"
    val languagesPath = "/text/analytics/v2.1/languages"
    val sentimentPath = "/text/analytics/v2.1/sentiment"
    val languagesUrl = new URL(host+languagesPath)
    val sentimenUrl = new URL(host+sentimentPath)
    val g = new Gson

    def getConnection(path: URL): HttpsURLConnection = {
        val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
        connection.setRequestMethod("POST")
        connection.setRequestProperty("Content-Type", "text/json")
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", accessKey)
        connection.setDoOutput(true)
        return connection
    }

    def prettify (json_text: String): String = {
        val parser = new JsonParser()
        val json = parser.parse(json_text).getAsJsonObject()
        val gson = new GsonBuilder().setPrettyPrinting().create()
        return gson.toJson(json)
    }

    // Handles the call to Cognitive Services API.
    def processUsingApi(request: RequestToTextApi, path: URL): String = {
        val requestToJson = g.toJson(request)
        val encoded_text = requestToJson.getBytes("UTF-8")
        val connection = getConnection(path)
        val wr = new DataOutputStream(connection.getOutputStream())
        wr.write(encoded_text, 0, encoded_text.length)
        wr.flush()
        wr.close()

        val response = new StringBuilder()
        val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
        var line = in.readLine()
        while (line != null) {
            response.append(line)
            line = in.readLine()
        }
        in.close()
        return response.toString()
    }

    // Calls the language API for specified documents.
    def getLanguage (inputDocs: RequestToTextApi): Option[Language] = {
        try {
            val response = processUsingApi(inputDocs, languagesUrl)
            // In case we need to log the json response somewhere
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val language = g.fromJson(niceResponse, classOf[Language])
            if (language.documents(0).detectedLanguages(0).iso6391Name == "(Unknown)")
                return None
            return Some(language)
        } catch {
            case e: Exception => return None
        }
    }

    // Calls the sentiment API for specified documents. Needs a language field to be set for each of them.
    def getSentiment (inputDocs: RequestToTextApi): Option[Sentiment] = {
        try {
            val response = processUsingApi(inputDocs, sentimenUrl)
            val niceResponse = prettify(response)
            // Deserializing the JSON response from the API into Scala types
            val sentiment = g.fromJson(niceResponse, classOf[Sentiment])
            return Some(sentiment)
        } catch {
            case e: Exception => return None
        }
    }
}
```

Adjon hozzá egy másik cellát egy Spark UDF (felhasználó által definiált függvény) definiálásához, amely meghatározza a hangulatot.

```scala
// User Defined Function for processing content of messages to return their sentiment.
val toSentiment =
    udf((textContent: String) =>
        {
            val inputObject = new RequestToTextApi(Array(new RequestToTextApiDocument(textContent, textContent)))
            val detectedLanguage = SentimentDetector.getLanguage(inputObject)
            detectedLanguage match {
                case Some(language) =>
                    if(language.documents.size > 0) {
                        inputObject.documents(0).language = language.documents(0).detectedLanguages(0).iso6391Name
                        val sentimentDetected = SentimentDetector.getSentiment(inputObject)
                        sentimentDetected match {
                            case Some(sentiment) => {
                                if(sentiment.documents.size > 0) {
                                    sentiment.documents(0).score.toString()
                                }
                                else {
                                    "Error happened when getting sentiment: " + sentiment.errors(0).toString
                                }
                            }
                            case None => "Couldn't detect sentiment"
                        }
                    }
                    else {
                        "Error happened when getting language" + language.errors(0).toString
                    }
                case None => "Couldn't detect language"
            }
        }
    )
```

Adjon hozzá egy utolsó kódcellát, amellyel előkészítheti az adatkeretet a tweet tartalmával és a tweethez társított hangulatértékkel.

```scala
// Prepare a dataframe with Content and Sentiment columns
val streamingDataFrame = incomingStream.selectExpr("cast (body as string) AS Content").withColumn("Sentiment", toSentiment($"Content"))

// Display the streaming data with the sentiment
streamingDataFrame.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
```

A következő kódrészlethez hasonló kimenetnek kell megjelennie:

    -------------------------------------------
    Batch: 0
    -------------------------------------------
    +--------------------------------+------------------+
    |Content                         |Sentiment         |
    +--------------------------------+------------------+
    |Public preview of Java on App Service, built-in support for Tomcat and OpenJDK
    https://t.co/7vs7cKtvah   #cloudcomputing #Azure          |0.7761918306350708|
    |Migrate your databases to a fully managed service with Azure SQL Database Managed Instance | #Azure | #Cloud https://t.co/sJHXN4trDk    |0.8558163642883301|
    |@Microsoft and @Esri launch Geospatial AI on Azure https://t.co/VmLUCiPm6q via @geoworldmedia #geoai #azure #gis #ArtificialIntelligence|0.5               |
    |4 Killer #Azure Features for #Data #Performance https://t.co/kpIb7hFO2j by @RedPixie                                                    |0.5               |
    +--------------------------------+------------------+

A **Hangulat** oszlopban szereplő **1**-hez közeli érték nagyszerű Azure felhasználói élményre utal. A **0**-hoz közeli érték arra utal, hogy a felhasználók a Microsoft Azure használata során hibákkal találkoztak.

Ennyi az egész! Az Azure Databricks használatával sikeresen streamelte az adatokat az Azure Event Hubs-ba, felhasználta az adatfolyam-adatokat az Event Hubs-összekötő használatával, majd közel valós időben futtatta a streamelési adatok hangulatelemzését.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után leállíthatja a fürtöt. Ehhez az Azure Databricks-munkaterület bal oldali panelén kattintson a **Fürtök** elemre. A leállítani kívánt fürtnél vigye az egérmutatót a **Műveletek** oszlopban található három pont fölé, majd kattintson a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/databricks-sentiment-analysis-cognitive-services/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha nem szakítja meg manuálisan a fürtöt, az automatikusan leáll, feltéve, hogy a fürt létrehozásakor bejelölte a **Percek \_ \_ utáni inaktivitás** után jelölőnégyzetet. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan használhatja az Azure Databricks szolgáltatást az adatok Azure Event Hubsra való streamelésére, és hogyan olvashatja valós időben a streamelt adatokat az Event Hubsról. Megismerte, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Azure Databricks-munkaterület létrehozása
> * Spark-fürt létrehozása az Azure Databricksben
> * Twitter-alkalmazás létrehozása a streamadatok eléréséhez
> * Jegyzetfüzetek létrehozása az Azure Databricksben
> * Kódtárak hozzáadása és csatlakoztatása az Event Hubshoz és a Twitter API-hoz
> * Microsoft Cognitive Services-fiók létrehozása és a hozzáférési kulcs lekérése
> * Tweetek küldése az Event Hubsnak
> * Tweetek beolvasása az Event Hubsról
> * Hangulatelemzés futtatása tweeteken

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hajthat végre gépi tanulási feladatokat az Azure Databricks használatával.

> [!div class="nextstepaction"]
>[Az Azure Databricks használatával történő gépi tanulás](/azure/databricks/applications/machine-learning/mllib/decision-trees)

