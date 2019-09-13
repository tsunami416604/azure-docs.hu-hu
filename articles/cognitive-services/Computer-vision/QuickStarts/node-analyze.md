---
title: 'Gyors útmutató: Távoli rendszerkép elemzése a REST API és a Node. js-sel'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy képet fog elemezni a Computer Vision API és a Node.js segítségével.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18, seo-javascript-september2018
ms.openlocfilehash: c9d2c28a1faa2bceafb2b45f1dac76356e0e8753
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933947"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-with-nodejs"></a>Gyors útmutató: Távoli rendszerkép elemzése a Node. js-sel rendelkező Computer Vision REST API használatával

Ebben a rövid útmutatóban egy távolban tárolt képet fog elemezni vizuális jellemzők kinyerése érdekében a Computer Vision REST API-jával. Az [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) metódussal vizuális jellemzőket nyerhet ki a képek tartalma alapján.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A [Node.js](https://nodejs.org) 4.x-es vagy újabb verziójával kell rendelkeznie.
- Rendelkeznie kell az [npm-mel](https://www.npmjs.com/).
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. A [kipróbálási Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)ingyenes próbaverziós kulcsot is beszerezhet. Vagy kövesse a [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Computer Visionra való előfizetéshez és a kulcs beszerzéséhez című témakör utasításait. Ezután [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs-és szolgáltatás végponti `COMPUTER_VISION_SUBSCRIPTION_KEY` karakterláncához, a nevet és `COMPUTER_VISION_ENDPOINT`a-t.

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Telepítse az npm [`request`](https://www.npmjs.com/package/request) csomagot.
   1. Nyissa meg a parancssori ablakot rendszergazdaként.
   1. Futtassa a következő parancsot:

      ```console
      npm install request
      ```

   1. A csomag sikeres telepítése után zárja be a parancssori ablakot.

1. Másolja az alábbi kódot egy szövegszerkesztőbe.
1. Ha szeretné, cserélje le az `imageUrl` értéket egy olyan kép URL-címére, amelyet elemezni szeretne.
1. Igény szerint cserélje le a `language` kérésparaméter értékét egy másik nyelvre.
1. Mentse a kódot fájlként `.js` kiterjesztéssel. Például: `analyze-image.js`.
1. Nyisson meg egy parancsablakot.
1. Amikor a rendszer kéri, a `node` paranccsal futtassa a fájlt. Például: `node analyze-image.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v2.0/analyze';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
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
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a fájlt, majd távolítsa el az npm `request` csomagot. A csomag eltávolításához hajtsa végre a következő lépéseket:

1. Nyissa meg a parancssori ablakot rendszergazdaként.
2. Futtassa a következő parancsot:

   ```console
   npm uninstall request
   ```

3. A csomag sikeres eltávolítása után zárja be a parancssori ablakot.

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API-kat, amelyekkel képeket elemezhet, hírességeket és nevezetességeket azonosíthat rajtuk, valamint miniatűrt hozhat létre, illetve nyomtatott és kézzel írott szövegeket nyerhet ki belőlük. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tekintse át a Computer Vision API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
