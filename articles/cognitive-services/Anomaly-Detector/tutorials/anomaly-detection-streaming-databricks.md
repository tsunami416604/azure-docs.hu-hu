---
title: 'Oktatóanyag: Streamelési adatok rendellenességeinek észlelése az Azure Databricks használatával'
description: Az Anomáliadetektálási detector használatával API és az Azure Databricks használatával figyelheti az adatok a rendellenességeket.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 05/08/2019
ms.author: aahi
ms.openlocfilehash: d1808bb88ac1bb27f2fd0652585521fbb2abdf56
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734666"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Oktatóanyag: Streamelési adatok rendellenességeinek észlelése az Azure Databricks használatával

[Az Azure Databricks](https://azure.microsoft.com/en-us/services/databricks/) egy gyors, könnyű és együttműködő Apache Spark-alapú elemzési szolgáltatás. Az Anomáliadetektálási detector használatával API, Azure Cognitive Services részeként az idősoros adatokat figyelési megoldást kínál. Ebben az oktatóanyagban használja az anomáliadetektálás futtatása közel valós idejű adatokat streamelt az Azure Databricks használatával. Twitter-adatok az Azure Event Hubs használatával fog, és importálja azokat az Azure Databricks a Spark Event Hubs összekötője segítségével. Ezt követően az API-t fogja használni a streamelési adatok hangulatelemzéséhez a rendellenességek észlelése. 

Az alábbi ábrán az alkalmazásfolyam látható:

![Az Azure Databricks használata az Event Hubs és a Cognitive Services szolgáltatásokkal](../media/tutorials/databricks-cognitive-services-tutorial.png "Az Azure Databricks használata az Event Hubs és a Cognitive Services szolgáltatásokkal")

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Databricks-munkaterület létrehozása
> * Spark-fürt létrehozása az Azure Databricksben
> * Twitter-alkalmazás létrehozása a streamadatok eléréséhez
> * Jegyzetfüzetek létrehozása az Azure Databricksben
> * Kódtárak csatlakoztatása az Event Hubshoz és a Twitter API-hoz
> * Hozzon létre egy Anomáliadetektálási detector használatával erőforrást, és a hozzáférési kulcs lekérése
> * Tweetek küldése az Event Hubsnak
> * Tweetek beolvasása az Event Hubsról
> * Anomáliadetektálás futtatása a tweeteken

> [!Note]
> Ez az oktatóanyag bemutatja a végrehajtási az ajánlott módszer [megoldásarchitektúra](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) az Anomáliadetektálási detector használatával API-hoz.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

> [!Note]
> Ebben az oktatóanyagban nem lehet végrehajtani, ingyenes próba kulccsal az Anomáliadetektálási detector használatával API-hoz. Ha egy ingyenes fiókot használna az Azure Databricks-fürt létrehozásához, a fürt létrehozása előtt nyissa meg a saját profilját, és módosítsa az előfizetését **használatalapú fizetésre**. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure Event Hubs-névtér](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) és az eseményközpont.

- A [kapcsolati karakterlánc](../../../event-hubs/event-hubs-get-connection-string.md) az Event Hubs-névtér elérésére. A kapcsolati karakterlánc rendelkeznie kell egy hasonló formátumot:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- A megosztott elérési házirend neve és a házirend az Event Hubs kulcsot.

