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
ms.date: 05/09/2019
ms.author: aahi
ms.openlocfilehash: 9d6dfb79d02df3eebe33e67743ceaf97fc0a2a77
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519348"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Gyors útmutató: A Text Analytics kognitív szolgáltatás hívásához Python REST API használatával 
<a name="HOLTop"></a>

Ez a rövid útmutató segítségével megkezdheti a Text Analytics REST API és a Python nyelv elemzése. Ez a cikk bemutatja, hogyan való [nyelvfelismerés](#Detect), [vélemények elemzése](#SentimentAnalysis), [kinyerheti a kulcskifejezéseket](#KeyPhraseExtraction), és [kapcsolt entitások azonosítása](#Entities).

Ebben a példában a parancssorból vagy egy Jupyter notebookot futtathatja [MyBinder](https://mybinder.org) jelvények Binder indításkor kattintva:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

Az API-k műszaki dokumentációjáért lásd az [API-definíciókat](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.x](https://python.org)

* A [végpontját és hozzáférési kulcsát](../How-tos/text-analytics-how-to-access-key.md) létrehozott az Ön számára a regisztrációhoz.

* A Python-kódtár kérelmek
    
    A könyvtár a következő paranccsal telepítheti:

    ```console
    pip install --upgrade requests
    ```

* Ha a Binder notebook helyileg futtatja, előfordulhat, hogy frissítenie [IPython](https://ipython.org/install.html):
    
    ```console
    pip install --upgrade IPython
    ```

* [!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazás a kedvenc szerkesztőjében, vagy IDE. Adja hozzá az alábbi importálásokat a fájlhoz.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
from IPython.display import HTML
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

### <a name="output"></a>Kimenet

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

## <a name="extract-key-phrases"></a>Kulcsszavak keresése
 
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
  "errors":[

  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Entitások azonosítása

Azonosíthatja a jól ismert entitások (személyek, helyek és dolgot) szereplő szöveges dokumentumok, append `keyPhrases` , a Szövegelemzés bázisvégpont kialakításához, a nyelv észlelése URL-CÍMÉT. Például:`https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
entities_url = text_analytics_base_url + "keyPhrases"
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

### <a name="output"></a>Kimenet

```json
{
  "documents":[
    {
      "id":"1",
      "keyPhrases":[
        "Bill Gates",
        "Paul Allen",
        "BASIC interpreters",
        "Altair",
        "Microsoft"
      ]
    }
  ],
  "errors":[]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
