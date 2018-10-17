---
title: 'Rövid útmutató: Arcfelismerés képen – Face API, cURL'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban arcokat fog felismerni egy képről a Face API segítségével és cURL használatával.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/10/2018
ms.author: nolachar
ms.openlocfilehash: 9ae8135481eb44e3b4b876fd4916e78a41c65042
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121904"
---
# <a name="quickstart-detect-faces-in-an-image-using-curl"></a>Rövid útmutató: Arcfelismerés egy képen cURL használatával

Ebben a rövid útmutatóban arcokat fog felismerni egy képen a Face API segítségével.

## <a name="prerequisites"></a>Előfeltételek

A minta futtatásához előfizetési kulcs szükséges. Ingyenes próba előfizetési kulcsot itt szerezhet: [A Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

A [Face - Detect](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) metódussal arcokat ismerhet fel a képeken, és többek között a következő arcattribútumokat adhatja vissza:

* Arcazonosító: Számos Face API-forgatókönyvben használt egyedi azonosító.
* Arcot jelölő téglalap: A képen az arc helyét jelző „bal oldalon, felül, szélesség és hosszúság” érték.
* Jellegzetes pontok: 27 pontból álló jellegzetes pontok egy tömbje, amely az arcösszetevők fontos részeire mutat.
* Az arcattribútumok közé tartozik az életkor, a nem, a mosoly intenzitása, a fejtartás és az arcszőrzet.

A minta futtatásához az alábbi lépéseket kell végrehajtania:

1. Nyisson meg egy parancssort.
2. A `<Subscription Key>` helyére írja be az érvényes előfizetési kulcsot.
3. Ha szükséges, változtassa meg az URL-címét (`https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect`) arra a címre, ahonnan beszerezte az előfizetési kulcsot.
4. Az elemezni kívánt képet (`"{\"url\":...`) is módosíthatja.
5. Másolja be a kódot a parancssor ablakba.
6. Futtassa a parancsot.

### <a name="face---detect-request"></a>Face - Detect kérés

> [!NOTE]
> A REST-hívásban ugyanazt a helyet kell használnia, ahonnan az előfizetési kulcsot beszerezte. Ha például a westus régióból szerezte be az előfizetési kulcsot, cserélje le a következő URL-címben látható „westcentralus” elemet a „westus” elemre.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="face---detect-response"></a>Face - Detect válasz

A rendszer JSON formátumban adja vissza a sikeres választ.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>További lépések

Felfedezheti a Face API-kat, amelyek a képeken emberi arcok felismerésére, az arcok téglalapba foglalására és attribútumok, például életkor és nem visszaadására használhatók.

> [!div class="nextstepaction"]
> [Face API-k](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
