---
title: 'Rövid útmutató: Arcfelismerés egy képen a REST API és a Go használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a Face szolgáltatás és a go használatával azonosíthatja a képekből származó arcokat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: b9f2d3397e0a2067cb173741a0037422021f3d87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858251"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-go"></a>Rövid útmutató: Arcfelismerés egy képen a REST API és a Go használatával

Ebben a rövid útmutatóban az Azure Face REST API a go használatával ismeri fel az emberi arcokat a képen.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" hozzon létre egy Face-erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Face APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
- Kódszerkesztő, például [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="write-the-script"></a>A parancsfájl írása

Hozzon létre egy új fájlt, _faceDetection. go_, és adja hozzá a következő kódot. Ez meghívja a Face API egy adott képurl-címhez.

:::code language="go" source="~/cognitive-services-quickstart-code/go/Face/rest/detect.go":::

Frissítenie kell az értéket az `subscriptionKey` előfizetési kulccsal, és módosítania kell a `uriBase` karakterláncot úgy, hogy az tartalmazza a megfelelő végponti karakterláncot.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

Azt is megteheti, hogy módosítani szeretné a `imageUrl` mezőt úgy, hogy a saját bemeneti képére mutasson. Előfordulhat, hogy módosítani kívánja azt a `returnFaceAttributes` mezőt is, amely megadja a lekérdezni kívánt Arcfelismerés-attribútumokat.

## <a name="run-the-script"></a>A szkript futtatása

Nyisson meg egy parancssort, és hozza létre a programot a következő paranccsal:

```shell
go build faceDetection.go
```

Ezután futtassa a programot:

```shell
detect-face
```

Meg kell jelennie a konzolon kinyomtatott, észlelt arc típusú információk JSON-karakterláncának. A következő példa egy sikeres JSON-választ mutat be.

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    }
  }
]
```

## <a name="extract-face-attributes"></a>Arc attribútumainak kinyerése
 
A Face attribútumok kinyeréséhez használja az 1. észlelési modellt, és adja hozzá a `returnFaceAttributes` lekérdezési paramétert.

```go
const params = "?detectionModel=detection_01&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise"
```

A válasz mostantól a Face attribútumokat is tartalmazza. Például:

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

Ebben a rövid útmutatóban egy go-konzol alkalmazást írt, amely meghívja az Azure Face Service-t, hogy felderítse a képekben lévő arcokat, és visszaállítsa az attribútumokat. További információért olvassa el a Face API dokumentációját.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
