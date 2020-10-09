---
title: 'Gyors útmutató: rendszerképek észlelése az Azure REST API és a Java szolgáltatással'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az Azure Face REST API Javával fogja felderíteni az arcokat a képen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: fbe62cf00422710e18a6b112adc08f19ea03177b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858353"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Rövid útmutató: Arcfelismerés egy képen a REST API és a Java használatával

Ebben a rövid útmutatóban az Azure Face REST API a Java használatával ismeri fel az emberi arcokat egy képben.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/), mielőtt hozzákezd. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" hozzon létre egy Face-erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Face APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* Bármely tetszőleges Java IDE.

## <a name="create-the-java-project"></a>A Java-projekt létrehozása

1. Hozzon létre egy új parancssori Java-alkalmazást az IDE-ben, és adjon hozzá egy **Main osztályt** a **Main** metódussal.
1. Importálja az alábbi kódtárakat a Java-projektbe. Ha Mavent használ, a Maven-koordináták mindegyik kódtár esetében meg vannak adva.
   - [Apache HTTP-ügyfél](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: httpclient: 4.5.6)
   - [Apache http Core](https://hc.apache.org/downloads.cgi) (org. Apache. httpcomponents: httpcore: 4.4.10)
   - [JSON-kódtár](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Apache Commons fakitermelés](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (Commons-naplózás: Commons-fakitermelés: 1.1.2)

## <a name="add-face-detection-code"></a>Arcfelismerés kód hozzáadása

Nyissa meg a projekt fő osztályát. Itt hozzá kell adnia a lemezképek betöltéséhez és az arcok észleléséhez szükséges kódot.

### <a name="import-packages"></a>Csomagok importálása

Adja hozzá a következő `import` utasításokat a fájl elejéhez.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="dependencies":::

### <a name="add-essential-fields"></a>Alapvető mezők hozzáadása

Cserélje le a **Main** osztályt a következő kódra. Ezek az adatok határozzák meg, hogyan csatlakozhat a Face szolgáltatáshoz, és honnan szerezhető be a bemeneti adatok. Frissítenie kell a mezőt az `subscriptionKey` előfizetési kulcs értékével, és módosítania kell a `uriBase` karakterláncot úgy, hogy az tartalmazza a megfelelő végponti karakterláncot. Azt is megteheti, hogy az `imageWithFaces` értéket olyan elérési útra szeretné beállítani, amely egy másik képfájlra mutat.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

A `faceAttributes` mező csak bizonyos típusú attribútumok listáját tartalmazza. Meghatározza, hogy mely információk kérhetők le az észlelt arcokról.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="environment":::

### <a name="call-the-face-detection-rest-api"></a>Arcfelismerés REST API hívása

Adja hozzá a **Main** metódust a következő kóddal. Egy REST-hívást hoz létre a Face API a távoli rendszerképben lévő arc-információk észleléséhez (ez a `faceAttributes` karakterlánc határozza meg a lekérdezni kívánt Arcfelismerés-attribútumokat). Ezután a kimeneti adatokat egy JSON-karakterláncba írja.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="main":::

### <a name="parse-the-json-response"></a>A JSON-válasz elemzése

Közvetlenül az előző kód alatt adja hozzá a következő blokkot, amely átalakítja a visszaadott JSON-értékeket egy könnyebben olvasható formátumba, mielőtt kinyomtatja a konzolra. Végül zárjuk le a try-catch blokkot, a **Main** metódust és a **Main** osztályt.

:::code language="java" source="~/cognitive-services-quickstart-code/java/Face/rest/detect.java" id="print":::

## <a name="run-the-app"></a>Az alkalmazás futtatása

Fordítsa le a kódot, és futtassa. A sikeres válasz a konzol ablakában könnyen olvasható JSON formátumban jeleníti meg az Arcfelismerés. Például:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  }
}]
```

## <a name="extract-face-attributes"></a>Arc attribútumainak kinyerése
 
A Face attribútumok kinyeréséhez használja az 1. észlelési modellt, és adja hozzá a `returnFaceAttributes` lekérdezési paramétert.

```java
builder.setParameter("detectionModel", "detection_01");
builder.setParameter("returnFaceAttributes", "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise");
```

A válasz mostantól a Face attribútumokat is tartalmazza. Például:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
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
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy egyszerű Java-konzol alkalmazást, amely REST-hívásokat használ az Azure Face API egy rendszerképben lévő arcok észleléséhez és attribútumaik visszaküldéséhez. Ezután tekintse át a Face API dokumentációját, ahol további információt talál a támogatott forgatókönyvekről.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
