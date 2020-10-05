---
title: 'Rövid útmutató: A Text Analytics API meghívása a Python használatával'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató azt ismerteti, hogyan használható a Python az információk és a kódok megszerzéséhez, hogy gyorsan megismerkedjen az Azure Cognitive Services Text Analytics API használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 0ef870b6b2d3b88b13c16c8c2acbfcee7ed551c1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90527241"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Rövid útmutató: a Text Analytics kognitív szolgáltatás meghívása a Python REST API használatával 
<a name="HOLTop"></a>

Ezzel a rövid útmutatóval megkezdheti a nyelv elemzését a Text Analytics REST API és a Python használatával. Ebből a cikkből megtudhatja, hogyan [derítheti fel a nyelveket](#Detect), [elemezheti](#SentimentAnalysis)a [fontos kifejezéseket](#KeyPhraseExtraction), és hogyan [azonosíthatja a társított entitásokat](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.x](https://python.org)

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

Hozzon létre változókat az erőforrás Azure-végpontjának és előfizetési kulcsának létrehozásához.
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

Az alábbi szakaszok azt ismertetik, hogyan hívhatók meg az egyes API-funkciók.

<a name="Detect"></a>

## <a name="detect-languages"></a>Nyelvek felismerése

Hozzáfűzés a `/text/analytics/v3.0/languages` text Analytics alap végponthoz a nyelvfelismerés URL-címének megalkotása érdekében. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

Az API-ban található hasznos adatok a `documents` `id` és a attribútumot tartalmazó rekordok tartalmazzák `text` . Az `text` attribútum tárolja az elemezni kívánt szöveget, és a `id` értéke bármilyen lehet. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

A dokumentumok API-nak való elküldéséhez használja a kérelmek könyvtárat. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és küldje el a kérelmet a következővel: `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Kimenet

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Vélemények elemzése

Ahhoz, hogy észlelni lehessen a dokumentumok egy halmazának pozitív vagy negatív közötti tartományát, fűzze hozzá az `/text/analytics/v3.0/sentiment` text Analytics Base végponthoz a nyelvfelismerés URL-címének megadásához. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

A nyelvfelismerés példához hasonlóan hozzon létre egy szótárt egy olyan `documents` kulccsal, amely a dokumentumok listájából áll. Minden dokumentum egy rekord, amely az `id`, az elemzendő `text` és a szöveg `language` tulajdonságából áll. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

A dokumentumok API-nak való elküldéséhez használja a kérelmek könyvtárat. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és küldje el a kérelmet a következővel: `requests.post()` . 

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
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Kulcsszókeresés
 
Ha a legfontosabb kifejezéseket szeretné kibontani a dokumentumok egy csoportján, fűzze hozzá az `/text/analytics/v3.0/keyPhrases` text Analytics Base végponthoz a nyelvfelismerés URL-címének megadásához. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

A dokumentumok ezen gyűjteménye ugyanaz, mint az érzelmek elemzésére szolgáló példa.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
    {"id": "3", "language": "en",
        "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."}
]}
```

A dokumentumok API-nak való elküldéséhez használja a kérelmek könyvtárat. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és küldje el a kérelmet a következővel: `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Kimenet

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Entitások azonosítása

A jól ismert entitások (személyek, helyek és dolgok) a szöveges dokumentumokban való azonosításához fűzze hozzá `/text/analytics/v3.0/entities/recognition/general` az Text Analytics Base végpontot a nyelvfelismerés URL-címének létrehozásához. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general"
```

Hozzon létre egy gyűjteményt a dokumentumokból, például az előző példákban. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

A dokumentumok API-nak való elküldéséhez használja a kérelmek könyvtárat. Adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez, és küldje el a kérelmet a következővel: `requests.post()` .

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>Kimenet

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Text Analytics a Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
