---
title: 'Gyors útmutató: Azure REST API-val a kép arcok észlelése ésC#'
titleSuffix: Azure Cognitive Services
description: Az ebben a rövid útmutatóban az Azure Face REST API-t fogja használni C# arcokat észleli a képet.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 587005fa183c11a24ea2083ee28c8faca616b4a4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866644"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Gyors útmutató: Arcfelismerés a képet, a Face REST API-val és aC#

Az ebben a rövid útmutatóban az Azure Face REST API-t fogja használni C# emberi arcok észlelése a képet.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

- A Face API előfizetési kulcs. Megjelenik a származó ingyenes próba-előfizetését kulcsok [próbálja meg a Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Másik lehetőségként kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Face API szolgáltatás és a kulcs beszerzése.
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. A Visual Studióban hozzon létre egy új **Console app (.NET Framework)** projektre, és adja neki **FaceDetection**. 
1. Ha más projektek is vannak a megoldásban, válassza ki ezt a projektet az egyedüli kezdőprojektként.

## <a name="add-face-detection-code"></a>Arcok észlelése kód hozzáadása

Nyissa meg az új projekt *Program.cs* fájlt. Itt fogja hozzáadni a képeket tölthet be és arcfelismerés szükséges kódot.

### <a name="include-namespaces"></a>Névterek belefoglalása

Adja hozzá az alábbi `using` utasításokat a *Program.cs* fájl elejéhez.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
```

### <a name="add-essential-fields"></a>Alapvető mezők hozzáadása

Adja hozzá a **Program** osztályhoz a következő mezőket: Ezeket az adatokat adja meg, hogyan csatlakozhat a Face szolgáltatás és a bemeneti adatok beszerzése. Frissíteni kell a `subscriptionKey` mező értékét az előfizetési kulcs, és előfordulhat, hogy módosítania kell a `uriBase` úgy, hogy a megfelelő régióazonosító tartalmaz (lásd a [Face API-dokumentumok](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) minden régióban listája végpontok).


```csharp
// Replace <Subscription Key> with your valid subscription key.
const string subscriptionKey = "<Subscription Key>";

// NOTE: You must use the same region in your REST call as you used to
// obtain your subscription keys. For example, if you obtained your
// subscription keys from westus, replace "westcentralus" in the URL
// below with "westus".
//
// Free trial subscription keys are generated in the "westus" region.
// If you use a free trial subscription key, you shouldn't need to change
// this region.
const string uriBase =
    "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";
```

### <a name="receive-image-input"></a>A megadott fogadása

Adja hozzá a következő kódot a **fő** módszere a **Program** osztály. Kérdés ír a konzolt, amely rákérdez, a felhasználónak meg kell adnia egy kép URL-címe. Ezután meghívja a másik módszer **MakeAnalysisRequest**, az adott helyen található kép feldolgozásához.

```csharp
// Get the path and filename to process from the user.
Console.WriteLine("Detect faces:");
Console.Write(
    "Enter the path to an image with faces that you wish to analyze: ");
string imageFilePath = Console.ReadLine();

if (File.Exists(imageFilePath))
{
    try
    {
        MakeAnalysisRequest(imageFilePath);
        Console.WriteLine("\nWait a moment for the results to appear.\n");
    }
    catch (Exception e)
    {
        Console.WriteLine("\n" + e.Message + "\nPress Enter to exit...\n");
    }
}
else
{
    Console.WriteLine("\nInvalid file path.\nPress Enter to exit...\n");
}
Console.ReadLine();
```

### <a name="call-the-face-detection-rest-api"></a>Az arcfelismerés is használható REST API meghívása

Adja hozzá a **Program** osztályhoz a következő metódust. Akkor hoz létre REST-hívást a Face API, arcfelismerési kapcsolatos információk észleléséhez a távoli kép (a `requestParameters` karakterláncot adja meg, melyik face attribútumok beolvasni). Ezután egy JSON-karakterláncot ír a kimeneti adatokat.

A következő lépésekben a segédmetódusokat fogja meghatározni.

```csharp
// Gets the analysis of the specified image by using the Face REST API.
static async void MakeAnalysisRequest(string imageFilePath)
{
    HttpClient client = new HttpClient();

    // Request headers.
    client.DefaultRequestHeaders.Add(
        "Ocp-Apim-Subscription-Key", subscriptionKey);

    // Request parameters. A third optional parameter is "details".
    string requestParameters = "returnFaceId=true&returnFaceLandmarks=false" +
        "&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses," +
        "emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

    // Assemble the URI for the REST API Call.
    string uri = uriBase + "?" + requestParameters;

    HttpResponseMessage response;

    // Request body. Posts a locally stored JPEG image.
    byte[] byteData = GetImageAsByteArray(imageFilePath);

    using (ByteArrayContent content = new ByteArrayContent(byteData))
    {
        // This example uses content type "application/octet-stream".
        // The other content types you can use are "application/json"
        // and "multipart/form-data".
        content.Headers.ContentType =
            new MediaTypeHeaderValue("application/octet-stream");

        // Execute the REST API call.
        response = await client.PostAsync(uri, content);

        // Get the JSON response.
        string contentString = await response.Content.ReadAsStringAsync();

        // Display the JSON response.
        Console.WriteLine("\nResponse:\n");
        Console.WriteLine(JsonPrettyPrint(contentString));
        Console.WriteLine("\nPress Enter to exit...");
    }
}
```

### <a name="process-the-input-image-data"></a>A bemeneti kép adatok feldolgozása

Adja hozzá a **Program** osztályhoz a következő metódust. A lemezkép a megadott URL-címen alakít egy bajtového Pole.

```csharp
// Returns the contents of the specified file as a byte array.
static byte[] GetImageAsByteArray(string imageFilePath)
{
    using (FileStream fileStream =
        new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
    {
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }
}
```

### <a name="parse-the-json-response"></a>A JSON-válasz elemzése

Adja hozzá a **Program** osztályhoz a következő metódust. Ez formázza a JSON-bemenetben könnyebben olvasható is. Az alkalmazás a karakterláncadatokat írni a konzolon.

```csharp
// Formats the given JSON string by adding line breaks and indents.
static string JsonPrettyPrint(string json)
{
    if (string.IsNullOrEmpty(json))
        return string.Empty;

    json = json.Replace(Environment.NewLine, "").Replace("\t", "");

    StringBuilder sb = new StringBuilder();
    bool quote = false;
    bool ignore = false;
    int offset = 0;
    int indentLength = 3;

    foreach (char ch in json)
    {
        switch (ch)
        {
            case '"':
                if (!ignore) quote = !quote;
                break;
            case '\'':
                if (quote) ignore = !ignore;
                break;
        }

        if (quote)
            sb.Append(ch);
        else
        {
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', ++offset * indentLength));
                    break;
                case '}':
                case ']':
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', --offset * indentLength));
                    sb.Append(ch);
                    break;
                case ',':
                    sb.Append(ch);
                    sb.Append(Environment.NewLine);
                    sb.Append(new string(' ', offset * indentLength));
                    break;
                case ':':
                    sb.Append(ch);
                    sb.Append(' ');
                    break;
                default:
                    if (ch != ' ') sb.Append(ch);
                    break;
            }
        }
    }

    return sb.ToString().Trim();
}
```

## <a name="run-the-app"></a>Az alkalmazás futtatása

A sikeres válasz Arcfelismerési adatokat könnyen olvasható JSON formátumban jeleníti meg. Példa:

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

Ebben a rövid útmutatóban létrehozott egy egyszerű .NET Konzolalkalmazás, amely az Azure Face API REST-hívások használ a kép arcok észlelése és attribútumaik. Ezután megkezdheti a Face API referenciadokumentációt tartalmaz további információt a támogatott forgatókönyveket.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
