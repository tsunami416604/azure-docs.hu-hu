---
title: 'Rövid útmutató: Arcok észlelése egy lemezképben az Azure REST API-val és a cURL-lel'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az Azure Face REST API cURL-lel egy lemezkép arcok észlelésére.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 7e7eb9ba942cd29db824388c02b2933c533c4b45
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403021"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Rövid útmutató: Arcok észlelése egy lemezképben a Face REST API és a cURL használatával

Ebben a rövid útmutatóban az Azure Face REST API cURL-lel való használatával észlelheti az emberi arcokat egy lemezképben.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek

- Face-előfizetési kulcs. Ingyenes próba-előfizetési kulcsot a [Cognitive Services kipróbálásával](https://azure.microsoft.com/try/cognitive-services/?api=face-api)szerezhetbe. Vagy kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) című részben található utasításokat, hogy előiratkozzon a Face szolgáltatásra, és levegye a kulcsot.

## <a name="write-the-command"></a>Írja meg a parancsot
 
A következőhöz hasonló parancsot fog használni a Face API hívásához és arcattribútum-adatok behívásához egy lemezképből. Először másolja a kódot&mdash;egy szövegszerkesztőbe, és a futtatás előtt módosítania kell a parancs bizonyos részeit.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://<My Endpoint String>.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Előfizetői azonosító
Cserélje `<Subscription Key>` le érvényes Face-előfizetési kulcsát.

### <a name="face-endpoint-url"></a>Face végpont URL-címe

Az `https://<My Endpoint String>.com/face/v1.0/detect` URL-cím azt jelzi, hogy az Azure Face-végpont lekérdezésre. Előfordulhat, hogy módosítania kell az URL-cím első részét, hogy megfeleljen az előfizetési kulcsnak megfelelő végpontnak.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="url-query-string"></a>URL-lekérdezési karakterlánc

A Face végpont URL-címének lekérdezési karakterlánca határozza meg, hogy mely arcattribútumokat kell beolvasni. Előfordulhat, hogy a kívánt használattól függően módosítani szeretné ezt a karakterláncot.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>Képforrás URL-címe
A forrás URL-címe azt jelzi, hogy a képet bemenetként kell használni. Ezt módosíthatja úgy, hogy az elemezni kívánt képekre mutasson.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Futtassa a

A módosítások végrehajtása után nyisson meg egy parancssort, és írja be az új parancsot. A konzolablakban json-adatként megjelenő arcadatoknak kell megjelennie. Például:

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

Ebben a rövid útmutatóban írt egy cURL-parancsot, amely meghívja az Azure Face szolgáltatást a rendszerképek arcok észleléséhez és az attribútumok visszaadásához. Ezután további információért tekintse meg a Face API referenciadokumentációját.

> [!div class="nextstepaction"]
> [Arcfelismerési API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