Tekintse meg az Azure Event Hubs [rövid](../../../event-hubs/event-hubs-create.md) a névtér és az eseményközpont létrehozásával kapcsolatos információk.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban, hozzon létre egy Azure Databricks munkaterületen a [az Azure portal](https://portal.azure.com/).

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Elemzés** > **Azure Databricks** elemet.

    ![Databricks az Azure Portalon](../media/tutorials/azure-databricks-on-portal.png "Databricks az Azure Portalon")

3. A **Azure Databricks szolgáltatás**, adja meg a Databricks-munkaterület létrehozásához a következő értékeket:


    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../../azure-resource-manager/resource-group-overview.md). |
    |**Hely**     | Válassza ki **USA keleti RÉGIÓJA 2** vagy bármely más választható régiók a következők egyikét. Lásd: [elérhető Azure-szolgáltatások régió szerint](https://azure.microsoft.com/regions/services/) régiók rendelkezésre állása számára.        |
    |**Tarifacsomag**     |  Válassza a **Standard** vagy a **Prémium** előfizetést. Ne adja meg **próbaverzió**. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

    Kattintson a **Létrehozás** gombra.

4. A munkaterület létrehozása eltarthat néhány percig. 

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

1. Az Azure Portalon lépjen a létrehozott Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A program átirányítja az Azure Databricks portáljára. Válassza ki a portálról, **új fürt**.

    ![Databricks az Azure-on](../media/tutorials/databricks-on-azure.png "Databricks az Azure-on")

3. Az a **új fürt** lap, adja meg a fürt létrehozásához.

    ![Databricks Spark-fürt létrehozása az Azure-on](../media/tutorials/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-on")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

   * Adjon egy nevet a fürtnek.
   * Ebben a cikkben hozzon létre egy fürtöt **5.2** modul. Ne válassza **5.3** modul.
   * Győződjön meg arról, hogy a **leállítása után \_ \_ ennyi perc inaktivitás** jelölőnégyzet be van jelölve. Adja meg egy időtartamot (percben) a fürt leállításához, ha a fürt használja.

     Válassza a **Fürt létrehozása** lehetőséget. 
4. A fürt létrehozása néhány percet vesz igénybe. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

A tweetstream fogadásához létre kell hoznia egy alkalmazást a Twitteren. Kövesse a Twitter-alkalmazás létrehozására vonatkozó lépéseket, és jegyezze fel az értékeket, amelyek az oktatóanyag elvégzéséhez szükségesek.

1. Egy webböngészőben nyissa meg a [Twitter Application Management](https://apps.twitter.com/) oldalt, és válassza az **Új alkalmazás létrehozása** elemet.

    ![Twitter-alkalmazás létrehozása](../media/tutorials/databricks-create-twitter-app.png "Twitter-alkalmazás létrehozása")

2. Az **Alkalmazás létrehozása** oldalon adja meg az új alkalmazás adatait, majd válassza a **Twitter-alkalmazás létrehozása** parancsot.

    ![Twitter-alkalmazás részletei](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter-alkalmazás részletei")

3. Az alkalmazás oldalán válassza a **Kulcsok és hozzáférési tokenek** fület, és másolja a **Fogyasztói kulcs** és a **Fogyasztói titkos kulcs** mezők értékeit. Ezenkívül válassza a **Saját hozzáférési token létrehozása** lehetőséget a hozzáférési tokenek létrehozásához. Másolja a **Hozzáférési token** és a **Hozzáférési token titkos kulcsa** mező értékeit.

    ![Twitter-alkalmazás részletei](../media/tutorials/twitter-app-key-secret.png "Twitter-alkalmazás részletei")

Mentse a Twitter-alkalmazáshoz lekért értékeket. Az oktatóanyag későbbi részében szüksége lesz rájuk.

## <a name="attach-libraries-to-spark-cluster"></a>Kódtárak csatolása Spark-fürthöz

Ez az oktatóanyag bemutatja, hogyan küldhet tweeteket az Event Hubsnak a Twitter API-k segítségével. Ezenkívül az [Apache Spark Event Hubs-összekötő](https://github.com/Azure/azure-event-hubs-spark) segítségével adatokat olvashat be és írhat az Azure Event Hubsba. Ha az API-kat a fürt részeként kívánja használni, adja hozzá azokat kódtárként az Azure Databrickshez, majd társítsa a Spark-fürthöz. A következőkben megtudhatja, hogyan adhat hozzá a kódtárakat, hogy a **megosztott** a munkaterület mappájában.

1. Az Azure Databricks-munkaterületen válassza a **Munkaterület** lehetőséget, majd kattintson a jobb gombbal a **Megosztott** elemre. A helyi menüben kattintson a **Létrehozás** > **Kódtár** parancsra.

   ![Kódtár hozzáadása párbeszédpanel](../media/tutorials/databricks-add-library-option.png "Kódtár hozzáadása párbeszédpanel")

2. Az új kódtár oldalon a **forrás** kiválasztása **Maven**. A **koordinálja**, írja be a hozzáadni kívánt csomag koordinátáit. Az oktatóanyagban használt kódtárak Maven-koordinátái a következők:

   * Spark Event Hubs-összekötő – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API – `org.twitter4j:twitter4j-core:4.0.7`

     ![Maven-koordináták megadása](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Maven-koordináták megadása")

3. Kattintson a **Létrehozás** gombra.

4. Válassza ki a mappát, amelyhez hozzáadta a kódtárat, majd válassza ki a kódtár nevét.

    ![Hozzáadni kívánt kódtár kiválasztása](../media/tutorials/select-library.png "Hozzáadni kívánt kódtár kiválasztása")

5. Ha nem található a fürt nem a szalagtár lapján, válassza ki a **fürtök** , és futtassa a létrehozott fürt. Várjon, amíg az állapota "Fut" jeleníti meg, és ezután lépjen vissza a könyvtár oldalon.
A kódtár oldalán válassza ki a fürt, ahol szeretné a könyvtárban, és válassza ki **telepítése**. Miután a kódtár sikeresen társítva a fürthöz, annak állapota azonnal a **telepített**.

    ![Erőforrástár telepítése fürtre](../media/tutorials/databricks-library-attached.png "fürt telepítése kódtár")

6. Ismételje meg ezeket a lépéseket a Twitter-csomag (`twitter4j-core:4.0.7`) esetében is.

## <a name="get-a-cognitive-services-access-key"></a>Cognitive Services hozzáférési kulcs beszerzése

Ebben az oktatóanyagban használja a [Azure Cognitive Services Anomáliadetektálási detector használatával API-k](../overview.md) közel valós idejű tweetstream anomáliadetektálás futtathatók. Az API-k használatához hozzon létre egy Anomáliadetektálási detector használatával erőforrást az Azure-ban, és az Anomáliadetektálási detector használatával API-k hozzáférési kulcs lekérése.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza a **+ Erőforrás létrehozása** lehetőséget.

3. Azure Marketplace-en, válassza a **mesterséges Intelligencia és Machine Learning** > **összes** > **Cognitive Services – több**  >  **Anomáliadetektálási detector használatával**. Vagy használhat [erre a hivatkozásra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) , nyissa meg a **létrehozás** közvetlenül a párbeszédpanel bezárásához.

    ![Anomáliadetektálási detector használatával erőforrás létrehozása](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "erőforrás létrehozása Anomáliadetektálási detector használatával")

4. A **Létrehozás** párbeszédpanelen adja meg az alábbi értékeket:

    |Érték |Leírás  |
    |---------|---------|
    |Name (Név)     | Az Anomáliadetektálási detector használatával erőforrás nevét.        |
    |Előfizetés     | Az Azure-előfizetés az erőforrás társítva lesz.        |
    |Location egység     | Azure-helyen.        |
    |Tarifacsomag     | Egy tarifacsomagot a szolgáltatáshoz. Anomáliadetektálási detector használatával díjszabással kapcsolatos további információkért lásd: [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Erőforráscsoport     | Adja meg, hogy hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévőt.        |


     Kattintson a **Létrehozás** gombra.

5. Az erőforrás létrehozása után a a **áttekintése** lapra, másolja és mentse a **végpont** URL-CÍMÉT, a képernyőképen látható módon. Válassza ki **hozzáférési kulcsok megjelenítése**.

    ![Hozzáférési kulcsok megjelenítése](../media/tutorials/cognitive-services-get-access-keys.png "Hozzáférési kulcsok megjelenítése")

6. A **kulcsok**, válassza ki a használni kívánt kulcs a másolási ikonra. Mentse a hozzáférési kulcsot.

    ![Hozzáférési kulcsok másolása](../media/tutorials/cognitive-services-copy-access-keys.png "Hozzáférési kulcsok másolása")

## <a name="create-notebooks-in-databricks"></a>Jegyzetfüzetek létrehozása a Databricksben

Ebben a szakaszban két jegyzetfüzetet hoz létre a Databricks munkaterületen a következő nevekkel

- **SendTweetsToEventHub** – Előállítói jegyzetfüzet a tweetek beszerzésére a Twitterről, majd azok streamelésére az Event Hubsnak.
- **AnalyzeTweetsFromEventHub** – fogyasztói jegyzetfüzet tweetek olvasásához az Event Hubs és használhat az anomáliadetektálás futtatása.

1. Válassza ki az Azure Databricks-munkaterület **munkaterület** a bal oldali ablaktáblán. A **Munkaterület** legördülő menüjében válassza a **Létrehozás**, majd a **Jegyzetfüzet** elemet.

    ![Notebook létrehozása a Databricksben](../media/tutorials/databricks-create-notebook.png "Notebook létrehozása a Databricksben")

2. Az a **jegyzetfüzet létrehozása** párbeszédpanelen adja meg **SendTweetsToEventHub** névként, válassza ki a **Scala** , a nyelvet, és válassza a korábban létrehozott Spark-fürt.

    ![Notebook létrehozása a Databricksben](../media/tutorials/databricks-notebook-details.png "Notebook létrehozása a Databricksben")

    Kattintson a **Létrehozás** gombra.

3. Az **AnalyzeTweetsFromEventHub** jegyzetfüzet létrehozásához ismételje meg ezeket a lépéseket.

## <a name="send-tweets-to-event-hubs"></a>Tweetek küldése az Event Hubsnak

Az a **SendTweetsToEventHub** jegyzetfüzet, illessze be az alábbi kódot, és cserélje le a helyőrző értékek Event Hubs-névtér és Twitter-alkalmazás, amelyet korábban hozott létre. Ez a jegyzetfüzet létrehozásának időpontja és a "Például a" s száma kibontja az "Azure" kulcsszót tartalmazó tweeteket, és az Event Hubsba mindezek lehetőségként megjelennek események valós idejű adatfolyam.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

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
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

A jegyzetfüzet futtatásához használja a **SHIFT + ENTER** billentyűparancsot. A következő kódrészlethez hasonló kimenetnek kell megjelennie. A kimenetben szereplő minden esemény "Például a" s az Event hubsba betöltött száma és az időbélyegző kombinációját.

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Tweetek beolvasása az Event Hubsról

Az a **AnalyzeTweetsFromEventHub** jegyzetfüzetbe illessze be az alábbi kódot, és a helyőrzőt cserélje le a korábban létrehozott Anomáliadetektálási detector használatával erőforrásához tartozó értékeket. Ez a jegyzetfüzet beolvassa a tweeteket, amelyeket korábban az Event Hubsba streamelt a **SendTweetsToEventHub** jegyzetfüzet segítségével.

Ügyfél-Anomáliadetektálási detector használatával hívja először írása. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
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

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

A jegyzetfüzet futtatásához használja a **SHIFT + ENTER** billentyűparancsot. A következő kódrészlethez hasonló kimenetnek kell megjelennie.

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

Aggregátumfüggvény majd előkészítheti a későbbi használatra.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

A jegyzetfüzet futtatásához használja a **SHIFT + ENTER** billentyűparancsot. A következő kódrészlethez hasonló kimenetnek kell megjelennie.

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

Majd betölteni az adatokat az event hubs szolgáltatást. Értékek a korábban létrehozott Azure Event hubs a helyőrzőt cserélje.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

A kimenet a következő képen láthatóhoz. Vegye figyelembe, hogy a tábla a dátumot eltérhet ebben az oktatóanyagban dátum, mivel az adatok valós idejű.
![Betöltés adatokat az Event hubs](../media/tutorials/load-data-from-eventhub.png "terhelés adatokat az Event Hubs")

Streamelt adatokat az Azure Event Hubsból az Azure Databricksbe az Apache Spark az Event Hubs összekötője segítségével közel valós időben. A Spark Event Hubs-összekötőinek használatáról az [összekötő dokumentációjában](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs) talál további információt.



## <a name="run-anomaly-detection-on-tweets"></a>Anomáliadetektálás futtatása a tweeteken

Ebben a szakaszban végre anomáliadetektálás az Anomáliadetektálási érzékelő API használatával fogadott tweeteken. Ebben a szakaszban a kódrészleteket fogja hozzáadni ugyanazon **AnalyzeTweetsFromEventHub** jegyzetfüzethez.

Tegye a rendellenességek észlelése, hogy először a metrika darabszám összesítenie óránként.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
A kimenet a következő kódrészletek láthatóhoz.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Kérje le változások az összesített kimeneti eredmény. Anomáliadetektálás hosszabb okno historie igényel, mert a különbözeti használunk az észlelni kívánt pont előzmények adatok. Cserélje le a "[helyőrző: tábla neve]" a különbözeti minősített táblanév (például "tweets") kell létrehozni. Cserélje le "[helyőrző: ellenőrzőpontokat mappa neve]" karakterláncérték, amely egyedi minden alkalommal futtassa ezt a kódot (például "etl-a – az eventhub-20190605").
Az Azure databricks szolgáltatásban különbözeti Lake kapcsolatos további információkért tekintse meg a [különbözeti Lake útmutató](https://docs.azuredatabricks.net/delta/index.html)


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

Cserélje le a "[helyőrző: tábla neve]" fent választott különbözeti tábla ugyanazzal a névvel.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
A kimenet a következő: 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

Most már az összesített idősorozat-adatok folyamatosan be lett töltve a változásokat. Majd ütemezhet egy óránkénti feladat legutóbbi pont a rendellenességek észlelését. Cserélje le a "[helyőrző: tábla neve]" fent választott különbözeti tábla ugyanazzal a névvel.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Az eredmény, az alábbi: 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+


That's it! Using Azure Databricks, you have successfully streamed data into Azure Event Hubs, consumed the stream data using the Event Hubs connector, and then run anomaly detection on streaming data in near real time.
Although in this tutorial, the granularity is hourly, you can always change the granularity to meet your need. 

## Clean up resources

After you have finished running the tutorial, you can terminate the cluster. To do so, in the Azure Databricks workspace, select **Clusters** from the left pane. For the cluster you want to terminate, move the cursor over the ellipsis under **Actions** column, and select the **Terminate** icon and then select **Confirm**.

![Stop a Databricks cluster](../media/tutorials/terminate-databricks-cluster.png "Stop a Databricks cluster")

If you don't manually terminate the cluster it will automatically stop, provided you selected the **Terminate after \_\_ minutes of inactivity** checkbox while creating the cluster. In such a case, the cluster will automatically stop if it has been inactive for the specified time.

## Next steps

In this tutorial, you learned how to use Azure Databricks to stream data into Azure Event Hubs and then read the streaming data from Event Hubs in real time. Advance to the next tutorial to learn how to call the Anomaly Detector API and visualize anomalies using Power BI desktop. 

> [!div class="nextstepaction"]
>[Batch anomaly detection with Power BI desktop](batch-anomaly-detection-powerbi.md)
