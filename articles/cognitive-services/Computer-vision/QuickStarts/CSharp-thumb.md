---
title: 'Rövid útmutató: Miniatűr létrehozása – REST, C# – Computer Vision'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és C# használatával.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: b3822fee5d729064375e5088cb207aebfa74f8ac
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342047"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-c35-in-computer-vision"></a>Rövid útmutató: Miniatűr létrehozása a REST API és C# használatával a Computer Visionben

Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision REST API-jának segítségével. A [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metódussal létrehozhatja egy kép miniatűrjét. Megadhatja a magasságát és a szélességét, amely eltérhet a bemeneti kép oldalarányától. A Computer Vision intelligens körbevágással azonosítja a releváns régiót, amely alapján létrehozza a vágási koordinátákat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) vagy újabb verzióval kell rendelkeznie.
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. Az előfizetői azonosító beszerzéséhez lásd az [előfizetői azonosítók beszerzéséről](../Vision-API-How-to-Topics/HowToSubscribe.md) szóló témakört.

## <a name="create-and-run-the-sample-application"></a>A mintaalkalmazás létrehozása és futtatása

A minta a Visual Studióban való létrehozásához végezze el az alábbi lépéseket:

1. A Visual C#-konzolalkalmazás sablonjával hozzon létre egy új Visual Studio-megoldást.
1. Telepítse a Newtonsoft.Json NuGet-csomagot.
    1. A menüben kattintson a **Tools** (Eszközök) elemre, és válassza a **NuGet Package Manager** (NuGet-csomagkezelő), majd a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) lehetőséget.
    1. Kattintson a **Browse** (Tallózás) lapra, majd írja be a **keresőmezőbe** a „Newtonsoft.Json” kifejezést.
    1. Válassza a megjelenő **Newtonsoft.Json** lehetőséget, majd jelölje be a projektnév melletti jelölőnégyzetet, és kattintson az **Install** (Telepítés) gombra.
1. Cserélje le a `Program.cs` kódját az alábbi kódra, majd hajtsa végre a következő változtatásokat, ahol szükséges:
    1. Cserélje le a `subscriptionKey` értéket az előfizetői azonosítóra.
    1. Ha szükséges, cserélje le az `uriBase` értéket azon Azure-régió [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) metódusának végponti URL-címére, ahol az előfizetői azonosítókat beszerezte.
1. Futtassa a programot.
1. Írja be parancssorba egy helyi kép elérési útját.

```csharp
using Newtonsoft.Json.Linq;
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;

namespace CSHttpClientSample
{
    static class Program
    {
        // Replace <Subscription Key> with your valid subscription key.
        const string subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the West Central US region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        const string uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Thumbnail:");
            Console.Write(
                "Enter the path to the image you wish to use to create a thumbnail image: ");
            string imageFilePath = Console.ReadLine();

            if (File.Exists(imageFilePath))
            {
                // Call the REST API method.
                Console.WriteLine("\nWait a moment for the results to appear.\n");
                MakeThumbNailRequest(imageFilePath).Wait();
            }
            else
            {
                Console.WriteLine("\nInvalid file path");
            }
            Console.WriteLine("\nPress Enter to exit...");
            Console.ReadLine();
        }

        /// <summary>
        /// Gets a thumbnail image from the specified image file by using
        /// the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file to use to create the thumbnail image.</param>
        static async Task MakeThumbNailRequest(string imageFilePath)
        {
            try
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add(
                    "Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters.
                // The width and height parameters specify a thumbnail that's 
                // 200 pixels wide and 150 pixels high.
                // The smartCropping parameter is set to true, to enable smart cropping.
                string requestParameters = "width=200&height=150&smartCropping=true";

                // Assemble the URI for the REST API method.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Read the contents of the specified local image
                // into a byte array.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                // Add the byte array as an octet stream to the request body.
                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses the "application/octet-stream" content type.
                    // The other content types you can use are "application/json"
                    // and "multipart/form-data".
                    content.Headers.ContentType =
                        new MediaTypeHeaderValue("application/octet-stream");

                    // Asynchronously call the REST API method.
                    response = await client.PostAsync(uri, content);
                }

                // Check the HTTP status code of the response. If successful, display
                // display the response and save the thumbnail.
                if (response.IsSuccessStatusCode)
                {
                    // Display the response data.
                    Console.WriteLine("\nResponse:\n{0}", response);

                    // Get the image data for the thumbnail from the response.
                    byte[] thumbnailImageData =
                        await response.Content.ReadAsByteArrayAsync();

                    // Save the thumbnail to the same folder as the original image,
                    // using the original name with the suffix "_thumb".
                    // Note: This will overwrite an existing file of the same name.
                    string thumbnailFilePath =
                        imageFilePath.Insert(imageFilePath.Length - 4, "_thumb");
                    File.WriteAllBytes(thumbnailFilePath, thumbnailImageData);
                    Console.WriteLine("\nThumbnail written to: {0}", thumbnailFilePath);
                }
                else
                {
                    // Display the JSON error data.
                    string errorString = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("\n\nResponse:\n{0}\n",
                        JToken.Parse(errorString).ToString());
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("\n" + e.Message);
            }
        }

        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            // Open a read-only file stream for the specified file.
            using (FileStream fileStream =
                new FileStream(imageFilePath, FileMode.Open, FileAccess.Read))
            {
                // Read the file's contents into a byte array.
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
}
```

## <a name="examine-the-response"></a>A válasz vizsgálata

A sikeres válasz bináris adatokként jelenik meg, amelyek a miniatűr képadatait képviselik. Sikeres kérés esetén a rendszer a helyi kép mappájába menti a miniatűrt, az eredeti fájlnevet a „_thumb” végződéssel kiegészítve. Ha a kérés meghiúsul, a válasz tartalmaz egy hibakódot és egy üzenetet, amely segít meghatározni a hiba okát.

A mintaalkalmazás megjeleníti a sikeres választ a konzolablakban, a következő példához hasonló módon:

```text
Response:

StatusCode: 200, ReasonPhrase: 'OK', Version: 1.1, Content: System.Net.Http.StreamContent, Headers:
{
  Pragma: no-cache
  apim-request-id: 131eb5b4-5807-466d-9656-4c1ef0a64c9b
  Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  x-content-type-options: nosniff
  Cache-Control: no-cache
  Date: Tue, 06 Jun 2017 20:54:07 GMT
  X-AspNet-Version: 4.0.30319
  X-Powered-By: ASP.NET
  Content-Length: 5800
  Content-Type: image/jpeg
  Expires: -1
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a Visual Studio-megoldást. Ehhez nyissa meg a Fájlkezelőt, lépjen a Visual Studio-megoldást tartalmazó mappára, majd törölje azt.

## <a name="next-steps"></a>További lépések

Ismerjen meg egy alapszintű Windows-alkalmazást, amely a Computer Vision segítségével végez optikai karakterfelismerést (OCR), és amellyel intelligens körbevágású miniatűröket hozhat létre, valamint képek vizuális jellemzőit, például arcokat észlelhet, kategorizálhat, címkézhet és írhat le. A Computer Vision API-kkal való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API CC&#35;-oktatóanyag](../Tutorials/CSharpTutorial.md)