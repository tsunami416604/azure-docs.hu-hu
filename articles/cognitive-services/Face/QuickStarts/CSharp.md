---
title: 'Rövid útmutató: Arcok észlelése egy lemezképben az Azure REST API-val és c #'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az Azure Face REST API C# használatával észleli a rendszerképek en lévő arcokat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 69d3f1a7f0c455275a212401110459abb1b8d8d0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403413"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Rövid útmutató: Arcok észlelése egy lemezképen a Face REST API és a C használatával #

Ebben a rövid útmutatóban az Azure Face REST API C# használatával észlelheti az emberi arcokat egy lemezképben.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

- Face-előfizetési kulcs. Ingyenes próba-előfizetési kulcsot a [Cognitive Services kipróbálásával](https://azure.microsoft.com/try/cognitive-services/?api=face-api)szerezhetbe. Vagy kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) című részben található utasításokat, hogy előiratkozzon a Face szolgáltatásra, és levegye a kulcsot.
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/)bármely kiadása.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. A Visual Studio-ban hozzon létre egy új **Konzolalkalmazás-projektet (.NET Framework),** és nevezze el **FaceDetection**néven.
1. Ha más projektek is vannak a megoldásban, válassza ki ezt a projektet az egyedüli kezdőprojektként.

## <a name="add-face-detection-code"></a>Arcfelismerő kód hozzáadása

Nyissa meg az új projekt *Program.cs* fájlját. Itt hozzáadja a képek betöltéséhez és az arcok észleléséhez szükséges kódot.

### <a name="include-namespaces"></a>Névterek belefoglalása

Adja hozzá az alábbi `using` utasításokat a *Program.cs* fájl elejéhez.

```csharp
using System;
using System.IO;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
```

### <a name="add-essential-fields"></a>Alapvető mezők hozzáadása

Adja hozzá a következő mezőket tartalmazó **Program** osztályt. Ezek az adatok határozzák meg, hogyan lehet csatlakozni a Face szolgáltatáshoz, és hol lehet beszerezni a bemeneti adatokat. Frissítenie kell a `subscriptionKey` mezőt az előfizetési kulcs értékével, és `uriBase` előfordulhat, hogy módosítania kell a karakterláncot, hogy az tartalmazza az erőforrásvégpont-karakterláncot.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
namespace DetectFace
{
    class Program
    {

        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // replace <myresourcename> with the string found in your endpoint URL
        const string uriBase =
            "https://<myresourcename>.cognitive.microsoft.com/face/v1.0/detect";
```

### <a name="receive-image-input"></a>Képbevitel fogadása

Adja hozzá a következő kódot a **Program** osztály **Fő** metódusához. Ez a kód egy kérést ír a konzolnak, amelyben megkéri a felhasználót, hogy adjon meg egy kép URL-címét. Ezután meghívja egy másik módszer, **MakeAnalysisRequest**, feldolgozni a képet, hogy a helyszínen.

```csharp
        static void Main(string[] args)
        {
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
        }
```

### <a name="call-the-face-detection-rest-api"></a>Hívja meg az arcfelismerő REST API-t

Adja hozzá a következő módszert a **Program** osztályhoz. Ez létrehoz egy REST-hívást a Face API-t a `requestParameters` face információk észlelésére a távoli lemezképben (a karakterlánc határozza meg, hogy melyik arcattribútumokat kell beolvasni). Ezután a kimeneti adatokat egy JSON-karakterláncba írja.

A segítő módszereket a következő lépésekben határozhatja meg.

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

### <a name="process-the-input-image-data"></a>A bemeneti képadatainak feldolgozása

Adja hozzá a következő módszert a **Program** osztályhoz. Ez a módszer a megadott URL-címen lévő lemezképet bájttömbté alakítja.

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

### <a name="parse-the-json-response"></a>Elemezd a JSON-választ

Adja hozzá a következő módszert a **Program** osztályhoz. Ez a módszer úgy formázza a JSON-bemenetet, hogy könnyebben olvasható legyen. Az alkalmazás ezeket a karakterlánc-adatokat a konzolra fogja írni. Ezután bezárhatja az osztályt és a névteret.

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
    }
}
```

## <a name="run-the-app"></a>Az alkalmazás futtatása

A sikeres válasz a Face adatokat könnyen olvasható JSON formátumban jeleníti meg. Például:

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

Ebben a rövid útmutatóban létrehozott egy egyszerű .NET konzolalkalmazást, amely REST-hívásokat használ az Azure Face szolgáltatással a lemezkép arcok észlelésére és attribútumaik visszaadására. Ezután fedezze fel a Face API referenciadokumentációját, hogy többet tudjon meg a támogatott forgatókönyvekről.

> [!div class="nextstepaction"]
> [Arcfelismerési API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
