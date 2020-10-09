---
title: 'Gyors útmutató: rendszerképek észlelése az Azure REST API és C #'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az Azure Face REST API C# használatával fogja felderíteni az arcokat a képen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 1b8bcaf283e612b3ebe6d6b7bb5660e8b3179ad3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858370"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Rövid útmutató: az arcok észlelése egy képen a Face REST API és C használatával #

Ebben a rövid útmutatóban az Azure Face REST API a C# használatával ismeri fel az emberi arcokat a képen.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" hozzon létre egy Face-erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése**elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Face APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
- A [Visual Studio](https://www.visualstudio.com/downloads/)bármely kiadása.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. A Visual Studióban hozzon létre egy új **Console app (.NET-keretrendszer)** projektet, és nevezze el **FaceDetection**.
1. Ha más projektek is vannak a megoldásban, válassza ki ezt a projektet az egyedüli kezdőprojektként.

## <a name="add-face-detection-code"></a>Arcfelismerés kód hozzáadása

Nyissa meg az új projekt *program.cs* -fájlját. Itt hozzá kell adnia a lemezképek betöltéséhez és az arcok észleléséhez szükséges kódot.

### <a name="include-namespaces"></a>Névterek belefoglalása

Adja hozzá az alábbi `using` utasításokat a *Program.cs* fájl elejéhez.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="dependencies":::

### <a name="add-essential-fields"></a>Alapvető mezők hozzáadása

Adja hozzá a **program** osztályt, amely tartalmazza a következő mezőket. Ezek az adatok határozzák meg, hogyan csatlakozhat a Face szolgáltatáshoz, és honnan szerezhető be a bemeneti adatok. Frissítenie kell a mezőt az `subscriptionKey` előfizetési kulcs értékével, és előfordulhat, hogy módosítania kell a `uriBase` karakterláncot úgy, hogy az tartalmazza az erőforrás-végponti karakterláncot.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="environment":::

### <a name="receive-image-input"></a>Képbevitel fogadása

Adja hozzá a következő kódot a **program** osztály **Main** metódusához. Ez a kód egy olyan kérést ír a konzolra, amely arra kéri a felhasználót, hogy adja meg a helyi képfájl elérési útját. Ezután meghívja a másik módszert, **MakeAnalysisRequest**, hogy feldolgozza a rendszerképet az adott helyen.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="main":::

### <a name="call-the-face-detection-rest-api"></a>Arcfelismerés REST API hívása

Adja hozzá a következő metódust a **program** osztályhoz. Egy REST-hívást hoz létre a Face API a távoli rendszerképben lévő arc-információk észleléséhez (ez a `requestParameters` karakterlánc határozza meg a lekérdezni kívánt Arcfelismerés-attribútumokat). Ezután a kimeneti adatokat egy JSON-karakterláncba írja.

A segítő metódusokat a következő lépésekben fogja meghatározni.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="request":::

### <a name="process-the-input-image-data"></a>A bemeneti képadatok feldolgozása

Adja hozzá a következő metódust a **program** osztályhoz. Ez a metódus a megadott elérési úton lévő rendszerképet egy byte tömbre konvertálja.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="getimage":::

### <a name="parse-the-json-response"></a>A JSON-válasz elemzése

Adja hozzá a következő metódust a **program** osztályhoz. Ezzel a módszerrel a JSON-bemenet könnyebben olvasható. Az alkalmazás ezt a sztringet fogja írni a konzolba. Ezután lezárhatja az osztályt és a névteret.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="print":::

## <a name="run-the-app"></a>Az alkalmazás futtatása

A sikeres válasz egy könnyen olvasható JSON formátumban jeleníti meg az Arcfelismerés. Például:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
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
 
A Face attribútumok kinyeréséhez használja az 1. észlelési modellt, és adja hozzá a `returnFaceAttributes` lekérdezési paramétert.

```csharp
string requestParameters = "detectionModel=detection_01&returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

A válasz mostantól a Face attribútumokat is tartalmazza. Például:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
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
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
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

Ebben a rövid útmutatóban létrehozott egy egyszerű .NET-konzol alkalmazást, amely REST-hívásokat használ az Azure Face szolgáltatással az arcok észleléséhez a rendszerképben, és visszaküldi az attribútumokat. Ezután tekintse át a Face API dokumentációját, ahol további információt talál a támogatott forgatókönyvekről.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
