---
title: OCR a Computer Vision API és a Node.js használatával – rövid útmutató | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ebben a rövid útmutatóban nyomtatott szöveget fog kinyerni egy képből a Computer Vision és a Node.js használatával a Cognitive Servicesben.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 73fe11800d7141c9e6e7272a0d008d2bcdf6d6af
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43770146"
---
# <a name="quickstart-extract-printed-text-ocr---rest-nodejs"></a>Rövid útmutató: Nyomtatott szöveg kinyerése (OCR) – REST, Node.js

Ebben a rövid útmutatóban nyomtatott szöveget fog kinyerni – más néven optikai karakterfelismerést (OCR) fog végezni – egy képből a Computer Vision segítségével.

## <a name="prerequisites"></a>Előfeltételek

A Computer Vision használatához előfizetési kulcsra van szüksége, lásd az [előfizetési kulcsok beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.

## <a name="ocr-request"></a>OCR-kérés

Az [OCR metódussal](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) nyomtatott szöveget észlelhet egy képen, és géppel olvasható karakterfolyamba nyerheti ki a felismert karaktereket.

A minta futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szerkesztőbe.
1. A `<Subscription Key>` helyére írja be az érvényes előfizetési kulcsot.
1. Ha szükséges, változtassa meg az `uriBase` értékét arra a helyre, ahonnan az előfizetési kulcsot beszerezte.
1. Az `imageUrl` elem értékét beállíthatja az elemezni kívánt kép elérési útjára.
1. Mentse a fájlt `.js` kiterjesztéssel.
1. Nyissa meg a Node.js-parancssort, és futtassa a fájlt (például: `node myfile.js`).

Ebben a példában az npm [request](https://www.npmjs.com/package/request) csomagját használjuk.

```nodejs
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase =
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr';

const imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/' +
    'Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png';

// Request parameters.
const params = {
    'language': 'unk',
    'detectOrientation': 'true',
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="ocr-response"></a>OCR-válasz

A sikeres OCR-művelet eredményei közé tartozik a szöveg, valamint az egyes régiókat, sorokat és szavakat határoló keretek, például:

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API-kat, amelyekkel képeket elemezhet, hírességeket és nevezetességeket azonosíthat rajtuk, valamint miniatűrt hozhat létre, illetve nyomtatott és kézzel írott szövegeket nyerhet ki belőlük. A Computer Vision API-kkal való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Ismerkedjen meg a Computer Vision API-k működésével](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
