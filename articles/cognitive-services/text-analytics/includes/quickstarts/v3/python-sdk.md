---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 518e6d544547b808b278121bf6364dcd1590bd6f
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281158"
---
<a name="HOLTop"></a>

[Dokumentáció](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [csomag (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

> [!NOTE]
> * Ez a rövid útmutató a Text Analytics ügyféloldali kódtár `3.0-preview` verzióját használja, amely egy nyilvános előzetes verziót tartalmaz a továbbfejlesztett [Hangulatelemzés](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) és az [elnevezett entitások felismeréséhez](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Éles környezetekben javasolt a kötegelt aszinkron módszerek használata a teljesítmény és a méretezhetőség érdekében. Például importálhatja az ügyfelet a `azure.ai.textanalytics.aio` névtérből, és meghívja a `analyze_sentiment()`t ahelyett, hogy a `azure.ai.textanalytics` névtérből `analyze_sentiment()`.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Python 3. x](https://www.python.org/)

## <a name="setting-up"></a>Beállítás

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure-erőforrás létrehozása

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után az ügyféloldali kódtár a következővel telepíthető:

```console
pip install azure-ai-textanalytics
```

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-fájlt, és hozzon létre változókat az erőforrás Azure-végpontjának és előfizetési kulcsának létrehozásához.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objektummodell

A Text Analytics ügyfél egy `TextAnalyticsClient`-objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél számos módszert biztosít a szöveg kötegként való elemzéséhez. Ez a rövid útmutató a függvények egy gyűjteményét használja az egyetlen dokumentum gyors elküldéséhez.

Ha a kötegelt feldolgozás szövegét az API-nak `documents`-listaként küldik el, `dictionary` a használt módszertől függően `id`, `text`és `language` attribútumok kombinációját tartalmazó objektumokat. A `text` attribútum tárolja a forrás `language`elemezni kívánt szöveget, és a `id` értéke lehet. Az egyes dokumentumok feldolgozásakor csak `text` bemenetre van szükség, ahogy az alábbi példákban is látható.  

A válasz objektum az egyes dokumentumok elemzési információit tartalmazó lista. 

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Pythonhoz készült Text Analytics ügyféloldali kódtár használatával:

* [Hangulatelemzés](#sentiment-analysis) (nyilvános előzetes verzió)
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitások felismerése](#named-entity-recognition-public-preview) (nyilvános előzetes verzió)
* [Nevesített entitások felismerése – személyes adatok](#named-entity-recognition---personal-information-public-preview) (nyilvános előzetes verzió)
* [Entitás összekapcsolása](#entity-linking)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>Véleményelemzés

> [!NOTE]
> Az alábbi kód az érzelmek elemzése v3, amely nyilvános előzetes verzióban érhető el.

Hozzon létre egy új, `sentiment_analysis_example()` nevű függvényt, amely a végpontot és a kulcsot argumentumként veszi fel, majd meghívja a `single_analyze_sentiment()` függvényt. A visszaadott válasz objektum a teljes bemeneti dokumentum hangulati címkéjét és pontszámát, valamint az egyes mondatok hangulati elemzését fogja tartalmazni.


```python
from azure.ai.textanalytics import single_analyze_sentiment

def sentiment_analysis_example(endpoint, key):

    document = "I had the best day of my life. I wish you were there with me."

    response = single_analyze_sentiment(endpoint=endpoint, key=key, input_text=document)
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.document_scores.positive,
        response.document_scores.neutral,
        response.document_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentence_scores.positive,
            sentence.sentence_scores.neutral,
            sentence.sentence_scores.negative,
        ))

            
sentiment_analysis_example(endpoint, key)
```

### <a name="output"></a>Kimenet

```console
Document Sentiment: positive
Overall scores: positive=0.999; neutral=0.001; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
positive=0.999
neutral=0.001
negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
positive=0.212
neutral=0.771
negative=0.017
```

## <a name="language-detection"></a>Nyelvfelismerés

Hozzon létre egy új, `language_detection_example()` nevű függvényt, amely a végpontot és a kulcsot argumentumként veszi fel, majd meghívja a `single_detect_languages()` függvényt. A visszaadott válasz objektum az észlelt nyelvet `detected_languages` ha sikeres, és egy `error`, ha nem.

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `country_hint` paraméterrel megadhatja a kétbetűs országkódot. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztring `country_hint : ""`értékre állítja. 

```python
from azure.ai.textanalytics import single_detect_language

def language_detection_example(endpoint, key):
    try:
        document = "Ce document est rédigé en Français."
        response = single_detect_language(endpoint=endpoint, key=key, input_text= document)
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(endpoint, key)
```


### <a name="output"></a>Kimenet

```console
Language:  French
```

## <a name="named-entity-recognition-public-preview"></a>Elnevezett entitások felismerése (nyilvános előzetes verzió)

> [!NOTE]
> Az alábbi kód a megnevezett Entity Recognition v3, amely nyilvános előzetes verzióban érhető el.

Hozzon létre egy `entity_recognition_example` nevű új függvényt, amely argumentumként fogadja a végpontot és a kulcsot, majd meghívja a `single_recognize_entities()` függvényt, és megismétli az eredményeket. A visszaadott válasz objektum az észlelt entitások listáját fogja tartalmazni `entity` ha sikeres, és egy `error`, ha nem. Minden észlelt entitás esetében nyomtassa ki a típusát és altípusát, ha létezik.

```python
from azure.ai.textanalytics import single_recognize_entities

def entity_recognition_example(endpoint, key):

    try:
        document = "I had a wonderful trip to Seattle last week."
        result = single_recognize_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tType: \t", entity.type, "\tSubType: \t", entity.subtype,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(endpoint, key)
```

### <a name="output"></a>Kimenet

```console
Named Entities:

    Text:    Seattle    Type:    Location   SubType:     None 
    Offset:      26     Length:      26     Confidence Score:    0.806 

    Text:    last week  Type:    DateTime   SubType:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Nevesített entitások felismerése – személyes adatok (nyilvános előzetes verzió)

> [!NOTE]
> Az alábbi kód a személyes adatok észlelésére szolgál az elnevezett Entity Recognition v3 használatával, amely nyilvános előzetes verzióban érhető el.

Hozzon létre egy új, `entity_pii_example()` nevű függvényt, amely a végpontot és a kulcsot argumentumként veszi át, majd meghívja a `single_recognize_pii_entities()` függvényt, és lekéri az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az entitásokat.

```python
from azure.ai.textanalytics import single_recognize_pii_entities

def entity_pii_example(endpoint, key):

        document = "Insurance policy for SSN on file 123-12-1234 is here by approved."


        result = single_recognize_pii_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tType: ", entity.type,"\tSub-Type: ", entity.subtype)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(endpoint, key)
```

### <a name="output"></a>Kimenet

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Type:  U.S. Social Security Number (SSN)    Sub-Type:  
        Offset:  33     Length:  11     Score: 0.850 
```

## <a name="entity-linking"></a>Entitáskapcsolás

Hozzon létre egy `entity_linking_example()` nevű új függvényt, amely argumentumként fogadja a végpontot és a kulcsot, majd meghívja a `single_recognize_linked_entities()` függvényt, és megismétli az eredményeket. A visszaadott válasz objektum az észlelt entitások listáját fogja tartalmazni `entities` ha sikeres, és egy `error`, ha nem. Mivel a csatolt entitások egyedi módon vannak azonosítva, az ugyanahhoz az entitáshoz tartozó előfordulások egy `entity` objektum alatt vannak csoportosítva `match` objektumok listájában.

```python
from azure.ai.textanalytics import single_recognize_linked_entities

def entity_linking_example(endpoint, key):

    try:
        document = """Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""
        result = single_recognize_linked_entities(endpoint=endpoint, key=key, input_text= document)

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(endpoint, key)
```

### <a name="output"></a>Kimenet

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  116    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  161    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  150    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  89     Length: 5
```

## <a name="key-phrase-extraction"></a>A kulcsfontosságú kifejezések kinyerése

Hozzon létre egy új, `key_phrase_extraction_example()` nevű függvényt, amely a végpontot és a kulcsot argumentumként veszi fel, majd meghívja a `single_extract_key_phrases()` függvényt. Ha a művelet sikeres, az eredmény tartalmazni fogja a `key_phrases` észlelt kulcsos kifejezéseket, és egy `error`, ha nem. Minden észlelt kulcs kifejezésének nyomtatása.

```python
from azure.ai.textanalytics import single_extract_key_phrases

def key_phrase_extraction_example(endpoint, key):

    try:
        document = "My cat might need to see a veterinarian."

        response = single_extract_key_phrases(endpoint=endpoint, key=key, input_text= document)

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(endpoint, key)
```


### <a name="output"></a>Kimenet

```console
    Key Phrases:
         cat
         veterinarian
```