---
title: 'Gyors útmutató: A nemkívánatos tartalmat a képeket elemezhet C# -Content Moderator'
titlesuffix: Azure Cognitive Services
description: Kép tartalmának elemzése különböző kifogásolható tartalmak felismeréséhez a Content Moderator SDK for .NET használatával
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: e11e037e7be8ca308f7924654c4f28b1634dc5fd
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260771"
---
# <a name="quickstart-analyze-image-content-for-objectionable-material-in-c"></a>Gyors útmutató: A nem kívánt tartalom kép tartalmakat elemezhetC#

Ez a cikk ahhoz biztosít információt és kódmintákat, hogy megismerkedhessen a [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) használatával. Megtanulja, hogyan szűrheti ki a felnőtteknek szóló és kényes tartalmakat, hogyan kereshet kinyerhető szöveget, és hogyan ismerheti fel az emberi arcokat az esetlegesen kifogásolható tartalom moderálása céljából.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- A Content Moderator előfizetői azonosítója. A Content Moderatorra történő előfizetéshez és az előfizetői azonosító beszerzéséhez kövesse a [Cognitive Services-fiók létrehozásával](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kapcsolatos szakaszban található utasításokat.
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.


> [!NOTE]
> Ez az útmutató ingyenes szintű Content Moderator-előfizetést használ. Az egyes előfizetési szintek tartalmáról a [díjszabást és a korlátozásokat](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/) ismertető oldalon talál információkat.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. Visual Studióban hozzon létre egy új **Console App (.NET Framework)** (Konzolalkalmazás- (.NET-keretrendszer)) projektet, és adja neki az **ImageModeration** nevet. 
1. Ha más projektek is vannak a megoldásban, válassza ki ezt a projektet az egyedüli kezdőprojektként.
1. Szerezze be a szükséges NuGet-csomagokat. A Solution Explorer (Megoldáskezelő) felületén kattintson a jobb gombbal a projektre és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget, majd keresse meg és telepítse a következő csomagokat:
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-image-moderation-code"></a>Képmoderálási kód hozzáadása

Következő lépésként másolja és illessze be az ebben az útmutatóban található kódot a projektjébe egy alapszintű tartalommoderálási forgatókönyv megvalósításához.

### <a name="include-namespaces"></a>Névterek belefoglalása

Adja hozzá az alábbi `using` utasításokat a *Program.cs* fájl elejéhez.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Content Moderator-ügyfél létrehozása

Adja hozzá a következő kódot a *Program.cs* fájlhoz, hogy létrehozzon egy Content Moderator-ügyfélszolgáltatót az előfizetéséhez. Adja hozzá a kódot a **Program** osztály mellett, ugyanabban a névtérben. Frissítenie kell a régióazonosító és az előfizetői azonosító értékét az **AzureRegion** és a **CMSubscriptionKey** mezőkben.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=84-107)]


### <a name="set-up-input-and-output-targets"></a>Bemeneti és kimeneti célok beállítása

Adja hozzá a következő statikus mezőket a **Program** osztályhoz a _Program.cs_-ben. Ezek adják meg a bemeneti kép tartalmának és a kimeneti JSON tartalmának fájljait.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=49-53)]

Létre kell hoznia az *_ImageFiles.txt* bemeneti fájlt, és ennek megfelelően frissítenie kell az elérési útját (a relatív elérési utak a végrehajtási könyvtárhoz képest relatívak). Nyissa meg az _ImageFiles.txt_ fájlt, és adja hozzá a moderálni kívánt képek URL-címét. Ez a rövid útmutató a következő URL-címeket használja bemeneti mintaként.

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="create-a-class-to-handle-results"></a>Osztály létrehozása az eredmények kezeléséhez

Adja hozzá a következő kódot a *Program.cs* fájlhoz a **Program** osztály mellett, ugyanabban a névtérben. Ennek az osztálynak egy-egy példányát fogja használni az egyes ellenőrzött képek moderálási eredményeinek rögzítéséhez.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=109-124)]


### <a name="define-the-image-evaluation-method"></a>A képértékelő módszer meghatározása

Adja hozzá a **Program** osztályhoz a következő metódust. Ez a módszer három különböző módon értékel egyetlen képet, majd visszaadja az értékelés eredményét. Ha többet szeretne megtudni arról, hogy mit hajtanak végre az egyes műveletek, kattintson a [További lépések](#next-steps) szakaszban található hivatkozásra.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=55-81)]

### <a name="load-the-input-images"></a>A bemeneti képek betöltése

Adja hozzá a következő kódot a **Main** metódushoz a **Program** osztályban. Ez beállítja a programot, hogy lekérje a bemeneti fájlban található egyes képek URL-címeinek kiértékelési adatait. Ezután a program ezeket az adatokat egy kimeneti fájlba írja.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=17-46)]

## <a name="run-the-program"></a>A program futtatása

A program a JSON-sztringadatokat a _ModerationOutput.json_ fájlba írja. Az ebben a rövid útmutatóban használt mintaképek a következő kimenetet adják. Vegye figyelembe, hogy minden egyes kép külön `ImageModeration`, `FaceDetection` és `TextDetection` szakasszal rendelkezik, amelyek az **EvaluateImage** metódus három API-hívásának felelnek meg.

```json
[{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
  "ImageModeration": {
    "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "116"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "12"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
    "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
    "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
    "result": false,
    "count": 0,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "11"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "faces": []
  }
},
{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
  "ImageModeration": {
    "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
    "adultClassificationScore": 0.0035635426174849272,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.021369094029068947,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "109"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "46"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
    "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
    "language": "eng",
    "text": "",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
    "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
    "result": true,
    "count": 6,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "60"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "faces": [
      {
        "bottom": 598,
        "left": 44,
        "right": 268,
        "top": 374
      },
      {
        "bottom": 620,
        "left": 308,
        "right": 532,
        "top": 396
      },
      {
        "bottom": 575,
        "left": 594,
        "right": 773,
        "top": 396
      },
      {
        "bottom": 563,
        "left": 812,
        "right": 955,
        "top": 420
      },
      {
        "bottom": 611,
        "left": 972,
        "right": 1151,
        "top": 432
      },
      {
        "bottom": 510,
        "left": 1232,
        "right": 1456,
        "top": 286
      }
    ]
  }
}]
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy egyszerű .NET-alkalmazást, amely a Content Moderator szolgáltatás segítségével releváns információkat ad vissza egy adott képmintáról. Következő lépésként többet tudhat meg arról, mit jelentenek a különböző jelzők és besorolások, így eldöntheti, milyen adatokra van szüksége, és hogyan kezelje ezeket az alkalmazása.

> [!div class="nextstepaction"]
> [Képmoderálási útmutató](image-moderation-api.md)
