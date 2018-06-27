---
title: Érzelemfelismerési API C# – első lépések |} Microsoft Docs
description: Információ és a segítségével gyorsan megismerkedhet a C# kognitív szolgáltatásban a Érzelemfelismerési API használatával kódminta kapják meg.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 11/02/2017
ms.author: anroth
ms.openlocfilehash: 89735ae54395447e3cb421f45db3d6b99001ecd6
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016565"
---
# <a name="emotion-api-c-quick-start"></a>Érzelemfelismerési API C# – első lépések

> [!IMPORTANT]
> A Video API előzetese 2017. október 30-án véget ért. Elemzések könnyen kibontani videók, kipróbálhatja az új [videó indexelő API előnézete](https://azure.microsoft.com/services/cognitive-services/video-indexer/). Is használhatja szóbeli szavakat, a lapok, a karakterek és a érzelmek észlelésével javítása érdekében a tartalom felderítési lép, például a keresési eredmények között. További tudnivalókért tekintse meg a [videó indexelő előnézete](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview) áttekintése.

Ez a cikk ismerteti, hogy információkat és a segítségével gyorsan kódminta használatával Ismerkedés a [Érzelemfelismerési API-t ismeri fel a metódust](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) a C#. Használhatja a kép egy vagy több személy által kifejezett érzelmek ismeri fel. 

## <a name="prerequisites"></a>Előfeltételek
* A kognitív szolgáltatások [Érzelemfelismerési API-ablakok SDK](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/).
* Az ingyenes beolvasása [előfizetés kulcs](https://azure.microsoft.com/try/cognitive-services/).

## <a name="emotion-recognition-c-example-request"></a>Érzelemfelismerési felismerés C# kérelem (példa)

Új konzol megoldás létrehozása a Visual Studióban, és akkor cserélje le a program.cs fájlt a következő kóddal. Módosítsa a `string uri` a régió, ahol szerezte be az előfizetés kulcsok használatára. Cserélje le a **Ocp-Apim-előfizetés-kulcs** értéke az érvényes előfizetés-kulccsal. Keresse meg az előfizetés kulcsot, ugorjon az Azure-portálon. A navigációs ablakban a bal oldali alatt a **kulcsok** területén keresse meg az Érzelemfelismerési API-erőforrást. Ehhez hasonlóan kaphat a megfelelő csatlakozás URI-azonosító található a **áttekintése** panel az erőforráshoz tartozó **végpont**.

![Az erőforrás API-kulcsokat](../../media/emotion-api/keys.png)

A kérelem a válasz feldolgozása, például egy szalagtár használja `Newtonsoft.Json`. Ezzel a módszerrel kezelhető objektumokban, úgynevezett jogkivonatok sorozataként képes kezelni egy JSON-karakterláncban. Az ebben a könyvtárban felvétele a csomagba, kattintson a jobb gombbal a projektben a Megoldáskezelőre, és válassza ki **Nuget-csomagok kezelése**. Majd keresse meg a **Newtonsoft**. Az első eredmény kell **Newtonsoft.Json**. Válassza az **Install** (Telepítés) lehetőséget. Ezt a szalagtárat hivatkozhat az alkalmazásban.

![Telepítse a newtonsoft.JSON elemet](../../media/emotion-api/newtonsoft-nuget.png)

```csharp
using System;
using System.IO;
using System.Net.Http.Headers;
using System.Net.Http;
using Newtonsoft.Json.Linq;

namespace CSHttpClientSample
{
    static class Program
    {
        static void Main()
        {
            Console.Write("Enter the path to a JPEG image file:");
            string imageFilePath = Console.ReadLine();

            MakeRequest(imageFilePath);

            Console.WriteLine("\n\n\nWait for the result below, then hit ENTER to exit...\n\n\n");
            Console.ReadLine(); // wait for ENTER to exit program
        }

        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        static async void MakeRequest(string imageFilePath)
        {
            var client = new HttpClient();

            // Request headers - replace this example key with your valid key.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<your-subscription-key>"); // 

            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URI below with "westcentralus".
            string uri = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?";
            HttpResponseMessage response;
            string responseContent;

            // Request body. Try this sample with a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (var content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                response = await client.PostAsync(uri, content);
                responseContent = response.Content.ReadAsStringAsync().Result;
            }

            // A peek at the raw JSON response.
            Console.WriteLine(responseContent);

            // Processing the JSON into manageable objects.
            JToken rootToken = JArray.Parse(responseContent).First;

            // First token is always the faceRectangle identified by the API.
            JToken faceRectangleToken = rootToken.First;

            // Second token is all emotion scores.
            JToken scoresToken = rootToken.Last;

            // Show all face rectangle dimensions
            JEnumerable<JToken> faceRectangleSizeList = faceRectangleToken.First.Children();
            foreach (var size in faceRectangleSizeList) {
                Console.WriteLine(size);
            }

            // Show all scores
            JEnumerable<JToken> scoreList = scoresToken.First.Children();
            foreach (var score in scoreList) {
                Console.WriteLine(score);
            }
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>Felismeri a érzelmek mintát választ
Sikeres meghívását arcfelismerési bejegyzések tömbje és a kapcsolódó érzelemfelismerési eredményeiket adja vissza. A sorrendje által tapasztalt téglalap mérete csökkenő sorrendben. Üres választ, hogy nincs lapok észlelt. Érzelemfelismerési bejegyzés a következő mezőket tartalmazza:

* faceRectangle: a kép arcfelismerési téglalap helye
* pontszámok: Érzelemfelismerési pontszámaihoz a kép minden lap 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
