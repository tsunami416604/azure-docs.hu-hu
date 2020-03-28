---
title: 'Rövid útmutató: Arcok észlelése egy lemezképben az Azure REST API-val és a Node.js'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az Azure Face REST API-t fogja használni a Node.js használatával a lemezképek en lévő arcok észleléséhez.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: pafarley
ms.openlocfilehash: 7897c1602a8b9ed6a1fea68e80d57cd5bae1c004
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76165955"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-nodejs"></a>Rövid útmutató: Arcok észlelése egy lemezképben a Face REST API és a Node.js használatával

Ebben a rövid útmutatóban az Azure Face REST API-t fogja használni a Node.js használatával a rendszerkép emberi arcok észleléséhez.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek

- Face-előfizetési kulcs. Ingyenes próba-előfizetési kulcsot a [Cognitive Services kipróbálásával](https://azure.microsoft.com/try/cognitive-services/?api=face-api)szerezhetbe. Vagy kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) című részben található utasításokat, hogy előiratkozzon a Face szolgáltatásra, és levegye a kulcsot.
- Kódszerkesztő, például [Visual Studio-kód](https://code.visualstudio.com/download)

## <a name="set-up-the-node-environment"></a>A csomópontkörnyezet beállítása

Nyissa meg azt a mappát, amelyben létre szeretné hozni a projektet, és hozzon létre egy új fájlt, *a facedetection.js*fájlt. Ezután `requests` telepítse a modult a projekthez. Ez lehetővé teszi, hogy a parancsfájlok HTTP-kéréseket tegyenek.

```shell
npm install request --save
```

## <a name="write-the-nodejs-script"></a>A Node.js parancsfájl írása

Illessze be a következő kódot *a facedetection.js .* Ezek a mezők határozzák meg, hogyan lehet csatlakozni a Face szolgáltatáshoz, és hol lehet beszerezni a bemeneti adatokat. Frissítenie kell a `subscriptionKey` mezőt az előfizetési kulcs értékével, és `uriBase` módosítania kell a karakterláncot, hogy az a megfelelő végponti karakterláncot tartalmazzon. Előfordulhat, hogy módosítani `imageUrl` szeretné a mezőt, hogy a saját bemeneti lemezképére mutasson.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```javascript
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase = 'https://<My Endpoint String>.com/face/v1.0/detect';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg';
```

Ezután adja hozzá a következő kódot a Face API hívásához, és a bemeneti lemezképből arcattribútum-adatokat kapjon. A `returnFaceAttributes` mező határozza meg, hogy mely arcattribútumokat kell beolvasni. Előfordulhat, hogy a kívánt használattól függően módosítani szeretné ezt a karakterláncot.


```javascript
// Request parameters.
const params = {
    'returnFaceId': 'true',
    'returnFaceLandmarks': 'false',
    'returnFaceAttributes': 'age,gender,headPose,smile,facialHair,glasses,' +
        'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
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

## <a name="save-and-run-the-script"></a>A parancsfájl mentése és futtatása

A módosítások elvégzése után nyisson meg egy parancssort, `node` és futtassa a fájlt a paranccsal.

```
node facedetection.js
```

A konzolablakban json-adatként megjelenő arcadatoknak kell megjelennie. Példa:

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban írt egy Node.js parancsfájlt, amely meghívja az Azure Face szolgáltatást a rendszerkép arcok észleléséhez és az attribútumok visszaadásához. Ezután további információért tekintse meg a Face API referenciadokumentációját.

> [!div class="nextstepaction"]
> [Arcfelismerési API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
