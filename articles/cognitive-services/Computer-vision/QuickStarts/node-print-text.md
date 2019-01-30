---
title: 'Gyors útmutató: Kinyerési nyomtatott szöveg-REST, a Node.js'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban nyomtatott szöveget fog kinyerni egy képből a Computer Vision API és a Node.js használatával.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c9dc64a39e86271131ef188c39d927455d0bd289
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215165"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-rest-api-and-nodejs-in-computer-vision"></a>Gyors útmutató: A REST API-t és a Node.js használatával a Computer Vision (OCR) nyomtatott szöveg kinyerése

Ebben a rövid útmutatóban optikai karakterfelismerést (OCR) használva nyomtatott szöveget fog kinyerni egy képből a Computer Vision REST API-jával. Az [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) metódussal nyomtatott szöveget észlelhet egy képen, és géppel olvasható karakterfolyamba nyerheti ki a felismert karaktereket.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A [Node.js](https://nodejs.org) 4.x-es vagy újabb verziójával kell rendelkeznie.
- Rendelkeznie kell az [npm-mel](https://www.npmjs.com/).
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Az előfizetői azonosító beszerzéséhez lásd az [előfizetői azonosítók beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Telepítse az npm [`request`](https://www.npmjs.com/package/request) csomagot.
   1. Nyissa meg a parancssori ablakot rendszergazdaként.
   1. Futtassa az alábbi parancsot:

      ```console
      npm install request
      ```

   1. A csomag sikeres telepítése után zárja be a parancssori ablakot.

1. Másolja az alábbi kódot egy szövegszerkesztőbe.
1. Hajtsa végre a következő módosításokat a kód megfelelő területein:
    1. Cserélje le a `subscriptionKey` értéket az előfizetői azonosítóra.
    1. Ha szükséges, cserélje le az `uriBase` értéket azon Azure-régió [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
    1. Ha szeretné, cserélje le az `imageUrl` értéket egy másik olyan kép URL-címére, amelyből nyomtatott szöveget szeretne kinyerni.
1. Mentse a kódot fájlként `.js` kiterjesztéssel. Például: `get-printed-text.js`.
1. Nyisson meg egy parancsablakot.
1. Amikor a rendszer kéri, a `node` paranccsal futtassa a fájlt. Például: `node get-printed-text.js`.

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

## <a name="examine-the-response"></a>A válasz vizsgálata

A rendszer JSON formátumban adja vissza a sikeres választ. A minta elemzi és megjeleníti a sikeres választ a parancssorban, a következő példához hasonló módon:

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a fájlt, majd távolítsa el az npm `request` csomagot. A csomag eltávolításához hajtsa végre a következő lépéseket:

1. Nyissa meg a parancssori ablakot rendszergazdaként.
2. Futtassa az alábbi parancsot:

   ```console
   npm uninstall request
   ```

3. A csomag sikeres eltávolítása után zárja be a parancssori ablakot.

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API-t, amellyel képeket elemezhet, hírességeket és nevezetességeket azonosíthat rajtuk, valamint miniatűrt hozhat létre, illetve nyomtatott és kézzel írott szövegeket nyerhet ki belőlük. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tekintse át a Computer Vision API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
