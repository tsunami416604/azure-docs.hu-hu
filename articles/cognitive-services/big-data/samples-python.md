---
title: Cognitive Services Big adatmennyiség – Python-minták
description: Próbálja ki Cognitive Services mintákat a Pythonban Azure Databricks a MMLSpark-folyamat futtatásához big data.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: caf492c2cd3940fd7f37e2a4462c8376a127f393
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86189815"
---
# <a name="python-samples-for-cognitive-services-for-big-data"></a>Python-minták Cognitive Services Big-adatmennyiséghez

A következő kódrészletek készen állnak a futtatásra, és segítséget nyújtanak a Cognitive Services a Spark és a Python használatával való használatának megkezdésében.

A cikkben szereplő minták ezeket a Cognitive Services használják:

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

A kezdéshez ezt a kódot hozzá kell adnia a projekthez:

```python
from mmlspark.cognitive import *

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key = "ADD_YOUR_SUBSCRIPION_KEY"
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY"
# An Anomaly Dectector subscription key
anomaly_key = "ADD_YOUR_SUBSCRIPION_KEY"

# Validate the key
assert service_key != "ADD_YOUR_SUBSCRIPION_KEY"
```    

## <a name="text-analytics-sample"></a>Text Analytics minta

A [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) szolgáltatás számos algoritmust biztosít az intelligens adatok szövegből való kinyeréséhez. Megtalálhatja például az adott bemeneti szöveg hangulatát. A szolgáltatás 0,0 és 1,0 közötti pontszámot ad vissza, ahol az alacsony pontszámok negatív hangulatot és magas pontszámot jeleznek a pozitív hangulat alapján.  Ez a példa három egyszerű mondatot használ, és visszaadja az egyes minták hangulatát.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format
display(sentiment.transform(df).select("text", col("sentiment")[0].getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>Várt eredmény

| szöveg                                      | hangulatelemzés                                             |
|:------------------------------------------|:------------------------------------------------------|
| Boldog vagyok, ma, a napsütötte!           | 0.9789592027664185                                    |
| Ez a Rush Hour-forgalom csalódott vagyok. | 0.023795604705810547                                  |
| A sparkon nem rossz a kognitív szolgáltatások  | 0.8888956308364868                                    |

## <a name="computer-vision-sample"></a>Computer Vision minta

[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) a lemezképek elemzésével azonosíthatja a struktúrát, például az arcokat, az objektumokat és a természetes nyelvi leírásokat. Ebben a példában a képek listáját címkézjük. A címkék egyszavas leírások a képen látható dolgokról, például a felismerhető objektumokról, a személyekről, a díszletekről és a műveletekről.

```python

# Create a dataframe with the image URLs
df = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select("image", "analysis_results.description.tags"))
```

### <a name="expected-result"></a>Várt eredmény

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ["korcsolyázó" "személy" "ember", "lovaglás" "sport" "gördeszka" "Young" "Board" ing "' Air" "The the" The "The" The "The" The "The" The "The" The "The" The "The" The "The" The "The" The "The" The "The" The "
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ["Dog" "kültéri" "kerítés" "faszerkezetes" "kis" "barna" "épület" ülő "" a "kiépítés" a "pad" állandó "tábla" a "Walking" "Board" "Beach                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ["a" fű "ház" a "régi" "Home" "kezdő" "kisméretű" "templom" kő "nagy" ("legeltetés") "udvar", "zöld", "a" vezető "" bárány "tégla" a pad "" utca "fehér" ország "óra" "a" parkolt "" a "kihelyezett" mező "a" Garden "Water" "piros" ló "férfi" "magas" "Fire" csoport "]


## <a name="bing-image-search-sample"></a>Bing Image Search minta

[Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview) megkeresi a weben a felhasználó természetes nyelvi lekérdezéséhez kapcsolódó rendszerképek lekéréséhez. Ebben a példában egy szöveges lekérdezést használunk, amely idézőjelekkel rendelkező képeket keres. A lekérdezéshez kapcsolódó fényképeket tartalmazó kép URL-címeinek listáját adja vissza.

```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 10
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(100)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bing_search_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")

# This displays the full results returned, uncomment to use
# display(bingSearch.transform(bingParameters))

# Since we have two services, they are put into a pipeline
pipeline = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
display(pipeline.transform(bingParameters))
```

### <a name="expected-result"></a>Várt eredmény

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |


## <a name="speech-to-text-sample"></a>Beszéd – szöveg minta
A [beszédfelismerési](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) szolgáltatás szöveggé alakítja át a hanganyagot vagy a beszélt hangon lévő fájlokat. Ebben a példában két hangfájlt teszünk át. Az első fájl könnyen értelmezhető, a második pedig nagyobb kihívást jelent.

```python

# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",),
                           ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```

### <a name="expected-result"></a>Várt eredmény

| url | Szöveg |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Az egyéni beszédfelismerés olyan eszközöket biztosít, amelyekkel vizuálisan ellenőrizheti a modell felismerési minőségét a hangadatok összehasonlításával az egyéni Speech Portal megfelelő felismerési eredményeivel. A feltöltött hang lejátszásával megállapíthatja, hogy a megadott felismerési eredmény helyes-e. Ezzel az eszközzel gyorsan megvizsgálhatja a Microsoft alapkonfigurációs beszédét szöveges modellre vagy egy betanított egyéni modellre anélkül, hogy hangadatokat kellene átírnia. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Vegyen fel egy Gentleman Sir gondolkodás vizuális ellenőrzését.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Hallom. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Szeretem a rádióhoz való megnyugtatást, amit én is hallottam. |


## <a name="anomaly-detector-sample"></a>Rendellenesség-érzékelő minta

Az [anomália detektor](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/) kiválóan használható a szabálytalanságok észlelésére az idősorozat-adataiban. Ebben a példában a szolgáltatást használjuk a teljes idősorozatban található anomáliák keresésére.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df = spark.createDataFrame([
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
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomaly_key)
  .setLocation("eastus")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
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

## <a name="arbitrary-web-apis"></a>Tetszőleges webes API-k

A "HTTP on Spark" szolgáltatásban bármely webszolgáltatás használható a big data folyamatában. Ebben a példában a [Világbank API](http://api.worldbank.org/v2/country/) -t használjuk a világ különböző országaival kapcsolatos információk beszerzéséhez.

```python
from requests import Request
from mmlspark.io.http import HTTPTransformer, http_udf
from pyspark.sql.functions import udf, col

# Use any requests from the python requests library
def world_bank_request(country):
  return Request("GET", "http://api.worldbank.org/v2/country/{}?format=json".format(country))

# Create a dataframe with spcificies which countries we want data on
df = (spark.createDataFrame([("br",),("usa",)], ["country"])
  .withColumn("request", http_udf(world_bank_request)(col("country"))))

# Much faster for big data because of the concurrency :)
client = (HTTPTransformer()
      .setConcurrency(3)
      .setInputCol("request")
      .setOutputCol("response"))

# Get the body of the response
def get_response_body(resp):
  return resp.entity.content.decode()

# Show the details of the country data returned
display(client.transform(df).select("country", udf(get_response_body)(col("response")).alias("response")))
```

### <a name="expected-result"></a>Várt eredmény

| ország   | válasz |
|:----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| br | [{"oldal": 1, "Pages": 1, "per_page": "50", "Total": 1}, [{"id": "MELLTARTÓ", "iso2Code": "BR", "Name": "Brazília", "régió": {"id": "LCN", "iso2Code": "ZJ", "value": "Latin-Amerika & Caribbean"}, "adminregion": {"id": "LAC", "iso2Code": "XJ", "value": "Latin-Amerika & Caribbean (a magas jövedelmek kivételével)"}, "incomeLevel": {"id": "UMC", "iso2Code": "XT", "value": "felső közepes bevétel"}, "lendingType": {"azonosító": "IBD", "iso2Code": "XF", "value": "IBRD"}, "capitalCity": "Brasilia", "hosszúság": "-47.9292", "Latitude": "-15,7801"}]] |
| USA  | [{"oldal": 1, "Pages": 1, "per_page": "50", "összesen": 1}, [{"id": "USA", "iso2Code": "US", "név": "Egyesült Államok", "Region": {"id": "NAC", "iso2Code": "XU", "value": "Észak-Amerika"}, "adminregion": {"id": "", "iso2Code": "", "value": ""}, "incomeLevel": {"id": "HIC", "iso2Code": "XD", "value": "magas bevétel"}, "lendingType": {"id": "LNX", "iso2Code": "XX", "value": "not besorolt"}, "capitalCity": "Washington D.C.", "hosszúság": "-77.032", "Latitude": "38.8895"}]] |

## <a name="see-also"></a>Lásd még

* [Recept: anomáliák észlelése](./recipes/anomaly-detection.md)
* [Recept: Art Explorer](./recipes/art-explorer.md)
