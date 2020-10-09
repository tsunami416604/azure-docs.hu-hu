---
title: 'Gyors útmutató: rendszerképek észlelése az Azure REST API és a cURL'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az Azure Face REST API és a cURL használatával fogja felderíteni a képekben lévő arcokat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: cd7d1a476f09a2fbfffa687a28616c8faeaae22c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858268"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Rövid útmutató: az arcok észlelése egy képen a Face REST API és a cURL használatával

Ebben a rövid útmutatóban az Azure Face REST API és a cURL használatával ismeri fel az emberi arcokat egy képben.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/), mielőtt hozzákezd. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" hozzon létre egy Face-erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Face APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="write-the-command"></a>A parancs írása
 
A következőhöz hasonló parancsot kell használnia a Face API meghívásához és a Face attribútum adatainak a rendszerképből való lekéréséhez. Először másolja a kódot egy szövegszerkesztőbe, hogy &mdash; a Futtatás előtt módosítania kell a parancs egyes részeit.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" id="detection_model_2":::

### <a name="subscription-key"></a>Előfizetői azonosító
A helyére írja `<Subscription Key>` be az érvényes Face előfizetési kulcsot.

### <a name="face-endpoint-url"></a>Szembenéző végpont URL-címe

Az URL-cím `https://<My Endpoint String>.com/face/v1.0/detect` a lekérdezni kívánt Azure Face-végpontot jelzi. Előfordulhat, hogy módosítania kell az URL első részét, hogy az megfeleljen az előfizetési kulcsnak megfelelő végpontnak.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="image-source-url"></a>Rendszerkép forrásának URL-címe
A forrás URL-címe jelzi a bemenetként használandó képet. Ezt úgy változtathatja meg, hogy bármely elemezni kívánt képre mutasson.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Futtassa a

Miután elvégezte a módosításokat, nyisson meg egy parancssort, és írja be az új parancsot. A konzol ablakában a JSON-adatokként megjelenő arc információit kell látnia. Például:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

## <a name="extract-face-attributes"></a>Arc attribútumainak kinyerése
 
A Face attribútumok kinyeréséhez használja az 1. észlelési modellt, és adja hozzá a `returnFaceAttributes` lekérdezési paramétert. A parancsnak ekkor a következőhöz hasonlóan kell kinéznie. Ahogy korábban is, szúrja be az arc-előfizetési kulcsot és a végpontot.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" id="detection_model_1":::

A visszaadott arc adatai mostantól a Face attribútumokat is tartalmazzák. Például:

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

Ebben a rövid útmutatóban egy cURL-parancsot írt, amely meghívja az Azure Face Service-t, hogy felderítse a képekben lévő arcokat, és visszaállítsa az attribútumokat. További információért olvassa el a Face API dokumentációját.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
