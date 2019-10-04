---
title: 'Gyors útmutató: Rendszerképek észlelése az Azure REST API és cURL'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az Azure Face REST API és a cURL használatával fogja felderíteni a képekben lévő arcokat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 09/06/2019
ms.author: pafarley
ms.openlocfilehash: da929744922f8653bc293b68dbbadb9347a447e9
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859149"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Gyors útmutató: Arcok észlelése egy képen a Face REST API és a cURL használatával

Ebben a rövid útmutatóban az Azure Face REST API és a cURL használatával fogja felderíteni az emberi arcokat egy képben.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- A Face API előfizetési kulcs. Megjelenik a származó ingyenes próba-előfizetését kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Másik lehetőségként kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Face API szolgáltatás és a kulcs beszerzése.

## <a name="write-the-command"></a>A parancs írása
 
A következőhöz hasonló parancsot kell használnia a Face API meghívásához és a Face attribútum adatainak a rendszerképből való lekéréséhez. Először másolja a kódot egy&mdash;szövegszerkesztőbe, hogy a Futtatás előtt módosítania kell a parancs egyes részeit.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://<My Endpoint String>.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Előfizetői azonosító
A `<Subscription Key>` helyére írja be az érvényes Face előfizetési kulcsot.

### <a name="face-endpoint-url"></a>Szembenéző végpont URL-címe

Az URL `https://<My Endpoint String>.com/face/v1.0/detect` -cím a lekérdezni kívánt Azure Face-végpontot jelzi. Előfordulhat, hogy módosítania kell az URL első részét, hogy az megfeleljen az előfizetési kulcsnak megfelelő végpontnak.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="url-query-string"></a>URL-lekérdezési karakterlánc

Az arc végpont URL-címének lekérdezési karakterlánca határozza meg, hogy melyik arc-attribútumokat kell lekérni. Előfordulhat, hogy módosítani kívánja ezt a karakterláncot a kívánt felhasználási módtól függően.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>Rendszerkép forrásának URL-címe
A forrás URL-címe jelzi a bemenetként használandó képet. Ezt úgy változtathatja meg, hogy bármely elemezni kívánt képre mutasson.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Futtassa a

Miután elvégezte a módosításokat, nyisson meg egy parancssort, és írja be az új parancsot. A konzol ablakában a JSON-adatokként megjelenő arc információit kell látnia. Példa:

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

Ebben a rövid útmutatóban egy olyan cURL-parancsot írt, amely meghívja az Azure Face APIt, hogy felderítse az arcokat egy képben, és visszaállítsa az attribútumokat. További információért olvassa el a Face API dokumentációját.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
