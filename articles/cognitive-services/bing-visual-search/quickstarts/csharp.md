---
title: 'Gyors útmutató: A Bing Visual Search REST API használatával kép elemzések lekérése ésC#'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan kaphat elemzési információkat, és töltsön fel egy képet, a Bing Visual Search API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 3/28/2019
ms.author: scottwhi
ms.openlocfilehash: d2f5e87bd6c6780e8504abe1753e90eca5db763a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880406"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Gyors útmutató: A Bing Visual Search REST API használatával kép elemzések lekérése ésC#

Ez a rövid útmutató bemutatja, hogyan tölthet fel képeket, a Bing Visual Search API, valamint a elemzési eredményeket adja vissza.

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* A [Json.NET keretrendszer](https://www.newtonsoft.com/json), NuGet-csomagként érhető el.
* Ha Linux/MacOS rendszeren használja, akkor futtathatja az alkalmazást a [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. A Visual Studióban hozzon létre egy új konzol megoldás BingSearchApisQuickStart nevű. A fő kódfájl adja hozzá a következő névterek:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Adja hozzá a feltölteni kívánt kép változói az előfizetési kulcs, végpont és elérési útja:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Hozzon létre egy metódust `GetImageFileName()` beolvasni a lemezkép elérési útja:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. A rendszerkép a bináris adatok metódus létrehozása:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Az űrlap-adatok létrehozása

A helyi rendszerképet tölt fel, a képernyőn az API-hoz elküldendő adatok először létre. Az űrlapadatok tartalmaznia kell a `Content-Disposition` fejléc, annak `name` paraméter "image" értékre kell állítani, és a `filename` bármilyen karakterlánc paraméterrel állítható. A képernyő tartalmát a rendszerkép a bináris adatokat tartalmaz. A maximális képméret feltöltheti az 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Adja hozzá a határ karakterláncok POST űrlap adatok formázásához. Határ karakterláncok határozza meg a kezdő, teljes és sortörés karaktereket az adatok:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Paraméterek hozzáadása az űrlapadatok használja az alábbi változókat:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Hozzon létre egy függvényt, nevű `BuildFormDataStart()` hozhat létre az űrlapadatokat, a határ karakterláncok és a lemezkép elérési útja kezdete:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Hozzon létre egy függvényt, nevű `BuildFormDataEnd()` létrehozása az űrlap adatokat, a határ karakterláncok használatával végén:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>A Bing Visual Search API meghívása

1. Hozzon létre egy függvényt, amely a Bing Visual Search-végpont meghívása, és a JSON-választ adja vissza. A függvény vesz igénybe, a kezdő és befejező adatai, egy bájttömböt a lemezkép-adatokat tartalmazó és a egy `contentType` értéket.

2. Használja a `WebRequest` URI, contentType értéket, és a fejlécek tárolásához.  

3. Használat `request.GetRequestStream()` a űrlap és a rendszerkép az adatokat, majd a választ. A függvény az alábbihoz hasonló lesz:
        
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

## <a name="create-the-main-method"></a>Hozzon létre a Main metódushoz

1. Az a `Main` metódus az alkalmazás, a filename és a bináris adatokat, a lemezkép beolvasása:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Állítsa be a bejegyzés törzse, a határ formázását. Ezután hívja meg `startFormData()` és `endFormData` , az űrlap adatok létrehozásához:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Hozzon létre a `ContentType` formázás érték `CONTENT_TYPE_HEADER_PARAMS` és adatok képernyőn határa:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Az API-válasz lekérése meghívásával `BingImageSearch()` és a válasz:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>HttpClient használata

Ha `HttpClient`, használhatja a `MultipartFormDataContent` hozhat létre az űrlapadatok osztály. Csak a következő részekben kód cserélje le az előző példában a megfelelő módszerek.

Cserélje le a `Main` metódus ezzel a kóddal:

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

Cserélje le a `BingImageSearch` metódus ezzel a kóddal:

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
> [Hozzon létre egy vizuális keresés egyoldalas webalkalmazást](../tutorial-bing-visual-search-single-page-app.md)
