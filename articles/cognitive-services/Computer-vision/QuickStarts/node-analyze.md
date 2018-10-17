---
title: 'Rövid útmutató: Távoli kép elemzése – REST, Node.js – Computer Vision'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy képet fog elemezni a Computer Vision API és a Node.js segítségével.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 76174a14720502a444fb86a337445caf1910ff78
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631583"
---
# <a name="quickstart-analyze-a-remote-image-using-the-rest-api-with-nodejs-in-computer-vision"></a>Rövid útmutató: Távoli kép elemzése a REST API és a Node.js használatával a Computer Visionben

Ebben a rövid útmutatóban egy távol tárolt képet fog elemezni vizuális jellemzők kinyeréséhez a Computer Vision REST API-jával. Az [Analyze Image metódussal](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) vizuális jellemzőket nyerhet ki a képek tartalma alapján.

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
    1. Ha szükséges, cserélje le az `uriBase` értéket azon Azure-régió [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
    1. Ha szeretné, cserélje le az `imageUrl` értéket egy másik elemzendő kép URL-címére.
    1. Igény szerint cserélje le a `language` kérésparaméter értékét egy másik nyelvre.
1. Mentse a kódot fájlként `.js` kiterjesztéssel. Például: `analyze-image.js`.
1. Nyisson meg egy parancssort.
1. Amikor a rendszer kéri, a `node` paranccsal futtassa a fájlt. Például: `node analyze-image.js`.

```nodejs
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase =
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze';

const imageUrl =
    'http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

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
2. Futtassa az alábbi parancsot:

   ```console
   npm uninstall request
   ```

3. A csomag sikeres eltávolítása után zárja be a parancssori ablakot.

## <a name="next-steps"></a>További lépések

Ismerje meg a Computer Vision API-kat, amelyekkel képeket elemezhet, hírességeket és nevezetességeket azonosíthat rajtuk, valamint miniatűrt hozhat létre, illetve nyomtatott és kézzel írott szövegeket nyerhet ki belőlük. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tekintse át a Computer Vision API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
