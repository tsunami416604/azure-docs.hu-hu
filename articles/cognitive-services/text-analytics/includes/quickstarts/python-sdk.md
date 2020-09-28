---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 09/21/2020
ms.author: aahi
ms.openlocfilehash: 3fa9683b63f94f740c19d45865dab9b6d5a08d3f
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400973"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

a [v 3.1 dokumentációja](https://docs.microsoft.com/python/api/azure-ai-textanalytics/azure.ai.textanalytics?view=azure-python-preview&preserve-view=true)  |  [v 3.1 függvénytár-forráskód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics)  |  [v 3.1 csomag (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [v 3.1 minta](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

a [v3-referenciák dokumentációja](https://aka.ms/azsdk-python-textanalytics-ref-docs)  |  [v3 függvénytár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics)  |  [v3 csomag (PiPy)](https://pypi.org/project/azure-ai-textanalytics/)  |  [v3 minta](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

[v2 dokumentáció](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics)  |  [v2 függvénytár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics)  |  [v2 csomag (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/)  |  [v2 minta](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" hozzon létre egy Text Analytics erőforrást, "  target="_blank"> és hozzon létre egy Text Analytics-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Text Analytics APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

```console
pip install azure-ai-textanalytics --pre
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), amely a jelen rövid útmutatóban szereplő példákat tartalmazza. 

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), amely a jelen rövid útmutatóban szereplő példákat tartalmazza. 

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py), amely a jelen rövid útmutatóban szereplő példákat tartalmazza. 

---

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-fájlt, és hozzon létre változókat az erőforrás Azure-végpontjának és előfizetési kulcsának létrehozásához.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektummodell

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Az Text Analytics ügyfél egy olyan `TextAnalyticsClient` objektum, amely az Azure-ban hitelesíti magát. Az ügyfél számos módszert biztosít a szöveg elemzéséhez. 

Ha a feldolgozás szövege az API-ra kerül a `documents` (z) listájára, amely a karakterláncok listája, a dict-szerű ábrázolás vagy a listája `TextDocumentInput/DetectLanguageInput` . Egy objektum a, a `dict-like` és a kombinációját tartalmazza `id` `text` `language/country_hint` . Az `text` attribútum tárolja a forrásban elemezni kívánt szöveget `country_hint` , és a `id` értéke bármilyen lehet. 

A válasz objektum az egyes dokumentumok elemzési információit tartalmazó lista. 

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Az Text Analytics-ügyfél egy olyan `TextAnalyticsClient` objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél számos módszert biztosít a szöveg kötegként való elemzéséhez. 

Ha a kötegelt feldolgozás szövege megjelenik az API-nak a (z) listájának megfelelően, amely a (z `documents` `dictionary` `id` `text` ) és a (z) függvény kombinációját, és `language` attribútumokat tartalmaz a használt módszertől függően. Az `text` attribútum tárolja a forrásban elemezni kívánt szöveget `language` , és a `id` értéke bármilyen lehet. 

A válasz objektum az egyes dokumentumok elemzett adatait tartalmazó lista. 

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Az Text Analytics-ügyfél egy [TextAnalyticsClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient) objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél számos módszert biztosít a szöveg elemzéséhez, egyetlen sztringként vagy kötegként. 

A rendszer az API-nak elküldi a szöveget, `documents` amely a `dictionary` `id` `text` használt módszertől függően a, a és az attribútumok kombinációját tartalmazó objektumokat tartalmazza `language` . Az `text` attribútum tárolja a forrásban elemezni kívánt szöveget `language` , és a `id` értéke bármilyen lehet. 

---

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Pythonhoz készült Text Analytics ügyféloldali kódtár használatával:

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitások felismerése](#named-entity-recognition-ner) 
* [Személyazonosításra alkalmas adatok felismerése](#personally-identifiable-information-recognition) 
* [Entitás összekapcsolása](#entity-linking)
* [Fő kifejezés kibontása](#key-phrase-extraction)


# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitások felismerése](#named-entity-recognition-ner) 
* [Entitás összekapcsolása](#entity-linking)
* [Fő kifejezés kibontása](#key-phrase-extraction)

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitások felismerése](#named-entity-recognition-ner) 
* [Entitás összekapcsolása](#entity-linking)
* [Fő kifejezés kibontása](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Hozzon létre egy függvényt, amely létrehozza az `TextAnalyticsClient` objektumot a `key` fentivel, és `endpoint` létrehozta azt. Ezután hozzon létre egy új ügyfelet. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Hozzon létre egy függvényt, amely létrehozza az `TextAnalyticsClient` objektumot a `key` fentivel, és `endpoint` létrehozta azt. Ezután hozzon létre egy új ügyfelet. Vegye figyelembe, hogy `api_version=TextAnalyticsApiVersion.V3_0` a 3,0-es verzió használatához definiálni kell.

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential, 
            api_version=TextAnalyticsApiVersion.V3_0)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

Hozzon létre egy függvényt, amely létrehozza az `TextAnalyticsClient` objektumot a `key` fentivel, és `endpoint` létrehozta azt. Ezután hozzon létre egy új ügyfelet. 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>Hangulatelemzés

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Hozzon létre egy nevű új függvényt `sentiment_analysis_example()` , amely argumentumként veszi fel az ügyfelet, majd hívja meg a `analyze_sentiment()` függvényt. A visszaadott válasz objektum a teljes bemeneti dokumentum hangulati címkéjét és pontszámát, valamint az egyes mondatok hangulati elemzését fogja tartalmazni.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Kimenet

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

## <a name="opinion-mining"></a>Vélemény bányászata

Ha szeretné elkészíteni a véleményeket a vélemény kitermelésével kapcsolatban, hozzon létre egy nevű új függvényt, `sentiment_analysis_with_opinion_mining_example()` amely argumentumként fogadja az ügyfelet, majd hívja meg a `analyze_sentiment()` függvényt a Option jelzővel `show_opinion_mining=True` . A visszaadott válasz objektum nem csupán az összes mondat hangulatát és a teljes bemeneti dokumentum pontszámát fogja tartalmazni, az egyes mondatok esetében azonban a szempontok és a vélemények szintjének elemzését is.


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice",
        "The rooms were beautiful but dirty. The AC was good and quiet, but the elevator was broken"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                aspect = mined_opinion.aspect
                print("......'{}' aspect '{}'".format(aspect.sentiment, aspect.text))
                for opinion in mined_opinion.opinions:
                    print("......'{}' opinion '{}'".format(opinion.sentiment, opinion.text))
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>Kimenet

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' aspect 'food'
......'negative' opinion 'unacceptable'
......'negative' aspect 'service'
......'negative' opinion 'unacceptable'
......'positive' aspect 'concierge'
......'positive' opinion 'nice'


Document Sentiment: negative
Overall scores: positive=0.00; neutral=0.00; negative=1.00

Sentence: The rooms were beautiful but dirty.
Sentence sentiment: negative
Sentence score:
Positive=0.01
Neutral=0.00
Negative=0.99

......'mixed' aspect 'rooms'
......'positive' opinion 'beautiful'
......'negative' opinion 'dirty'
Sentence: The AC was good and quiet, but the elevator was broken
Sentence sentiment: negative
Sentence score:
Positive=0.00
Neutral=0.00
Negative=1.00

......'positive' aspect 'AC'
......'positive' opinion 'good'
......'positive' opinion 'quiet'
......'negative' aspect 'elevator'
......'negative' opinion 'broken'
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Hozzon létre egy nevű új függvényt `sentiment_analysis_example()` , amely argumentumként veszi fel az ügyfelet, majd hívja meg a `analyze_sentiment()` függvényt. A visszaadott válasz objektum a teljes bemeneti dokumentum hangulati címkéjét és pontszámát, valamint az egyes mondatok hangulati elemzését fogja tartalmazni.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Kimenet

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Hitelesítse az ügyfél objektumát, és hívja meg a [hangulat ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt. Ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint a hangulat pontszámát. Ha a pontszám közelebb van a 0 értékhez, a negatív érzést jelez, míg az 1. számú pontszám pozitív hangulatot jelez.

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>Nyelvfelismerés

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Hozzon létre egy nevű új függvényt `language_detection_example()` , amely argumentumként veszi fel az ügyfelet, majd hívja meg a `detect_language()` függvényt. A visszaadott válasz objektum az észlelt nyelvet fogja tartalmazni, ha a művelet `primary_language` sikeres, és `error` Ha nem.

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `country_hint` paraméter használatával kétbetűs országkód adható meg. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztringre állítja `country_hint : ""` . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Kimenet

```console
Language:  French
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Hozzon létre egy nevű új függvényt `language_detection_example()` , amely argumentumként veszi fel az ügyfelet, majd hívja meg a `detect_language()` függvényt. A visszaadott válasz objektum az észlelt nyelvet fogja tartalmazni, ha a művelet `primary_language` sikeres, és `error` Ha nem.

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `country_hint` paraméter használatával kétbetűs országkód adható meg. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztringre állítja `country_hint : ""` . 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Kimenet

```console
Language:  French
```

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

A korábban létrehozott ügyfél használatával hívja meg [detect_language ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) , és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az első visszaadott nyelvet.

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>Kimenet

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

> [!NOTE]
> A verzióban `3.1` : 
> * Az entitások összekapcsolása egy külön kérelem, mint a kapcsolatfelvétel.

Hozzon létre egy nevű új függvényt `entity_recognition_example` , amely argumentumként fogadja az ügyfelet, majd hívja meg a `recognize_entities()` függvényt, és ismételje meg az eredményeket. A visszaadott válasz objektum az észlelt entitások listáját fogja tartalmazni, `entity` Ha az sikeres, és `error` Ha nem. Minden észlelt entitás esetében nyomtassa ki a kategóriáját és alkategóriáját, ha van ilyen.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Kimenet

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

### <a name="entity-linking"></a>Entitáskapcsolás

Hozzon létre egy nevű új függvényt `entity_linking_example()` , amely argumentumként fogadja az ügyfelet, majd hívja meg a `recognize_linked_entities()` függvényt, és ismételje meg az eredményeket. A visszaadott válasz objektum az észlelt entitások listáját fogja tartalmazni, `entities` Ha az sikeres, és `error` Ha nem. Mivel a csatolt entitások egyedi módon vannak azonosítva, az azonos entitások előfordulásainak az `entity` objektumok listáján vannak csoportosítva `match` .

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Kimenet

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

### <a name="personally-identifiable-information-recognition"></a>Személyazonosításra alkalmas adatok felismerése

Hozzon létre egy nevű új függvényt `pii_recognition_example` , amely argumentumként fogadja az ügyfelet, majd hívja meg a `recognize_pii_entities()` függvényt, és ismételje meg az eredményeket. A visszaadott válasz objektum az észlelt entitások listáját fogja tartalmazni, `entity` Ha az sikeres, és `error` Ha nem. Minden észlelt entitás esetében nyomtassa ki a kategóriáját és alkategóriáját, ha van ilyen.

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>Kimenet

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

> [!NOTE]
> A verzióban `3.0` : 
> * Az entitások összekapcsolása egy külön kérelem, mint a kapcsolatfelvétel.

Hozzon létre egy nevű új függvényt `entity_recognition_example` , amely argumentumként fogadja az ügyfelet, majd hívja meg a `recognize_entities()` függvényt, és ismételje meg az eredményeket. A visszaadott válasz objektum az észlelt entitások listáját fogja tartalmazni, `entity` Ha az sikeres, és `error` Ha nem. Minden észlelt entitás esetében nyomtassa ki a kategóriáját és alkategóriáját, ha van ilyen.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Kimenet

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

### <a name="entity-linking"></a>Entitáskapcsolás

Hozzon létre egy nevű új függvényt `entity_linking_example()` , amely argumentumként fogadja az ügyfelet, majd hívja meg a `recognize_linked_entities()` függvényt, és ismételje meg az eredményeket. A visszaadott válasz objektum az észlelt entitások listáját fogja tartalmazni, `entities` Ha az sikeres, és `error` Ha nem. Mivel a csatolt entitások egyedi módon vannak azonosítva, az azonos entitások előfordulásainak az `entity` objektumok listáján vannak csoportosítva `match` .

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Kimenet

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

> [!NOTE]
> Az 2,1-es verzióban az entitások összekapcsolása szerepel a megjelenő válaszban.

A korábban létrehozott ügyfél használatával hívja meg az [entitások ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt, és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint a benne található entitásokat.

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>Kimenet

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

### <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

# <a name="version-31-preview"></a>[3,1-es verzió előnézet](#tab/version-3-1)

Hozzon létre egy nevű új függvényt `key_phrase_extraction_example()` , amely argumentumként veszi fel az ügyfelet, majd hívja meg a `extract_key_phrases()` függvényt. Az eredmény tartalmazza az észlelt kulcsfontosságú kifejezések listáját, `key_phrases` Ha az sikeres volt, és `error` Ha nem, akkor. Minden észlelt kulcs kifejezésének nyomtatása.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Kimenet

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-30"></a>[3,0-es verzió](#tab/version-3)

Hozzon létre egy nevű új függvényt `key_phrase_extraction_example()` , amely argumentumként veszi fel az ügyfelet, majd hívja meg a `extract_key_phrases()` függvényt. Az eredmény tartalmazza az észlelt kulcsfontosságú kifejezések listáját, `key_phrases` Ha az sikeres volt, és `error` Ha nem, akkor. Minden észlelt kulcs kifejezésének nyomtatása.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Kimenet

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

A korábban létrehozott ügyfél használatával hívja meg a [key_phrases ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) függvényt, és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az abban található fő kifejezéseket.

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>Kimenet

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

--- 