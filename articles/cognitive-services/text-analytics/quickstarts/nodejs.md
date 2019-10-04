---
title: 'Gyors útmutató: A Node. js használatával hívja meg a Text Analytics REST API'
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
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 2b98949ca93be5ec0324561a053df730705af152
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671873"
---
# <a name="quickstart-use-nodejs-to-call-the-text-analytics-cognitive-service"></a>Gyors útmutató: A Node. js használata a Text Analytics kognitív szolgáltatás meghívásához  
<a name="HOLTop"></a>

Ebből a cikkből megtudhatja, hogyan [derítheti](#Detect)fel a nyelvet, [elemezheti a véleményét](#SentimentAnalysis), [kinyerheti a legfontosabb kifejezéseket](#KeyPhraseExtraction), és hogyan [azonosíthatja a csatolt entitásokat](#Entities) a [text Analytics API](//go.microsoft.com/fwlink/?LinkID=759711)-k  with

Az API-k műszaki dokumentációjáért lásd az [API-definíciókat](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Nyelvfelismerés

A Language Detection API a [Detect Language metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) használatával felismeri a szöveges dokumentumok nyelvét.

1. Hozzon létre `TEXT_ANALYTICS_SUBSCRIPTION_KEY` környezeti `TEXT_ANALYTICS_ENDPOINT` változókat és az erőforrás Azure-végpontját és előfizetési kulcsát. Ha ezeket a környezeti változókat az alkalmazás szerkesztésének megkezdése után hozta létre, akkor be kell állítania és újra meg kell nyitnia azt a szerkesztőt, IDE vagy rendszerhéjt, amelyet a változók eléréséhez használ.
1. Hozzon létre egy új Node. JS-projektet a kedvenc IDE vagy egy, az asztalon lévő mappában.
1. Adja hozzá az alább megadott kódot egy új `.js` fájlhoz.
1. Futtassa a programot az ide vagy a parancssorból, például `npm start` : vagy. `node detect.js`

```javascript
'use strict';

let https = require ('https');

const key_var = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('please set/export the following environment variable: ' + key_var);
}
const subscription_key = process.env[key_var];

const endpoint_var = 'TEXT_ANALYTICS_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('please set/export the following environment variable: ' + endpoint_var);
}
const endpoint = process.env[endpoint_var];

let path = '/text/analytics/v2.1/languages';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_language = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'text': 'This is a document written in English.' },
        { 'id': '2', 'text': 'Este es un document escrito en Español.' },
        { 'id': '3', 'text': '这是一个用中文写的文件' }
    ]
};

get_language(documents);
```

**Nyelvfelismerési válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}


```
<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Vélemények elemzése

A Sentiment Analysis API a szöveges bejegyzések hangulatát érzékeli a [Sentiment metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) használatával. Az érzelmek elemzése segítségével megtudhatja, hogy az ügyfelek mit gondolnak a márka vagy a téma alapján, ha a nyers szöveget a pozitív vagy negatív hangulatú nyomokra elemzi. Az alábbi példa két dokumentum pontszámait tartalmazza, amelyek közül az egyik az angol, a másik pedig a spanyol.

1. Hozzon létre `TEXT_ANALYTICS_SUBSCRIPTION_KEY` környezeti `TEXT_ANALYTICS_ENDPOINT` változókat és az erőforrás Azure-végpontját és előfizetési kulcsát. Ha ezeket a környezeti változókat az alkalmazás szerkesztésének megkezdése után hozta létre, akkor be kell állítania és újra meg kell nyitnia azt a szerkesztőt, IDE vagy rendszerhéjt, amelyet a változók eléréséhez használ.
1. Hozzon létre egy új Node. JS-projektet a kedvenc IDE vagy egy, az asztalon lévő mappában.
1. Adja hozzá az alább megadott kódot egy új `.js` fájlhoz.
1. Futtassa a programot az ide vagy a parancssorból, például `npm start` : vagy. `node sentiment.js`

```javascript
'use strict';

let https = require ('https');

const key_var = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('please set/export the following environment variable: ' + key_var);
}
const subscription_key = process.env[key_var];

const endpoint_var = 'TEXT_ANALYTICS_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('please set/export the following environment variable: ' + endpoint_var);
}
const endpoint = process.env[endpoint_var];

