---
title: 'Gyors útmutató: A szövegelemzési API meghívására Node.js használatával'
titleSuffix: Azure Cognitive Services
description: Ezekkel a rövid útmutatókkal és kódmintákkal gyorsan kezdheti meg a Text Analytics API használatát a Microsoft Cognitive Services-ben az Azure-on.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/15/2019
ms.author: aahi
ms.openlocfilehash: 61cfb5fa78a735d2ef542c30b445f3200f256d7c
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226929"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Gyors útmutató: A Text Analytics kognitív szolgáltatás hívásához Node.js használatával  
<a name="HOLTop"></a>

Ez a cikk bemutatja, hogyan való [nyelvfelismerés](#Detect), [vélemények elemzése](#SentimentAnalysis), [kinyerheti a kulcskifejezéseket](#KeyPhraseExtraction), és [kapcsolt entitások azonosítása](#Entities) használatával a [Text Analytics API-k](//go.microsoft.com/fwlink/?LinkID=759711) a node.js használatával.

Az API-k műszaki dokumentációjáért lásd az [API-definíciókat](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

A regisztráció során létrejött [végponttal és hozzáférési kulccsal](../How-tos/text-analytics-how-to-access-key.md) is rendelkeznie kell.

<a name="Detect"></a>

## <a name="detect-language"></a>Nyelv felismerése

A Language Detection API a [Detect Language metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) használatával felismeri a szöveges dokumentumok nyelvét.

1. Hozzon létre egy új Node.JS-projektet a kedvenc IDE vagy az asztal az egyik mappájába.
2. Adja hozzá a kódot egy új lejjebb `.js` fájlt.
3. Cserélje le a `accessKey` érték és a egy előfizetési kulcsot a Text Analytics erőforrásból az Azure-ban.
4. Cserélje le a `uri` helyét (jelenleg `westus`) a regisztrált régióra.
5. Futtassa a programot az IDE vagy az parancssor, például `npm start` vagy `node detect.js`.

```javascript
'use strict';

let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
let accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
let uri = 'westus.api.cognitive.microsoft.com';
let path = '/text/analytics/v2.0/languages';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
        console.log (body__);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_language = function (documents) {
    let body = JSON.stringify (documents);

    let request_params = {
        method : 'POST',
        hostname : uri,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : accessKey,
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (body);
    req.end ();
}

let documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]};

get_language (documents);
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

A Sentiment Analysis API a szöveges bejegyzések hangulatát érzékeli a [Sentiment metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) használatával. Hangulatelemzés segítségével ismerje meg, hogy alkotott ügyfélvéleményeket a márkájáról vagy a témakör a keresőmotorok pozitív vagy negatív véleményeket kapcsolatos nyers szöveg elemzésével. Az alábbi példa a pontszámok biztosít két dokumentumot, egy angol nyelvű, míg a másik spanyol.

1. Hozzon létre egy új Node.JS-projektet a kedvenc IDE vagy az asztal az egyik mappájába.
2. Adja hozzá a kódot egy új lejjebb `.js` fájlt.
3. Cserélje le a `accessKey` érték és a egy előfizetési kulcsot a Text Analytics erőforrásból az Azure-ban.
4. Cserélje le a `uri` helyét (jelenleg `westus`) a regisztrált régióra.
5. Futtassa a programot az IDE vagy az parancssor, például `npm start` vagy `node sentiment.js`.

```javascript
'use strict';

let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
let accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
let uri = 'westus.api.cognitive.microsoft.com';
let path = '/text/analytics/v2.0/sentiment';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
        console.log (body__);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_sentiments = function (documents) {
    let body = JSON.stringify (documents);

    let request_params = {
        method : 'POST',
        hostname : uri,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : accessKey,
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (body);
    req.end ();
}

let documents = { 'documents': [
    { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id': '2', 'language': 'es', 'text': 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' },
]};

get_sentiments (documents);
```

**Hangulatelemzési válasz**

Az eredmény, ha azt sorolódik közelebb 1.0-s és a negatív közelebb van pontozását 0.0, ha pozitív mérjük.
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

## <a name="extract-key-phrases"></a>Kulcsszavak kinyerése

A Key Phrase Extraction API kulcskifejezéseket nyer ki a szöveges dokumentumokból a [Key Phrases metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) használatával. Kulcskifejezések kinyerése segítségével gyorsan azonosíthatja a szöveg és a dokumentum a fő pontokat. A következő példa kulcskifejezéseket nyer ki angol és spanyol nyelvű dokumentumokhoz.

1. Hozzon létre egy új Node.JS-projektet a kedvenc IDE vagy az asztal az egyik mappájába.
2. Adja hozzá a kódot egy új lejjebb `.js` fájlt.
3. Cserélje le a `accessKey` érték és a egy előfizetési kulcsot a Text Analytics erőforrásból az Azure-ban.
4. Cserélje le a `uri` helyét (jelenleg `westus`) a regisztrált régióra.
5. Futtassa a programot az IDE vagy az parancssor, például `npm start` vagy `node key-phrases.js`.

```javascript
'use strict';

let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
let accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
let uri = 'westus.api.cognitive.microsoft.com';
let path = '/text/analytics/v2.0/keyPhrases';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
        console.log (body__);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_key_phrases = function (documents) {
    let body = JSON.stringify (documents);

    let request_params = {
        method : 'POST',
        hostname : uri,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : accessKey,
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (body);
    req.end ();
}

let documents = { 'documents': [
    { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
    { 'id': '2', 'language': 'es', 'text': 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
    { 'id': '3', 'language': 'en', 'text': 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' }
]};

get_key_phrases (documents);
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

Az Entities API azonosítja a szöveges dokumentumok jól ismert entitásait az [Entities metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) használatával. [Entitások](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) kinyerheti a szöveget, például a "Egyesült Államok", majd biztosítson a típusa és/vagy a Wikipédia-hivatkozás esetében a szavak. A típus az "Egyesült Államok" `location`, míg a Wikipedia hivatkozása `https://en.wikipedia.org/wiki/United_States`.  Az alábbi példa angol nyelvű dokumentumok entitásait azonosítja.

1. Hozzon létre egy új Node.JS-projektet a kedvenc IDE vagy az asztal az egyik mappájába.
2. Adja hozzá a kódot egy új lejjebb `.js` fájlt.
3. Cserélje le a `accessKey` érték és a egy előfizetési kulcsot a Text Analytics erőforrásból az Azure-ban.
4. Cserélje le a `uri` helyét (jelenleg `westus`) a regisztrált régióra.
5. Futtassa a programot az IDE vagy az parancssor, például `npm start` vagy `node entities.js`.

```javascript
'use strict';

let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the accessKey string value with your valid access key.
let accessKey = 'enter key here';

// Replace or verify the region.

// You must use the same region in your REST API call as you used to obtain your access keys.
// For example, if you obtained your access keys from the westus region, replace 
// "westcentralus" in the URI below with "westus".

// NOTE: Free trial access keys are generated in the westcentralus region, so if you are using
// a free trial access key, you should not need to change this region.
let uri = 'westus.api.cognitive.microsoft.com';
let path = '/text/analytics/v2.1-preview/entities';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
        console.log (body__);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_entities = function (documents) {
    let body = JSON.stringify (documents);

    let request_params = {
        method : 'POST',
        hostname : uri,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : accessKey,
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (body);
    req.end ();
}

let documents = { 'documents': [
    { 'id': '1', 'language': 'en', 'text': 'Jeff bought three dozen eggs because there was a 50% discount.' },
    { 'id': '2', 'language': 'en', 'text': 'The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.' }
]};

get_entities (documents);
```

**Entitáskinyerési válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
    "Documents": [
        {
            "Id": "1",
            "Entities": [
                {
                    "Name": "Jeff",
                    "Matches": [
                        {
                            "Text": "Jeff",
                            "Offset": 0,
                            "Length": 4
                        }
                    ],
                    "Type": "Person"
                },
                {
                    "Name": "three dozen",
                    "Matches": [
                        {
                            "Text": "three dozen",
                            "Offset": 12,
                            "Length": 11
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50",
                    "Matches": [
                        {
                            "Text": "50",
                            "Offset": 49,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "50%",
                    "Matches": [
                        {
                            "Text": "50%",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        },
        {
            "Id": "2",
            "Entities": [
                {
                    "Name": "Great Depression",
                    "Matches": [
                        {
                            "Text": "The Great Depression",
                            "Offset": 0,
                            "Length": 20
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Great Depression",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                    "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                },
                {
                    "Name": "1929",
                    "Matches": [
                        {
                            "Text": "1929",
                            "Offset": 30,
                            "Length": 4
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "By 1933",
                    "Matches": [
                        {
                            "Text": "By 1933",
                            "Offset": 36,
                            "Length": 7
                        }
                    ],
                    "Type": "DateTime",
                    "SubType": "DateRange"
                },
                {
                    "Name": "Gross domestic product",
                    "Matches": [
                        {
                            "Text": "GDP",
                            "Offset": 49,
                            "Length": 3
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "Gross domestic product",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                    "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                },
                {
                    "Name": "United States",
                    "Matches": [
                        {
                            "Text": "America",
                            "Offset": 56,
                            "Length": 7
                        }
                    ],
                    "WikipediaLanguage": "en",
                    "WikipediaId": "United States",
                    "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                    "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                    "Type": "Location"
                },
                {
                    "Name": "25",
                    "Matches": [
                        {
                            "Text": "25",
                            "Offset": 72,
                            "Length": 2
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Number"
                },
                {
                    "Name": "25%",
                    "Matches": [
                        {
                            "Text": "25%",
                            "Offset": 72,
                            "Length": 3
                        }
                    ],
                    "Type": "Quantity",
                    "SubType": "Percentage"
                }
            ]
        }
    ],
    "Errors": []
}
```



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még 

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
