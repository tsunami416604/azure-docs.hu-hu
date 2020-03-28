---
title: 'Oktatóanyag: Anomália-észlelés az Azure Databricks használatával történő adatfolyam-adatfolyamokon'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az Anomália-detektor API-t és az Azure Databricks-t az adatok anomáliáinak figyeléséhez.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: e0df0773daf8f9be21ac70d8390013adfd93483a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78402668"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Oktatóanyag: Anomália-észlelés az Azure Databricks használatával történő adatfolyam-adatfolyamokon

[Az Azure Databricks](https://azure.microsoft.com/services/databricks/) egy gyors, egyszerű és együttműködő Apache Spark-alapú elemzési szolgáltatás. Az Azure Cognitive Services részét, az Anomáliadetektor API segítségével figyelheti az idősorozat-adatokat. Az oktatóanyag használatával anomáliafelismerést közel valós időben futtatja egy adatfolyamon az Azure Databricks használatával. A Twitter-adatokat az Azure Event Hubs használatával fogja bebetöltésre, és importálja őket az Azure Databricks-be a Spark Event Hubs-összekötő használatával. Ezt követően az API-t fogja használni az adatfolyamon lévő adatok anomáliák észlelésére. 

Az alábbi ábrán az alkalmazásfolyam látható:

![Azure Databricks az Event Hubs és a Cognitive Services szolgáltatással](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks az Event Hubs és a Cognitive Services szolgáltatással")

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Databricks-munkaterület létrehozása
> * Spark-fürt létrehozása az Azure Databricksben
> * Twitter-alkalmazás létrehozása a streamadatok eléréséhez
> * Jegyzetfüzetek létrehozása az Azure Databricksben
> * Kódtárak csatlakoztatása az Event Hubshoz és a Twitter API-hoz
> * Anomáliadetektor-erőforrás létrehozása és a hozzáférési kulcs beolvasása
> * Tweetek küldése az Event Hubsnak
> * Tweetek beolvasása az Event Hubsról
> * Anomáliaészlelés futtatása tweeteken

> [!Note]
> * Ez az oktatóanyag bemutatja az anomáliadetektor API ajánlott [megoldásarchitektúrájának](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) megvalósítását.
> * Ez az oktatóanyag nem hajtható végre az Anomaly Detector API vagy az Azure Databricks ingyenes próbaverziójával. 

Hozzon létre egy [Azure-előfizetést,](https://azure.microsoft.com/free/) ha nem rendelkezik ilyensel.

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure Event Hubs névtér](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) és az eseményközpont.

- Az [connection string](../../../event-hubs/event-hubs-get-connection-string.md) Event Hubs névtér eléréséhez. A kapcsolati karakterláncnak a következőhöz hasonló formátumúnak kell lennie:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- Az Event Hubs megosztott hozzáférési házirendneve és házirendkulcsa.

Tekintse meg az Azure Event Hubs [rövid útmutató](../../../event-hubs/event-hubs-create.md) a névtér és az eseményközpont létrehozásáról.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban hozzon létre egy Azure Databricks-munkaterületet az [Azure Portal](https://portal.azure.com/)használatával.

1. Az Azure Portalon válassza az**Analytics** > Erőforrás > létrehozása**Azure Databricks** **lehetőséget.**

    ![Databricks az Azure Portalon](../media/tutorials/azure-databricks-on-portal.png "Databricks az Azure Portalon")

3. Az **Azure Databricks Service alatt**adja meg a következő értékeket a Databricks-munkaterület létrehozásához:


    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../../azure-resource-manager/management/overview.md). |
    |**Helyen**     | Válassza **az USA keleti régiója 2** vagy bármely más rendelkezésre álló régió egyikét. Tekintse meg a [régiónként elérhető Azure-szolgáltatásokat](https://azure.microsoft.com/regions/services/) a régió elérhetőségéhez.        |
    |**Árképzési szint**     |  Válassza a **Standard** vagy a **Prémium** előfizetést. Ne válassza **a Próbaverzió**lehetőséget. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

    Kattintson a **Létrehozás** gombra.

4. A munkaterület létrehozása eltarthat néhány percig. 

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

1. Az Azure Portalon lépjen a létrehozott Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A rendszer átirányítja az Azure Databricks-portálra. A portálon válassza az **Új fürt**lehetőséget.

    ![Databricks az Azure-ban](../media/tutorials/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához megadott értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](../media/tutorials/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

   * Adjon egy nevet a fürtnek.
   * Ebben a cikkben hozzon létre egy fürtöt **5.2-es** futásidejű. NE válassza az **5.3-as** futásidőt.
   * Győződjön meg arról, hogy a **Percek \_ \_ utáni inaktivitás** jelölőnégyzet be van jelölve. Adjon meg egy időtartamot (percben) a fürt leállításához, ha a fürt nincs használatban.

     Válassza a **Fürt létrehozása** lehetőséget. 
4. A fürt létrehozása néhány percet vesz igénybe. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

A tweetstream fogadásához létre kell hoznia egy alkalmazást a Twitteren. Kövesse a Twitter-alkalmazás létrehozására vonatkozó lépéseket, és jegyezze fel az értékeket, amelyek az oktatóanyag elvégzéséhez szükségesek.

1. Egy webböngészőben nyissa meg a [Twitter Application Management](https://apps.twitter.com/) oldalt, és válassza az **Új alkalmazás létrehozása** elemet.

    ![Twitter alkalmazás létrehozása](../media/tutorials/databricks-create-twitter-app.png "Twitter alkalmazás létrehozása")

2. Az **Alkalmazás létrehozása** oldalon adja meg az új alkalmazás adatait, majd válassza a **Twitter-alkalmazás létrehozása** parancsot.

    ![Twitter alkalmazás részletei](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter alkalmazás részletei")

3. Az alkalmazás oldalán válassza a **Kulcsok és hozzáférési tokenek** fület, és másolja a **Fogyasztói kulcs** és a **Fogyasztói titkos kulcs** mezők értékeit. Ezenkívül válassza a **Saját hozzáférési token létrehozása** lehetőséget a hozzáférési tokenek létrehozásához. Másolja a **Hozzáférési token** és a **Hozzáférési token titkos kulcsa** mező értékeit.

    ![Twitter alkalmazás részletei](../media/tutorials/twitter-app-key-secret.png "Twitter alkalmazás részletei")

Mentse a Twitter-alkalmazáshoz lekért értékeket. Az oktatóanyag későbbi részében szüksége lesz rájuk.

## <a name="attach-libraries-to-spark-cluster"></a>Kódtárak csatolása Spark-fürthöz

Ez az oktatóanyag bemutatja, hogyan küldhet tweeteket az Event Hubsnak a Twitter API-k segítségével. Ezenkívül az [Apache Spark Event Hubs-összekötő](https://github.com/Azure/azure-event-hubs-spark) segítségével adatokat olvashat be és írhat az Azure Event Hubsba. Ha az API-kat a fürt részeként kívánja használni, adja hozzá azokat kódtárként az Azure Databrickshez, majd társítsa a Spark-fürthöz. Az alábbi utasítások bemutatják, hogyan veheti fel a tárakat a munkaterület **megosztott** mappájába.

1. Az Azure Databricks-munkaterületen válassza a **Munkaterület** lehetőséget, majd kattintson a jobb gombbal a **Megosztott** elemre. A helyi menüben válassza a Tár **létrehozása parancsot.** > **Library**

   ![Tár hozzáadása párbeszédpanel](../media/tutorials/databricks-add-library-option.png "Tár hozzáadása párbeszédpanel")

2. Az Új könyvtár lapon a **Forrás** területen válassza a **Maven**lehetőséget. A **Koordináták**mezőbe írja be a hozzáadni kívánt csomag koordinátáta. Az oktatóanyagban használt kódtárak Maven-koordinátái a következők:

   * Spark Event Hubs-összekötő – `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API – `org.twitter4j:twitter4j-core:4.0.7`

     ![Maven koordináták megadására](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Maven koordináták megadására")

3. Kattintson a **Létrehozás** gombra.

4. Válassza ki a mappát, amelyhez hozzáadta a kódtárat, majd válassza ki a kódtár nevét.

    ![A hozzáadni kívánt tár kiválasztása](../media/tutorials/select-library.png "A hozzáadni kívánt tár kiválasztása")

5. Ha a tárlapnem található fürt, válassza a Fürtök lehetőséget, és **futtassa** a létrehozott fürtöt. Várjon, amíg az állapot a "Futás" jelenik meg, majd lépjen vissza a könyvtár lapra.
A tár lapon jelölje ki azt a fürtöt, amelyben használni szeretné a tárat, majd kattintson a **Telepítés gombra.** Miután a tár sikeresen társítva lett a fürthöz, az állapot azonnal Telepített állapotra **változik.**

    ![Tár telepítése fürtre](../media/tutorials/databricks-library-attached.png "Tár telepítése fürtre")

6. Ismételje meg ezeket a lépéseket a Twitter-csomag (`twitter4j-core:4.0.7`) esetében is.

## <a name="get-a-cognitive-services-access-key"></a>Cognitive Services hozzáférési kulcs beszerzése

Ebben az oktatóanyagban az [Azure Cognitive Services anomáliadetektor API-k](../overview.md) használatával közel valós időben futtathatja az anomáliaészlelést a tweetek adatfolyamán. Az API-k használata előtt létre kell hoznia egy anomáliadetektor-erőforrást az Azure-ban, és be kell olvasnia egy hozzáférési kulcsot az Anomália-detektor API-k használatához.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Válassza a **+ Erőforrás létrehozása** lehetőséget.

3. Az Azure Piactér csoportban válassza az **AI + Machine Learning** > **lehetőséget az összes** > **kognitív szolgáltatás – további** > **anomáliadetektor megtekintése.** Vagy használhatja [ezt a hivatkozást,](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) hogy közvetlenül a **Létrehozás** párbeszédpanelre lépjen.

    ![Anomáliadetektor-erőforrás létrehozása](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Anomáliadetektor-erőforrás létrehozása")

4. A **Létrehozás** párbeszédpanelen adja meg az alábbi értékeket:

    |Érték |Leírás  |
    |---------|---------|
    |Név     | Az Anomáliadetektor erőforrás neve.        |
    |Előfizetés     | Az erőforráshoz tartozó Azure-előfizetéshez lesz társítva.        |
    |Hely     | Egy Azure-hely.        |
    |Tarifacsomag     | A szolgáltatás tarifacsomagja. Az Anomáliadetektor oktatásáról további információt az [árképzési oldalon](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/)talál.        |
    |Erőforráscsoport     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt kíván-e kijelölni.        |


     Kattintson a **Létrehozás** gombra.

5. Az erőforrás létrehozása után az **Áttekintés** lapon másolja és mentse a **végpont** URL-címét, ahogy az a képernyőképen látható. Ezután válassza **a Hozzáférési kulcsok megjelenítése lehetőséget.**

    ![Hozzáférési kulcsok megjelenítése](../media/tutorials/cognitive-services-get-access-keys.png "Hozzáférési kulcsok megjelenítése")

6. A **Billentyűk**csoportban jelölje ki a másolásikont a használni kívánt kulcson. Mentse a hozzáférési kulcsot.

    ![Hozzáférési kulcsok másolása](../media/tutorials/cognitive-services-copy-access-keys.png "Hozzáférési kulcsok másolása")

## <a name="create-notebooks-in-databricks"></a>Jegyzetfüzetek létrehozása a Databricksben

Ebben a szakaszban két jegyzetfüzetet hoz létre a Databricks munkaterületen a következő nevekkel

- **SendTweetsToEventHub** – Előállítói jegyzetfüzet a tweetek beszerzésére a Twitterről, majd azok streamelésére az Event Hubsnak.
- **AnalyzeTweetsFromEventHub** - Egy fogyasztói notebook segítségével olvassa el a tweets az Event Hubs és anomália észlelése.

1. Az Azure Databricks-munkaterületen válassza a **Munkaterület** a bal oldali ablaktáblában. A **Munkaterület** legördülő menüjében válassza a **Létrehozás**, majd a **Jegyzetfüzet** elemet.

    ![Jegyzetfüzet létrehozása a Databricks-ben](../media/tutorials/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

2. A **Jegyzetfüzet létrehozása** párbeszédpanelen írja be **a SendTweetsToEventHub** nevet, válassza a **Scala** nyelvet, és válassza ki a korábban létrehozott Spark-fürtöt.

    ![Jegyzetfüzet létrehozása a Databricks-ben](../media/tutorials/databricks-notebook-details.png "Jegyzetfüzet létrehozása a Databricks-ben")

    Kattintson a **Létrehozás** gombra.

3. Az **AnalyzeTweetsFromEventHub** jegyzetfüzet létrehozásához ismételje meg ezeket a lépéseket.

## <a name="send-tweets-to-event-hubs"></a>Tweetek küldése az Event Hubsnak

A **SendTweetsToEventHub** notebook, illessze be a következő kódot, és cserélje le a helyőrző értékeket az Event Hubs névtér és a Twitter alkalmazás, amely korábban létrehozott. Ez a jegyzetfüzet kivonja a létrehozási időt és a "Like" számú tweetek a kulcsszó "Azure", és streameli azokat, mint események Event Hubs valós időben.

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

A jegyzetfüzet futtatásához használja a **SHIFT + ENTER** billentyűparancsot. A következő kódrészlethez hasonló kimenetnek kell megjelennie. A kimenet minden esemény időbélyegének és az Event Hubs-ba betöltött "Like" számának kombinációja.

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

Az **AnalyzeTweetsFromEventHub** jegyzetfüzetbe illessze be a következő kódot, és cserélje le a helyőrzőt a korábban létrehozott Anomaly Detector erőforrás értékeire. Ez a jegyzetfüzet beolvassa a tweeteket, amelyeket korábban az Event Hubsba streamelt a **SendTweetsToEventHub** jegyzetfüzet segítségével.

Először írjon egy ügyfelet az Anomáliadetektor hívásához. 
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

Ezután készítsen elő egy összesítési függvényt a későbbi használatra.
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

Ezután töltse be az adatokat az eseményközpontból az anomáliadetektáláshoz. Cserélje le a helyőrzőt a korábban létrehozott Azure Event Hubs értékeire.

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

A kimenet most hasonlít az alábbi képre. Vegye figyelembe, hogy a táblázatban szereplő dátum eltérhet az oktatóanyagban szereplő dátumtól, mivel az adatok valós idejűek.
![Adatok betöltése az eseményközpontból](../media/tutorials/load-data-from-eventhub.png "Adatok betöltése az eseményközpontból")

Az Azure Event Hubs-ból az Azure Databricks-be közel valós időben streamelte az adatokat az Apache Spark Event Hubs-összekötőhasználatával. A Spark Event Hubs-összekötőinek használatáról az [összekötő dokumentációjában](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs) talál további információt.



## <a name="run-anomaly-detection-on-tweets"></a>Anomáliaészlelés futtatása tweeteken

Ebben a szakaszban anomáliaészlelést futtat az Anomáliadetektor API-val kapott tweeteken. Ebben a szakaszban a kódrészleteket fogja hozzáadni ugyanazon **AnalyzeTweetsFromEventHub** jegyzetfüzethez.

Az anomáliadetektáláshoz először a metrikák számát óránként kell összesítenie.
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
A kimenet most hasonlít a következő kódrészletek.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Ezután az összesített kimeneti eredmény a Delta. Mivel az anomáliadetektálás hosszabb előzményablakot igényel, a Delta segítségével őrizzük meg a észlelni kívánt pont előzményadatait. Cserélje le a "[Helyőrző: tábla neve]" szót egy létrehozandó delta tábla nevére (például "tweet"). Cserélje le az "[Placeholder: folder name for checkpoints]" karakterlánc-értéket, amely minden alkalommal egyedi, amikor ezt a kódot futtatja (például "etl-from-eventhub-20190605").
Ha többet szeretne megtudni a Delta-tó az Azure Databricks, kérjük, olvassa el a [Delta Lake Guide](https://docs.azuredatabricks.net/delta/index.html)


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

Cserélje le a ([Helyőrző: tábla neve]" táblát a fent kiválasztott Delta táblanévre.
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
A kimenet az alábbiak szerint: 
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

Most az összesített idősorozat-adatok folyamatosan a Deltába kerülnek. Ezután ütemezhet egy óránkénti feladatot a legutóbbi pont anomáliájának észleléséhez. Cserélje le a ([Helyőrző: tábla neve]" táblát a fent kiválasztott Delta táblanévre.

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
Eredmény az alábbiak szerint: 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

Ennyi az egész! Az Azure Databricks használatával sikeresen streamelte az adatokat az Azure Event Hubs-ba, felhasználta az adatfolyam-adatokat az Event Hubs-összekötő használatával, majd közel valós időben futtatta az anomáliafelismerést a streamelési adatokon.
Bár ebben az oktatóanyagban a részletesség óránként imitátum, bármikor módosíthatja a granularitást, hogy megfeleljen az Ön igényének. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után leállíthatja a fürtöt. Ehhez az Azure Databricks munkaterületen válassza **a fürtök** a bal oldali ablaktáblából. A lezárni kívánt fürt esetében vigye a kurzort a **műveletek** oszlop három pontra, majd kattintson a **Leállítás** ikonra, majd a **Megerősítés gombra.**

![Databricks-fürt leállítása](../media/tutorials/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha nem szakítja meg manuálisan a fürtöt, akkor automatikusan leáll, feltéve, hogy a fürt létrehozásakor bejelölte a **Percek \_ \_ utáni inaktivitás** után jelölőnégyzetet. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan használhatja az Azure Databricks szolgáltatást az adatok Azure Event Hubsra való streamelésére, és hogyan olvashatja valós időben a streamelt adatokat az Event Hubsról. A következő oktatóanyagra lépve megtudhatja, hogyan hívhatja meg az Anomália-detektor API-t, és hogyan jelenítheti meg az anomáliákat a Power BI desktop használatával. 

> [!div class="nextstepaction"]
>[Kötegelt anomáliaészlelés a Power BI desktopsegítségével](batch-anomaly-detection-powerbi.md)
