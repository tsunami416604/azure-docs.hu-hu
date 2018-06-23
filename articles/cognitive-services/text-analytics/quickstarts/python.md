---
title: Python gyors üzembe helyezés az Azure kognitív szolgáltatások, Szövegelemzések API |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében a szöveg Analytics API-t a Microsoft Azure kognitív Services.
services: cognitive-services
author: ashmaka
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: b4c02767320b71912050ad511811767e6b5decf4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347762"
---
# <a name="quickstart-for-text-analytics-api-with-python"></a>Az API-t Python Szövegelemzések gyors üzembe helyezés 
<a name="HOLTop"></a>

Ez a forgatókönyv bemutatja, hogyan való [nyelvi észlelése](#Detect), [elemzése a céggel kapcsolatos véleményeket](#SentimentAnalysis), és [bontsa ki a legfontosabb kifejezések](#KeyPhraseExtraction) használatával a [szöveg Analytics API-k](//go.microsoft.com/fwlink/?LinkID=759711)a Python.

Futtathatja, ebben a példában Jupyter notebook [MyBinder](https://mybinder.org) az indítási kötő kattintva jelvények: 

[![Kötő](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

Tekintse meg a [API-definíciók](//go.microsoft.com/fwlink/?LinkID=759346) az API-k műszaki dokumentációját.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy [kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rendelkező **szöveg Analytics API**. Használhatja a **5000 tranzakciók/hónapban ingyenes szint** forgatókönyv végrehajtásához.

Rendelkeznie kell a [végpont és a hozzáférési kulcsot](../How-tos/text-analytics-how-to-access-key.md) meg a regisztráció során létrehozott. 

Ez a forgatókönyv folytatásához, cserélje le a `subscription_key` korábban beszerzett érvényes előfizetéssel kulccsal.


```python
subscription_key = None
assert subscription_key
```

Ezt követően ellenőrizze, hogy az a régió `text_analytics_base_url` megfelel-e a szolgáltatás használt. Ha egy ingyenes próba kulcsot használ, nem kell bármit módosíthat.


```python
text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/"
```

<a name="Detect"></a>

## <a name="detect-languages"></a>Nyelvek észlelése

A nyelvi észlelési API észleli a szöveg nyelvének dokumentálása, használja a [észlelése nyelvi metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7). A szolgáltatási végpont a régió nyelvi észlelés API a következő URL-CÍMEN keresztül érhető el:


```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/languages


Listájának áll a tartalom az API `documents`, minden egyes, amely tartalmaz egy `id` és egy `text` attribútum. A `text` attribútum tárolja az elemezni kívánt szöveg. 

Cserélje le a `documents` szótár nyelvi észlelési többi szöveget. 


```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

A következő pár sornyi kód hívásához a nyelvi észlelési API használatával a `requests` szalagtár Python meghatározásához a dokumentumokat a megfelelő nyelvet.


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


Az alábbi kódsorokat képezhető le a JSON-adatok egy HTML táblázatként.


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

A céggel kapcsolatos véleményeket elemzés API detexts a céggel kapcsolatos véleményeket a szöveg rekordkészlet, használja a [véleményeket metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9). A következő példában két dokumentumot, egy az angol és spanyol másik pontszámaihoz.

A szolgáltatásvégpont véleményeket elemzés a régió, a következő URL-CÍMEN keresztül érhető el:


```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment


A nyelvi észlelési példában a szolgáltatás egy szótár által biztosított, egy `documents` kulcs dokumentumok listáját tartalmazza. Minden egyes dokumentum álló rekordot a `id`, a `text` vizsgálandó és a `language` szöveg. A fenti szakaszban leírt nyelvi észlelését API segítségével feltölteni ezt a mezőt. 


```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

A céggel kapcsolatos véleményeket API már használható a dokumentumokat a hangulati elemek elemzése.


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


A dokumentum véleményeket pontszám között 0 $$ $1$ arról nagyobb pozitív véleményeket magasabb pontszám jelenti.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Kulcsszavak kinyerése

A kulcs kifejezés kibontási API key-kifejezések kiolvassa a egy szöveges dokumentum, használja a [kulcs kifejezések metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). Az útmutató ezen szakasza egyaránt angol és spanyol dokumentumok legfontosabb kifejezések bontja ki.

A szolgáltatási végpont a kulcs-kifejezés kibontási szolgáltatás a következő URL-CÍMEN keresztül érhető el:


```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases


A gyűjtemény dokumentumok megegyezik a céggel kapcsolatos véleményeket elemzés használt.


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


A JSON-objektum még egyszer az alábbi kódsorokat használatával HTML-táblázatként megjeleníthetők:


```python
from IPython.display import HTML
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

## <a name="identify-linked-entities"></a>Csatolt entitások azonosítása

Az entitás Linking API azonosítja a szöveg jól ismert entitások dokumentálása, használja a [entitás Linking metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634). Az alábbi példa angol dokumentumok entitások azonosítja.

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

Most a dokumentumok küldhető a szöveg Analytics API a választ kapnak.

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
> [A Power BI Szövegelemzések](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még 

 [Szöveg elemzés áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
