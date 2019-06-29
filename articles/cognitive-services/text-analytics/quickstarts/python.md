---
title: 'Gyors útmutató: A szövegelemzési API meghívására Python használatával'
titleSuffix: Azure Cognitive Services
description: Get information és kód minták segítségével gyorsan Ismerkedés a szövegelemzési API-val az Azure Cognitive Servicesben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: aahi
ms.openlocfilehash: 835dc8d25ad1d6a30020408636b556c3f247200d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478379"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Gyors útmutató: A Text Analytics kognitív szolgáltatás hívásához Python REST API használatával 
<a name="HOLTop"></a>

Ez a rövid útmutató segítségével megkezdheti a Text Analytics REST API és a Python nyelv elemzése. Ez a cikk bemutatja, hogyan való [nyelvfelismerés](#Detect), [vélemények elemzése](#SentimentAnalysis), [kinyerheti a kulcskifejezéseket](#KeyPhraseExtraction), és [kapcsolt entitások azonosítása](#Entities).

Az API-k műszaki dokumentációjáért lásd az [API-definíciókat](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.x](https://python.org)

* A [végpontját és hozzáférési kulcsát](../How-tos/text-analytics-how-to-access-key.md) létrehozott az Ön számára a regisztrációhoz.

* A Python-kódtár kérelmek
    
    A könyvtár a következő paranccsal telepítheti:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazás a kedvenc szerkesztőjében, vagy IDE. Adja hozzá az alábbi importálásokat a fájlhoz.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

Hozzon létre változókat az előfizetési kulcs és a végpont a Text Analytics REST API-hoz. Győződjön meg arról, hogy a régió végpontját, a regisztráció során használt felel meg (például `westcentralus`). Ha egy ingyenes próba-kulcsot használ, nem kell bármin változtatni.
    
```python
subscription_key = "<ADD YOUR KEY HERE>"
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
```

A következő szakaszok ismertetik, hogyan hívhat meg minden, az API-k funkcióját.

<a name="Detect"></a>

## <a name="detect-languages"></a>Nyelvek felismerése

Hozzáfűzés `languages` , a Szövegelemzés bázisvégpont kialakításához, a nyelv észlelése URL-CÍMÉT. Például:`https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
    
```python
language_api_url = text_analytics_base_url + "languages"
```

Az API-hoz a hasznos listája áll `documents`, amely vannak rekordokat tartalmazó egy `id` és a egy `text` attribútum. A `text` attribútumtárak elemezni, a szöveg és a `id` bármilyen érték lehet. 

```python
documents = { "documents": [
    { "id": "1", "text": "This is a document written in English." },
    { "id": "2", "text": "Este es un document escrito en Español." },
    { "id": "3", "text": "这是一个用中文写的文件" }
]}
```

A kérések library használatával a dokumentumok küldhet az API-t. Az előfizetési kulcs hozzáadása a `Ocp-Apim-Subscription-Key` fejléc, és a kérelem küldése `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Output

```json
{
"documents":[
    {
        "detectedLanguages":[
        {
            "iso6391Name":"en",
            "name":"English",
            "score":1.0
        }
        ],
        "id":"1"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"es",
            "name":"Spanish",
            "score":1.0
        }
        ],
        "id":"2"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"zh_chs",
            "name":"Chinese_Simplified",
            "score":1.0
        }
        ],
        "id":"3"
    }
],
"errors":[]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Vélemények elemzése

Hangulatának (amely a tartományok közötti pozitív vagy negatív) áll a dokumentumok, Hozzáfűzés `sentiment` , a Szövegelemzés bázisvégpont kialakításához, a nyelv észlelése URL-CÍMÉT. Például:`https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
    
```python
sentiment_url = text_analytics_base_url + "sentiment"
```

A nyelv észlelése példában hozzon létre egy szótár egy `documents` kulcs, amely dokumentumot áll. Minden dokumentum egy rekord, amely az `id`, az elemzendő `text` és a szöveg `language` tulajdonságából áll. 

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

A kérések library használatával a dokumentumok küldhet az API-t. Az előfizetési kulcs hozzáadása a `Ocp-Apim-Subscription-Key` fejléc, és a kérelem küldése `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Output

A dokumentumok értelmezhetőségi pontszámot 0,0 és 1,0, között van, a nagyobb pozitív vélemények jelző magasabb pontszámot.

```json
{
  "documents":[
    {
      "id":"1",
      "score":0.9708490371704102
    },
    {
      "id":"2",
      "score":0.0019068121910095215
    },
    {
      "id":"3",
      "score":0.7456425428390503
    },
    {
      "id":"4",
      "score":0.334433376789093
    }
  ],
  "errors":[

  ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Kulcsszavak kinyerése
 
A kulcskifejezések kinyerése azon dokumentumok, a hozzáfűző `keyPhrases` , a Szövegelemzés bázisvégpont kialakításához, a nyelv észlelése URL-CÍMÉT. Például:`https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
keyphrase_url = text_analytics_base_url + "keyPhrases"
```

Ez a témakörgyűjtemény dokumentumok megegyezik a vélemények elemzése példában használt.

```python
documents = {"documents" : [
  {"id": "1", "language": "en", "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
  {"id": "2", "language": "en", "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},  
  {"id": "3", "language": "es", "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},  
  {"id": "4", "language": "es", "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

A kérések library használatával a dokumentumok küldhet az API-t. Az előfizetési kulcs hozzáadása a `Ocp-Apim-Subscription-Key` fejléc, és a kérelem küldése `requests.post()`. 

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Output

```json
{
  "documents":[
    {
      "keyPhrases":[
        "wonderful experience",
        "staff",
        "rooms"
      ],
      "id":"1"
    },
    {
      "keyPhrases":[
        "food",
        "terrible time",
        "hotel",
        "staff"
      ],
      "id":"2"
    },
    {
      "keyPhrases":[
        "Monte Rainier",
        "caminos"
      ],
      "id":"3"
    },
    {
      "keyPhrases":[
        "carretera",
        "tráfico",
        "día"
      ],
      "id":"4"
    }
  ],
  "errors":[

  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Entitások azonosítása

Azonosíthatja a jól ismert entitások (személyek, helyek és dolgot) szereplő szöveges dokumentumok, append `entities` , a Szövegelemzés bázisvégpont kialakításához, a nyelv észlelése URL-CÍMÉT. Például:`https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`
    
```python
entities_url = text_analytics_base_url + "entities"
```

Hozzon létre egy gyűjteményt a dokumentumok, például az előző példák. 

```python
documents = {"documents" : [
  {"id": "1", "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."}
]}
```

A kérések library használatával a dokumentumok küldhet az API-t. Az előfizetési kulcs hozzáadása a `Ocp-Apim-Subscription-Key` fejléc, és a kérelem küldése `requests.post()`.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
```

### <a name="output"></a>Output

```json
{'documents': [{'id': '1',
   'entities': [{'name': 'Microsoft',
     'matches': [{'wikipediaScore': 0.502357972145024,
       'entityTypeScore': 1.0,
       'text': 'Microsoft',
       'offset': 0,
       'length': 9}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Microsoft',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Microsoft',
     'bingId': 'a093e9b9-90f5-a3d5-c4b8-5855e1b01f85',
     'type': 'Organization'},
    {'name': 'Bill Gates',
     'matches': [{'wikipediaScore': 0.5849375085784292,
       'entityTypeScore': 0.999847412109375,
       'text': 'Bill Gates',
       'offset': 25,
       'length': 10}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Bill Gates',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Bill_Gates',
     'bingId': '0d47c987-0042-5576-15e8-97af601614fa',
     'type': 'Person'},
    {'name': 'Paul Allen',
     'matches': [{'wikipediaScore': 0.5314163053043621,
       'entityTypeScore': 0.9988409876823425,
       'text': 'Paul Allen',
       'offset': 40,
       'length': 10}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Paul Allen',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Paul_Allen',
     'bingId': 'df2c4376-9923-6a54-893f-2ee5a5badbc7',
     'type': 'Person'},
    {'name': 'April 4',
     'matches': [{'wikipediaScore': 0.37312706493069636,
       'entityTypeScore': 0.8,
       'text': 'April 4',
       'offset': 54,
       'length': 7}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'April 4',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/April_4',
     'bingId': '52535f87-235e-b513-54fe-c03e4233ac6e',
     'type': 'Other'},
    {'name': 'April 4, 1975',
     'matches': [{'entityTypeScore': 0.8,
       'text': 'April 4, 1975',
       'offset': 54,
       'length': 13}],
     'type': 'DateTime',
     'subType': 'Date'},
    {'name': 'BASIC',
     'matches': [{'wikipediaScore': 0.35916049097766867,
       'entityTypeScore': 0.8,
       'text': 'BASIC',
       'offset': 89,
       'length': 5}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'BASIC',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/BASIC',
     'bingId': '5b16443d-501c-58f3-352e-611bbe75aa6e',
     'type': 'Other'},
    {'name': 'Altair 8800',
     'matches': [{'wikipediaScore': 0.8697256853652899,
       'entityTypeScore': 0.8,
       'text': 'Altair 8800',
       'offset': 116,
       'length': 11}],
     'wikipediaLanguage': 'en',
     'wikipediaId': 'Altair 8800',
     'wikipediaUrl': 'https://en.wikipedia.org/wiki/Altair_8800',
     'bingId': '7216c654-3779-68a2-c7b7-12ff3dad5606',
     'type': 'Other'}]}],
 'errors': []}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