let path = '/text/analytics/v2.1/sentiment';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_sentiments = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
        { 'id': '2', 'language': 'es', 'text': 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' },
    ]
};

get_sentiments(documents);
```

**Hangulatelemzési válasz**

Az eredmény pozitív értékre van számítva, ha az értéke 1,0 és negatív, ha az értéke a 0,0-hoz közeledik.
A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Kulcsszavak keresése

A Key Phrase Extraction API kulcskifejezéseket nyer ki a szöveges dokumentumokból a [Key Phrases metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) használatával. A kulcsfontosságú kifejezés kibontásával gyorsan azonosíthatók a dokumentumok vagy szövegek fő pontjai. A következő példa kulcskifejezéseket nyer ki angol és spanyol nyelvű dokumentumokhoz.

1. Hozzon létre `TEXT_ANALYTICS_SUBSCRIPTION_KEY` környezeti `TEXT_ANALYTICS_ENDPOINT` változókat és az erőforrás Azure-végpontját és előfizetési kulcsát. Ha ezeket a környezeti változókat az alkalmazás szerkesztésének megkezdése után hozta létre, akkor be kell állítania és újra meg kell nyitnia azt a szerkesztőt, IDE vagy rendszerhéjt, amelyet a változók eléréséhez használ.
1. Hozzon létre egy új Node. JS-projektet a kedvenc IDE vagy egy, az asztalon lévő mappában.
1. Adja hozzá az alább megadott kódot egy új `.js` fájlhoz.
1. Futtassa a programot az ide vagy a parancssorból, például `npm start` : vagy. `node key-phrases.js`

```javascript
'use strict';

let https = require ('https');

const key_var = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('please set/export the following environment variable: ' + key_var);
}
const subscription_key = process.env[key_var];

const endpoint_var = 'TEXT_ANALYTICS_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('please set/export the following environment variable: ' + endpoint_var);
}
const endpoint = process.env[endpoint_var];

let path = '/text/analytics/v2.1/keyPhrases';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_key_phrases = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
        { 'id': '2', 'language': 'es', 'text': 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
        { 'id': '3', 'language': 'en', 'text': 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' }
    ]
};

get_key_phrases(documents);
```

**Kulcskifejezés-kinyerési válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-linked-entities"></a>Társított entitások azonosítása

Az Entities API azonosítja a szöveges dokumentumok jól ismert entitásait az [Entities metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) használatával. [](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) Az entitások szövegből kinyerik a szavakat, például a "Egyesült Államok" kifejezést, majd megadja a Word (ek) típus és/vagy wikipedia hivatkozását. A "Egyesült Államok" `location`típusa, míg a `https://en.wikipedia.org/wiki/United_States`wikipedia-ra mutató hivatkozás.  Az alábbi példa angol nyelvű dokumentumok entitásait azonosítja.

1. Hozzon létre `TEXT_ANALYTICS_SUBSCRIPTION_KEY` környezeti `TEXT_ANALYTICS_ENDPOINT` változókat és az erőforrás Azure-végpontját és előfizetési kulcsát. Ha ezeket a környezeti változókat az alkalmazás szerkesztésének megkezdése után hozta létre, akkor be kell állítania és újra meg kell nyitnia azt a szerkesztőt, IDE vagy rendszerhéjt, amelyet a változók eléréséhez használ.
1. Hozzon létre egy új Node. JS-projektet a kedvenc IDE vagy egy, az asztalon lévő mappában.
1. Adja hozzá az alább megadott kódot egy új `.js` fájlhoz.
1. Futtassa a programot az ide vagy a parancssorból, például `npm start` : vagy. `node entities.js`

```javascript
'use strict';

let https = require ('https');

const key_var = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('please set/export the following environment variable: ' + key_var);
}
const subscription_key = process.env[key_var];

const endpoint_var = 'TEXT_ANALYTICS_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('please set/export the following environment variable: ' + endpoint_var);
}
const endpoint = process.env[endpoint_var];

let path = '/text/analytics/v2.1/entities';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_entities = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'Microsoft is an It company.' }
    ]
};

get_entities(documents);
```

**Entitáskinyerési válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

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
