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
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: 69eb3789586233b824da1ef6a9c338b07281f324
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001388"
---
# <a name="quickstart-using-python-to-call-the-text-analytics-cognitive-service"></a>Gyors útmutató: A Text Analytics kognitív szolgáltatás hívásához Python használatával 
<a name="HOLTop"></a>

Ebből az útmutatóból megtudhatja, hogyan [észlelheti a nyelvet](#Detect), [elemezhet hangulatot](#SentimentAnalysis) és [nyerhet ki kulcskifejezéseket](#KeyPhraseExtraction) a [Text Analytics API-kkal](//go.microsoft.com/fwlink/?LinkID=759711) a Python használatával.

Ebben a példában a parancssorból vagy egy Jupyter notebookot futtathatja [MyBinder](https://mybinder.org) jelvények Binder indításkor kattintva:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

### <a name="command-line"></a>Parancssor

Előfordulhat, hogy frissíteni kell [IPython](https://ipython.org/install.html), a Jupyter a kernel:
```bash
pip install --upgrade IPython
```

Előfordulhat, hogy frissíteni kell a [kérelmek](http://docs.python-requests.org/en/master/) könyvtár:
```bash
pip install requests
```

Az API-k műszaki dokumentációjáért lásd az [API-definíciókat](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Előfeltételek

* [!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

* A [végpontját és hozzáférési kulcsát](../How-tos/text-analytics-how-to-access-key.md) létrehozott az Ön számára a regisztrációhoz.

* Az alábbi importálásokat, az előfizetési kulcsot, és `text_analytics_base_url` minden az alábbi rövid útmutatók szolgálnak. Adja hozzá a import.

    ```python
    import requests
    # pprint is pretty print (formats the JSON)
    from pprint import pprint
    from IPython.display import HTML
    ```
    
    Adja hozzá ezeket a sorokat, majd cserélje le `subscription_key` korábban beszerzett érvényes előfizetéssel kulccsal.
    
    ```python
    subscription_key = '<ADD KEY HERE>'
    assert subscription_key
    ```
    
    Ezután adja hozzá ezt a sort, majd ellenőrizze, hogy az a régió `text_analytics_base_url` felel meg, amelyet használ, a szolgáltatás beállítása során. Ha egy ingyenes próba-kulcsot használ, nem kell bármin változtatni.
    
    ```python
    text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
    ```

<a name="Detect"></a>

## <a name="detect-languages"></a>Nyelvek felismerése

A Language Detection API a [Detect Language metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) használatával felismeri a szöveges dokumentumok nyelvét. A nyelvfelismerési API szolgáltatásvégpontja az Ön régiójához az alábbi URL-címen érhető el:

```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages


Az API hasznos adatait `documents` egy listája teszi ki, amelyek mindegyike tartalmaz egy `id` és egy `text` attribútumot. A `text` attribútum az elemzendő szöveget tárolja. 

Nyelvfelismeréshez cserélje le a `documents` szótárt bármely más szöveggel.

```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

A következő néhány kódsor a dokumentumok nyelvének felismeréséhez meghívja a nyelvfelismerő API-t a Python nyelvű `requests` kódtár használatával.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

Az alábbi kódsorok a JSON-adatokat egy HTML-táblaként jelenítik meg.

```python
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

A sikeres JSON-választ:

```json
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
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Vélemények elemzése

A Sentiment Analysis API felismeri a vélemény (tartomány közötti pozitív vagy negatív) szöveg rekordkészlet, a használatával a [vélemények metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). A következő példa két dokumentumhoz rendel pontszámot, az egyik angol, a másik spanyol nyelvű.

A hangulatelemzés szolgáltatásvégpontja az Ön régiójához az alábbi URL-címen érhető el:

```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment

Ahogy a nyelvfelismerési példában, a szolgáltatás ebben esetben is el van látva egy `documents` kulccsal rendelkező szótárral, amely egy dokumentumlistából áll. Minden dokumentum egy rekord, amely az `id`, az elemzendő `text` és a szöveg `language` tulajdonságából áll. Ennek a mezőnek a feltöltéséhez használhatja az előző szakasz nyelvfelismerési API-ját.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

A hangulati API-t mostantól használhatja a dokumentumokban található hangulatok elemzéséhez.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

A sikeres JSON-választ:

```json
{'documents': [{'id': '1', 'score': 0.7673527002334595},
                {'id': '2', 'score': 0.18574094772338867},
                {'id': '3', 'score': 0.5}],
    'errors': []}
```

A dokumentumok értelmezhetőségi pontszámot 0,0 és 1,0, között van, a nagyobb pozitív vélemények jelző magasabb pontszámot.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Kulcsszavak kinyerése

A Key Phrase Extraction API kulcskifejezéseket nyer ki a szöveges dokumentumokból a [Key Phrases metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) használatával. Az útmutatónak ez a szakasza kulcskifejezéseket nyer ki angol és spanyol nyelvű dokumentumokhoz.

A kulcskifejezés-kinyerés szolgáltatásvégpontja az alábbi URL-címen érhető el:

```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases

A dokumentumgyűjtemény megegyezik a hangulatelemzésnél használttal.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

A JSON-objektum a következő kódsorokat használatával HTML-táblázatként jeleníthetők meg:

```python
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

A következő néhány kódsor a dokumentumok nyelvének felismeréséhez meghívja a nyelvfelismerő API-t a Python nyelvű `requests` kódtár használatával.
```python
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

A sikeres JSON-választ:
```json
{'documents': [
    {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
    {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
    {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
    {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
    'errors': []
}
```

## <a name="identify-entities"></a>Entitások azonosítása

Az Entities API azonosítja a szöveges dokumentumok jól ismert entitásait az [Entities metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) használatával. Az alábbi példa angol nyelvű dokumentumok entitásait azonosítja.

Az entitáskapcsoló szolgáltatás szolgáltatásvégpontja az alábbi URL-címen érhető el:

```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/entities

A dokumentumgyűjtemény alább látható:

```python
documents = {'documents' : [
  {'id': '1', 'text': 'Microsoft is an It company.'}
]}
```
A dokumentumokat most elküldheti a Text Analytics API-nak, hogy megkapja a választ.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```

A sikeres JSON-választ:
```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
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
