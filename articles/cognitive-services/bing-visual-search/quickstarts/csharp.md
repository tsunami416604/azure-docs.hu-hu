---
title: 'Rövid útmutató: Képelemzések beszereznie a REST API és a C# használatával – Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként tölthet fel képet a Bing Visual Search API-ba, és hogyan kaphat betekintést.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 07ecac46ab13058d308c17c5747701ee5ed577fc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446674"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Rövid útmutató: Képelemzésbe való betekintés a Bing Visual Search REST API és a C használatával #

Ez a rövid útmutató bemutatja, hogyan tölthet fel egy képet a Bing Visual Search API-ba, és hogyan tekintheti meg a visszaadott elemzéseket.

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2019](https://www.visualstudio.com/downloads/)bármely kiadása.
* A [Json.NET keretrendszer](https://www.newtonsoft.com/json), amely NuGet csomagként érhető el.
* Ha Linux/MacOS-t használsz, futtathatod ezt az alkalmazást a [Mono](https://www.mono-project.com/)használatával.

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. A Visual Studióban hozzon létre egy új konzolmegoldást, a BingSearchApisQuickStart nevet. Adja hozzá a következő névtereket a fő kódfájlhoz:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Adja hozzá az előfizetési kulcs, a végpont és a feltölteni kívánt kép elérési útjának változóit. `uriBase`lehet az alábbi globális végpont, vagy az erőforráshoz az Azure Portalon megjelenő [egyéni altartomány-végpont:](../../../cognitive-services/cognitive-services-custom-subdomains.md)

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Hozzon létre `GetImageFileName()` egy nevű módszert a kép elérési útjának lekéri:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Hozzon létre egy módszert a kép bináris adatainak beolvasására:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Az űrlapadatok létrehozása

Helyi lemezkép feltöltéséhez először létrehozza az API-nak küldendő űrlapadatokat. Az űrlapadatoknak `Content-Disposition` tartalmazniuk `name` kell a fejlécet, a paramétert "kép" -re kell állítani, és a `filename` paraméter tanuscal állítható be. Az űrlap tartalma tartalmazza a kép bináris adatait. A feltölthető maximális képméret 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Határkarakterláncok hozzáadása a POST űrlapadatok formázásához. A határkarakterláncok határozzák meg az adatok kezdő, záró és új sorkaraktereit:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. A következő változók segítségével adhat hozzá paramétereket az űrlapadatokhoz:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Hozzon létre `BuildFormDataStart()` egy nevű függvényt az űrlapadatok indításához a határkarakterláncok és a kép elérési útja segítségével:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Hozzon létre `BuildFormDataEnd()` egy nevű függvényt az űrlapadatok végének létrehozásához a határkarakterláncok használatával:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>A Bing Visual Search API felhívása

1. Hozzon létre egy függvényt a Bing Visual Search végpont jának meghívásához, és adja vissza a JSON-választ. A függvény az űrlapadatok kezdetét és végét, a képadatokat tartalmazó `contentType` bájttömböt és egy értéket vesz fel.

2. `WebRequest` Az a segítségével tárolja az URI-t, contentType-értékét és fejléceit.  

3. Az `request.GetRequestStream()` űrlap- és képadatok írása, majd a válasz beírása. A funkciónak hasonlónak kell lennie az alábbihoz:
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>A fő metódus létrehozása

1. Az `Main` alkalmazás módszerében kapja meg a kép fájlnevét és bináris adatait:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Állítsa be a POST törzset a határvonal formázásával. Ezután `startFormData()` `endFormData` hívja meg és hozza létre az űrlapadatokat:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Az `ContentType` érték létrehozása `CONTENT_TYPE_HEADER_PARAMS` formázással és az űrlap adathatárával:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Az API-válasz `BingImageSearch()` beírása és nyomtatása:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>HttpClient használata

Ha a `HttpClient`használatát használja, `MultipartFormDataContent` az osztály segítségével készítheti el az űrlapadatokat. Csak használja a következő kódszakaszokat az előző példában a megfelelő módszerek cseréjéhez.

Cserélje `Main` le a módszert erre a kódra:

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

Cserélje `BingImageSearch` le a módszert erre a kódra:

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Vizuális keresés létrehozása egyoldalas webalkalmazáslétrehozása](../tutorial-bing-visual-search-single-page-app.md)
