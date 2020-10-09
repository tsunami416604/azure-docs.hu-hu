---
title: Cognitive Services Big Rea Scala-mintákhoz
description: A MMLSpark folyamat futtatásához használja a Azure Databricks Cognitive Services a big data.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 4546ef03c82f19d188a71a86f6964ca87c0f834e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90524963"
---
# <a name="quick-examples"></a>Gyors példák

A következő kódrészletek készen állnak a futtatásra, és segítséget nyújtanak a Cognitive Services használatának megkezdésében a Sparkon. Az alábbi minták a Scala-ban találhatók.

A minták ezeket a Cognitive Services használják:

- Text Analytics – megszerezheti a mondatok egy halmazának hangulatát (vagy hangulatát).
- Computer Vision – beolvassa a képek egy halmazához társított címkéket (egyszavas leírásokat).
- Bing Image Search – megkeresheti a weben a természetes nyelvű lekérdezéshez kapcsolódó rendszerképeket.
- Beszéd – szöveg – hangfájlok átírása szöveg-alapú átiratok kinyeréséhez.
- Anomália-detektor – a rendellenességek észlelése egy idősorozat-adathalmazon belül.

## <a name="prerequisites"></a>Előfeltételek

1. A Azure Databricks és Cognitive Services környezet beállításához kövesse az [első lépések](getting-started.md) című témakör lépéseit. Ez az oktatóanyag bemutatja, hogyan telepítheti a MMLSpark, és hogyan hozhatja létre a Spark-fürtöt a Databricks-ben.
1. Miután létrehozott egy új jegyzetfüzetet a Azure Databricksban, másolja az alábbi **megosztott kódot** , és illessze be egy új cellába a jegyzetfüzetbe.
1. Válasszon egy szolgáltatási mintát, és másolja be a vágólapra a jegyzetfüzet második új cellájába.
1. Cserélje le a szolgáltatás előfizetési kulcsának helyőrzőit a saját kulcsára.
1. Kattintson a Futtatás gombra (háromszög ikon) a cella jobb felső sarkában, majd válassza a **cella futtatása**lehetőséget.
1. A cella alatti táblázat eredményeinek megtekintése.

## <a name="shared-code"></a>Megosztott kód
Első lépésként adja hozzá ezt a kódot a projekthez:

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="text-analytics"></a>Szövegelemzés

A [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) szolgáltatás számos algoritmust biztosít az intelligens adatok szövegből való kinyeréséhez. Megtalálhatja például az adott bemeneti szöveg hangulatát. A szolgáltatás egy pontszámot ad vissza `0.0` , és `1.0` ahol az alacsony pontszámok értéke negatív, a magas pontszámot pedig pozitív hangulatra utal.  Az alábbi minta három egyszerű mondatot használ, és az egyes értékekhez tartozó hangulati pontszámot adja vissza.

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Várt eredmény

| szöveg                                      | hangulatelemzés                                             |
|:------------------------------------------|:------------------------------------------------------|
| Boldog vagyok, ma, a napsütötte!           | 0.9789592027664185                                    |
| Ez a Rush Hour-forgalom csalódott vagyok. | 0.023795604705810547                                  |
| A sparkon nem rossz a kognitív szolgáltatások  | 0.8888956308364868                                    |

## <a name="computer-vision"></a>Számítógépes látástechnológia

[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) a lemezképek elemzésével azonosíthatja a struktúrát, például az arcokat, az objektumokat és a természetes nyelvi leírásokat.
Ebben a példában a képek listáját címkézjük. A címkék egyszavas leírások a képen látható dolgokról, például a felismerhető objektumokról, a személyekről, a díszletekről és a műveletekről.

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>Várt eredmény

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ["korcsolyázó" "személy" "ember", "lovaglás" "sport" "gördeszka" "Young" "Board" ing "' Air" "The the" The "The" The "The" The "The" The "The" The "The" The "The" The "The" The "The" The "The" The "The" The "
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ["Dog" "kültéri" "kerítés" "faszerkezetes" "kis" "barna" "épület" ülő "" a "kiépítés" a "pad" állandó "tábla" a "Walking" "Board" "Beach                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ["a" fű "ház" a "régi" "Home" "kezdő" "kisméretű" "templom" kő "nagy" ("legeltetés") "udvar", "zöld", "a" vezető "" bárány "tégla" a pad "" utca "fehér" ország "óra" "a" parkolt "" a "kihelyezett" mező "a" Garden "Water" "piros" ló "férfi" "magas" "Fire" csoport "] 

## <a name="bing-image-search"></a>Bing – Képkeresés

[Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview) megkeresi a weben a felhasználó természetes nyelvi lekérdezéséhez kapcsolódó rendszerképek lekéréséhez. Ebben a példában egy szöveges lekérdezést használunk, amely idézőjelekkel rendelkező képeket keres. A lekérdezéshez kapcsolódó fényképeket tartalmazó kép URL-címeinek listáját adja vissza.


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>Várt eredmény

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>Speech-to-Text

A [beszédfelismerési](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) szolgáltatás szöveggé alakítja át a hanganyagot vagy a beszélt hangon lévő fájlokat. Ebben a példában két hangfájlt teszünk át. Az első fájl könnyen értelmezhető, a második pedig nagyobb kihívást jelent.

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>Várt eredmény

| url | Szöveg |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Az egyéni beszédfelismerés olyan eszközöket biztosít, amelyekkel vizuálisan ellenőrizheti a modell felismerési minőségét a hangadatok összehasonlításával az egyéni Speech Portal megfelelő felismerési eredményeivel. A feltöltött hang lejátszásával megállapíthatja, hogy a megadott felismerési eredmény helyes-e. Ezzel az eszközzel gyorsan megvizsgálhatja a Microsoft alapkonfigurációs beszédét szöveges modellre vagy egy betanított egyéni modellre anélkül, hogy hangadatokat kellene átírnia. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Vegyen fel egy Gentleman Sir gondolkodás vizuális ellenőrzését.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Hallom. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Szeretem a rádióhoz való megnyugtatást, amit én is hallottam. |

## <a name="anomaly-detector"></a>Anomáliadetektor

Az [anomália detektor](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/) kiválóan használható a szabálytalanságok észlelésére az idősorozat-adataiban. Ebben a példában a szolgáltatást használjuk a teljes idősorozatban található anomáliák keresésére.

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>Várt eredmény

| időbélyeg            |   érték | isAnomaly   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | Hamis       |
| 1972-02-01T00:00:00Z |     799 | Hamis       |
| 1972-03-01T00:00:00Z |     890 | Hamis       |
| 1972-04-01T00:00:00Z |     900 | Hamis       |
| 1972-05-01T00:00:00Z |     766 | Hamis       |
| 1972-06-01T00:00:00Z |     805 | Hamis       |
| 1972-07-01T00:00:00Z |     821 | Hamis       |
| 1972-08-01T00:00:00Z |   20000 | Igaz        |
| 1972-09-01T00:00:00Z |     883 | Hamis       |
| 1972-10-01T00:00:00Z |     898 | Hamis       |
| 1972-11-01T00:00:00Z |     957 | Hamis       |
| 1972-12-01T00:00:00Z |     924 | Hamis       |
| 1973-01-01T00:00:00Z |     881 | Hamis       |
| 1973-02-01T00:00:00Z |     837 | Hamis       |
| 1973-03-01T00:00:00Z |    9000 | Igaz        |
