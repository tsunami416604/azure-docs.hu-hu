---
title: 'Rövid útmutató: A Text Analytics API meghívása a Python használatával'
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services Text Analytics API használatának gyors megkezdéséhez olvassa el az információk és a kódok mintáit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 15f0cf7725dec99884497be79b63c21ef16f88b1
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284971"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Rövid útmutató: a Text Analytics kognitív szolgáltatás meghívása a Python REST API használatával 
<a name="HOLTop"></a>

Ezzel a rövid útmutatóval megkezdheti a nyelv elemzését a Text Analytics REST API és a Python használatával. Ebből a cikkből megtudhatja, hogyan [derítheti fel a nyelveket](#Detect), [elemezheti](#SentimentAnalysis)a [fontos kifejezéseket](#KeyPhraseExtraction), és hogyan [azonosíthatja a társított entitásokat](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Python 3. x](https://python.org)

* A Python-kérelmek könyvtára
    
    A függvénytárat a következő paranccsal telepítheti:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást a kedvenc szerkesztőjében vagy az IDE-ban. Adja hozzá a következő importálásokat a fájlhoz.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

Hozzon létre változókat az erőforrás Azure-végpontjának és előfizetési kulcsának létrehozásához. Szerezze be ezeket az értékeket a környezeti változók TEXT_ANALYTICS_SUBSCRIPTION_KEY és TEXT_ANALYTICS_ENDPOINT. Ha ezeket a környezeti változókat az alkalmazás szerkesztésének megkezdése után hozta létre, akkor be kell állítania és újra meg kell nyitnia azt a szerkesztőt, IDE vagy rendszerhéjt, amelyet a változók eléréséhez használ.
    
```python
import os

key_var_name = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TEXT_ANALYTICS_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Az alábbi szakaszok azt ismertetik, hogyan hívhatók meg az egyes API-funkciók.

<a name="Detect"></a>

## <a name="detect-languages"></a>Nyelvek felismerése

A nyelvfelismerés URL-címének létrehozásához fűzze hozzá `/text/analytics/v2.1/languages` a Text Analytics Base végponthoz. Például:`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v2.1/languages"
```

Az API hasznos adatai a `documents`listáját tartalmazzák, amely egy `id` és egy `text` attribútumot tartalmazó rekordok. A `text` attribútum tárolja az elemezni kívánt szöveget, és a `id` bármilyen érték lehet. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

A dokumentumok API-nak való elküldéséhez használja a kérelmek könyvtárat. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és küldje el a kérést `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Kimenet

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

A dokumentumok egy csoportjának pozitív vagy negatív tartománya közötti érzelmek észleléséhez fűzze hozzá `/text/analytics/v2.1/sentiment`t a Text Analytics Base végponthoz a nyelvfelismerés URL-címének megadásához. Például:`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v2.1/sentiment"
```

A nyelvfelismerés példához hasonlóan hozzon létre egy szótárt egy `documents` kulccsal, amely a dokumentumok listájából áll. Minden dokumentum egy rekord, amely az `id`, az elemzendő `text` és a szöveg `language` tulajdonságából áll. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
    {"id": "2", "language": "en",
        "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},
    {"id": "3", "language": "es",
        "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},
    {"id": "4", "language": "es",
     "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

A dokumentumok API-nak való elküldéséhez használja a kérelmek könyvtárat. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és küldje el a kérést `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Kimenet

A dokumentumra vonatkozó hangulati pontszám 0,0 és 1,0 között van, és egy magasabb pontszámot jelez, amely még pozitívabb érzést mutat.

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
  "errors":[]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Kulcsszavak kinyerése
 
Ha a legfontosabb kifejezéseket szeretné kibontani a dokumentumok egy csoportján, fűzze hozzá `/text/analytics/v2.1/keyPhrases` a Text Analytics Base végponthoz a nyelvfelismerés URL-címének megadásához. Például:`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v2.1/keyphrases"
```

A dokumentumok ezen gyűjteménye ugyanaz, mint az érzelmek elemzésére szolgáló példa.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
    {"id": "2", "language": "en",
        "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},
    {"id": "3", "language": "es",
        "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},
    {"id": "4", "language": "es",
     "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
]}
```

A dokumentumok API-nak való elküldéséhez használja a kérelmek könyvtárat. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és küldje el a kérést `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Kimenet

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
  "errors":[]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Entitások azonosítása

A jól ismert entitások (személyek, helyek és dolgok) a szöveges dokumentumokban való azonosításához fűzze hozzá `/text/analytics/v2.1/entities`t a Text Analytics Base végponthoz a nyelvfelismerés URL-címének megalkotása érdekében. Például:`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`
    
```python
entities_url = endpoint + "/text/analytics/v2.1/entities"
```

Hozzon létre egy gyűjteményt a dokumentumokból, például az előző példákban. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."}
]}
```

A dokumentumok API-nak való elküldéséhez használja a kérelmek könyvtárat. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és küldje el a kérést `requests.post()`.

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>Kimenet

```json
{
   "documents" : [
      {
         "id" : "1",
         "entities" : [
            {
               "name" : "Microsoft",
               "matches" : [
                  {
                     "wikipediaScore" : 0.49897989655674446,
                     "entityTypeScore" : 1.0,
                     "text" : "Microsoft",
                     "offset" : 0,
                     "length" : 9
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Microsoft",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Microsoft",
               "bingId" : "a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type" : "Organization"
            },
            {
               "name" : "Bill Gates",
               "matches" : [
                  {
                     "wikipediaScore" : 0.58357497243368983,
                     "entityTypeScore" : 0.999847412109375,
                     "text" : "Bill Gates",
                     "offset" : 25,
                     "length" : 10
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Bill Gates",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Bill_Gates",
               "bingId" : "0d47c987-0042-5576-15e8-97af601614fa",
               "type" : "Person"
            },
            {
               "name" : "Paul Allen",
               "matches" : [
                  {
                     "wikipediaScore" : 0.52977533244176866,
                     "entityTypeScore" : 0.99884098768234253,
                     "text" : "Paul Allen",
                     "offset" : 40,
                     "length" : 10
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Paul Allen",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Paul_Allen",
               "bingId" : "df2c4376-9923-6a54-893f-2ee5a5badbc7",
               "type" : "Person"
            },
            {
               "name" : "April 4",
               "matches" : [
                  {
                     "wikipediaScore" : 0.37220990924571939,
                     "entityTypeScore" : 0.8,
                     "text" : "April 4",
                     "offset" : 54,
                     "length" : 7
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "April 4",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/April_4",
               "bingId" : "52535f87-235e-b513-54fe-c03e4233ac6e",
               "type" : "Other"
            },
            {
               "name" : "April 4, 1975",
               "matches" : [
                  {
                     "entityTypeScore" : 0.8,
                     "text" : "April 4, 1975",
                     "offset" : 54,
                     "length" : 13
                  }
               ],
               "type" : "DateTime",
               "subType" : "Date"
            },
            {
               "name" : "BASIC",
               "matches" : [
                  {
                     "wikipediaScore" : 0.35686239324548041,
                     "entityTypeScore" : 0.8,
                     "text" : "BASIC",
                     "offset" : 89,
                     "length" : 5
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "BASIC",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/BASIC",
               "bingId" : "5b16443d-501c-58f3-352e-611bbe75aa6e",
               "type" : "Other"
            },
            {
               "name" : "Altair 8800",
               "matches" : [
                  {
                     "wikipediaScore" : 0.868324676465041,
                     "entityTypeScore" : 0.8,
                     "text" : "Altair 8800",
                     "offset" : 116,
                     "length" : 11
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Altair 8800",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Altair_8800",
               "bingId" : "7216c654-3779-68a2-c7b7-12ff3dad5606",
               "type" : "Other"
            },
            {
               "name" : "Altair",
               "matches" : [
                  {
                     "entityTypeScore" : 0.52505272626876831,
                     "text" : "Altair",
                     "offset" : 116,
                     "length" : 6
                  }
               ],
               "type" : "Organization"
            },
            {
               "name" : "8800",
               "matches" : [
                  {
                     "entityTypeScore" : 0.8,
                     "text" : "8800",
                     "offset" : 123,
                     "length" : 4
                  }
               ],
               "type" : "Quantity",
               "subType" : "Number"
            }
         ]
      }
   ],
   "errors" : []
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
