---
title: 'Gyors útmutató: Érzelemfelismerés az arcok a képen – Emotion API,C#'
titlesuffix: Azure Cognitive Services
description: Információk és egy kódminta segítségével ismerkedhet meg az Emotion API C#-pal való használatának első lépéseivel.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 0874fc7b0c78f93df806b5bf782477efab0c0207
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234158"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Gyors útmutató: Érzelemfelismerés az arcok a képen az alkalmazás létrehozása.

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként általánosan elérhető.

Ebben a cikkben információk és egy kódminta segítségével rövid idő alatt megismerkedhet az [Emotion API Recognize metódusának](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) C#-pal való használatának alapjaival. Ezt a funkciót használhatja egy képen szereplő egy vagy több személy által kifejezett érzelmek felismerésére.

## <a name="prerequisites"></a>Előfeltételek
* Szerezze be a Cognitive Services [Emotion API Windows SDK-t](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/).
* Szerezze be ingyenes [előfizetői azonosítóját](https://azure.microsoft.com/try/cognitive-services/).

## <a name="emotion-recognition-c-example-request"></a>Érzelemfelismerési C#-kérésminta

Hozzon létre egy új konzolmegoldást a Visual Studióban, majd cserélje le a Program.cs fájlt az alábbi kódra. Módosítsa a `string uri` értékét úgy, hogy azt a régiót használja, ahonnan beszerezte az előfizetési azonosítókat. Cserélje le az **Ocp-Apim-Subscription-Key** értéket az érvényes előfizetői azonosítóra. Az előfizetői azonosítót megtalálhatja az Azure Portalon. A bal oldalon található navigációs ablakban, a **Kulcsok** szakaszban keresse meg az Emotion API-erőforrást. A megfelelő csatlakozási URI-t megtalálhatja a **Végpont** terület listájában szereplő erőforrás **Áttekintés** ablaktábláján.

![Az API-erőforráskulcsok](../../media/emotion-api/keys.png)

A kérésre adott válasz feldolgozásához használja a `Newtonsoft.Json` vagy egy hasonló kódtárat. Ezzel a módszerrel tokeneknek nevezett, kezelhető objektumokból álló sorozatként kezelheti a JSON-sztringeket. Ha ezt a kódtárat szeretné hozzáadni a csomaghoz, kattintson a jobb gombbal a projektjére a Solution Explorerben (Megoldáskezelő), és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget. Ezután keressen a **Newtonsoft** kifejezésre. Az első eredmény a **Newtonsoft.Json** lesz. Válassza az **Install** (Telepítés) lehetőséget. Most már hivatkozhat erre a kódtárra az alkalmazásban.

![A Newtonsoft.Json telepítése](../../media/emotion-api/newtonsoft-nuget.png)

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

## <a name="recognize-emotions-sample-response"></a>Érzelemfelismerési válaszminta
A sikeres hívás egy, az arcrekordokat és a hozzájuk tartozó érzelempontszámokat tartalmazó tömböt ad vissza. Az adatok az arcot jelölő téglalap mérete szerinti csökkenő sorrendben jelennek meg. Az üres válasz azt jelzi, hogy a rendszer nem észlelt arcot. Az érzelemrekord a következő mezőket foglalja magában:

* faceRectangle: Téglalap archoz helye
* pontszámok: Minden lap a képen érzelemfelismerési pontszámok

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
