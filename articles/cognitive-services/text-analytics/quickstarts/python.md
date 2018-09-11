---
title: 'Gyors útmutató: A szövegelemzési API meghívására Python használatával |} A Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Get information és kód minták segítségével gyorsan használatának első lépései a szövegelemzési API-t a Microsoft Cognitive Services, Azure-ban.
services: cognitive-services
author: ashmaka
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: 8e570aac2c2d89a8147d179c4b0f9155497c5188
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298692"
---
# <a name="quickstart-using-python-to-call-the-text-analytics-cognitive-service"></a>Gyors útmutató: A Text Analytics kognitív szolgáltatás hívásához Python használatával
<a name="HOLTop"></a>

Ez az útmutató bemutatja, hogyan való [nyelvfelismerés](#Detect), [vélemények elemzése](#SentimentAnalysis), és [kinyerheti a kulcskifejezéseket](#KeyPhraseExtraction) használatával a [Text Analytics API-k](//go.microsoft.com/fwlink/?LinkID=759711)Python használatával.

Ebben a példában-et futtathatja egy Jupyter notebookot [MyBinder](https://mybinder.org) jelvények Binder indításkor kattintva: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

Tekintse meg a [API-definíciók](//go.microsoft.com/fwlink/?LinkID=759346) technikai dokumentációját az API-kat.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a **Text Analytics API**. Használhatja a **összesen 5 000 tranzakció/hó ingyenes szintet** forgatókönyv végrehajtásához.

Rendelkeznie kell a [végpontját és hozzáférési kulcsát](../How-tos/text-analytics-how-to-access-key.md) létrehozott az Ön számára a regisztrációhoz. 

Ez a forgatókönyv folytatásához, cserélje le a `subscription_key` korábban beszerzett érvényes előfizetéssel kulccsal.


```python
subscription_key = None
assert subscription_key
```

Ezután ellenőrizze, hogy az a régió `text_analytics_base_url` felel meg, amelyet használ, a szolgáltatás beállítása során. Ha egy ingyenes próba-kulcsot használ, nem kell bármin változtatni.


```python
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/"
```

<a name="Detect"></a>

## <a name="detect-languages"></a>Nyelvfelismerés

A nyelvi API-t észleli a szöveg nyelvének dokumentálja, használja a [nyelv észlelése metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7). A szolgáltatásvégpont API nyelvfelismerés az Ön régiójában, az alábbi URL-CÍMEN keresztül érhető el:


```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/languages


Az API-hoz a hasznos listája áll `documents`, minden egyes, amely tartalmaz egy `id` és a egy `text` attribútum. A `text` attribútum tárolja az elemezni kívánt szöveget. 

Cserélje le a `documents` szótárba, minden más nyelv észlelése szövege. 


```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

A következő néhány sornyi kóddal hívja a nyelv észlelése API használatával a `requests` kódtárat a Python használatával határozza meg a dokumentumokat a megfelelő nyelvet.


```python
import requests
from pprint import pprint
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

    {'documents': [{'detectedLanguages': [{'iso6391Name': 'en',
                                           'name': 'English',
                                           'score': 1.0}],
                    'id': '1'},
                   {'detectedLanguages': [{'iso6391Name': 'es',
                                           'name': 'Spanish',
                                           'score': 1.0}],
                    'id': '2'},
                   {'detectedLanguages': [{'iso6391Name': 'zh_chs',
                                           'name': 'Chinese_Simplified',
                                           'score': 1.0}],
                    'id': '3'}],
     'errors': []}


A következő kódsorokat HTML-táblázatként jelennek meg a JSON-adatokat.


```python
from IPython.display import HTML
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Vélemények elemzése

A Sentiment Analysis API detexts jelöli szöveg rekordkészlet, használja a [vélemények metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). Az alábbi példa két dokumentumot, egy az angol és spanyol nyelven egy másik pontszámmodell.

Hangulatelemzés szolgáltatás végpontját az Ön régiójában, az alábbi URL-CÍMEN keresztül érhető el:


```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment


A nyelv észlelése példában a szolgáltatás és a egy szótár biztosított egy `documents` kulcs, amely dokumentumot áll. Minden egyes dokumentum egy rekord, amely a `id`, a `text` elemezni és a `language` a szöveg. Az előző szakaszban az API nyelvfelismerés használatával töltse fel ezt a mezőt. 


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

A sentiment API most már használható a dokumentumokat a azok hangulati elemzéséhez.


```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

    {'documents': [{'id': '1', 'score': 0.7673527002334595},
                   {'id': '2', 'score': 0.18574094772338867},
                   {'id': '3', 'score': 0.5}],
     'errors': []}


A véleménypontszám a dokumentumok $ $0 és 1 USD$, között van, a nagyobb pozitív vélemények jelző magasabb pontszámot.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Kulcsszavak kinyerése

A Key kifejezés kinyerése API – a kulcskifejezések kigyűjti a szöveges dokumentum használata a [Kulcskifejezések metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Az útmutató ezen szakasza kiolvassa az angol és spanyol is dokumentumok kulcskifejezéseket.

A szolgáltatásvégpont a kulcs kulcsszókeresés szolgáltatás a következő URL-CÍMEN keresztül érhető el:


```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases


A dokumentumok gyűjteményét megegyezik a mi hangulatelemzés használták.


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```


    {'documents': [
        {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
        {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
        {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
        {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
     'errors': []
    }


A JSON-objektum még egyszer az alábbi kódsorok használatával HTML-táblázatként jeleníthetők meg:


```python
from IPython.display import HTML
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

## <a name="identify-linked-entities"></a>Kapcsolt entitások azonosítása

Az Entitáskapcsolási API azonosítja a jól ismert entitások egy szöveges dokumentum használata a [Entitáskapcsolás metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634). Az alábbi példa az angol nyelvű dokumentumok entitások azonosítja.

A szolgáltatásvégpont az entitás hivatkozási szolgáltatás a következő URL-CÍMEN keresztül érhető el:


```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/entities


A dokumentumok gyűjteménye nem éri el:


```python
documents = {'documents' : [
  {'id': '1', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.'},
  {'id': '2', 'text': 'The Seattle Seahawks won the Super Bowl in 2014.'}
]}
```

Most a dokumentumok is küldhetők a szövegelemzési API a válasz fogadására.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```
    {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "name": "Xbox One",
                        "matches": [
                            {
                                "text": "XBox One",
                                "offset": 23,
                                "length": 8
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "Xbox One",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/Xbox_One",
                        "bingId": "446bb4df-4999-4243-84c0-74e0f6c60e75"
                    },
                    {
                        "name": "Ultra-high-definition television",
                        "matches": [
                            {
                                "text": "4K",
                                "offset": 63,
                                "length": 2
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "Ultra-high-definition television",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/Ultra-high-definition_television",
                        "bingId": "7ee02026-b6ec-878b-f4de-f0bc7b0ab8c4"
                    }
                ]
            },
            {
                "id": "2",
                "entities": [
                    {
                        "name": "2013 Seattle Seahawks season",
                        "matches": [
                            {
                                "text": "Seattle Seahawks",
                                "offset": 4,
                                "length": 16
                            }
                        ],
                        "wikipediaLanguage": "en",
                        "wikipediaId": "2013 Seattle Seahawks season",
                        "wikipediaUrl": "https://en.wikipedia.org/wiki/2013_Seattle_Seahawks_season",
                        "bingId": "eb637865-4722-4eca-be9e-0ac0c376d361"
                    }
                ]
            }
        ],
        "errors": []
    }

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power bi-ban](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még 

 [Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
