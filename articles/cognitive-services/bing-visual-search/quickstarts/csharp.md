---
title: 'Gyors útmutató: képelemzések beolvasása a REST API C# és a-Bing Visual Search használatával'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan tölthet fel képet a Bing Visual Search API, és hogyan szerezhet be információkat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 04/26/2019
ms.author: scottwhi
ms.openlocfilehash: 82c1159aca51bc30839f5380a414bd2b3b488bb8
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383639"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Gyors útmutató: képelemzések beolvasása a Bing Visual Search REST API használatávalC#

Ez a rövid útmutató bemutatja, hogyan tölthet fel egy rendszerképet a Bing Visual Search APIba, és megtekintheti a visszaadott elemzéseket.

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2019](https://www.visualstudio.com/downloads/)bármely kiadása.
* A [JSON.NET keretrendszer](https://www.newtonsoft.com/json), amely NuGet-csomagként érhető el.
* Ha Linux/MacOS rendszert használ, akkor az alkalmazást a [mono](https://www.mono-project.com/)használatával futtathatja.

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. A Visual Studióban hozzon létre egy BingSearchApisQuickStart nevű új konzolos megoldást. Adja hozzá a következő névtereket a fő kódhoz:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Adja meg a feltölteni kívánt rendszerképhez tartozó előfizetési kulcs, végpont és elérési út változóit:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Hozzon létre egy `GetImageFileName()` nevű metódust a rendszerkép elérési útjának beolvasásához:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Hozzon létre egy metódust a rendszerkép bináris adatok beolvasásához:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Űrlapadatok készítése

Helyi rendszerkép feltöltéséhez először létre kell készítenie az adatűrlap-adatok küldését az API-nak. Az űrlap-adatoknak tartalmaznia kell a `Content-Disposition` fejlécet, a `name` paramétert "rendszerkép" értékre kell állítani, és a `filename` paraméter tetszőleges karakterláncra állítható be. Az űrlap tartalma tartalmazza a rendszerkép bináris értékeit. A feltölthető maximális képméret 1 MB lehet.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Adjon hozzá határi karakterláncokat a POST Form adatának formázásához. A határ karakterláncok határozzák meg az adat indítási, befejezési és sortörési karaktereit:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. A következő változók használatával adhat hozzá paramétereket az űrlap adatait:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Hozzon létre egy `BuildFormDataStart()` nevű függvényt, amely a határ karakterláncok és a képelérési út alapján hozza létre az űrlap-adatok kezdetét:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Hozzon létre egy `BuildFormDataEnd()` nevű függvényt az űrlapadatok végének létrehozásához a határ karakterláncok használatával:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>A Bing Visual Search API meghívása

1. Hozzon létre egy függvényt az Bing Visual Search végpont meghívásához és a JSON-válasz visszaküldéséhez. A függvény az űrlapadatok, a képadatokat tartalmazó bájtok és egy `contentType` érték kezdetét és végét veszi át.

2. Az URI-t, a contentType-értéket és a fejléceket a `WebRequest` használatával tárolhatja.  

3. Az űrlap-és képadatok megírásához, majd a válasz beszerzéséhez használja a `request.GetRequestStream()`. A függvénynek az alábbihoz hasonlónak kell lennie:
        
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

## <a name="create-the-main-method"></a>A Main metódus létrehozása

1. Az alkalmazás `Main` metódusában szerezze be a rendszerkép fájlnevét és bináris adatait:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Állítsa be a POST törzset úgy, hogy a határt formázza. Ezután hívja meg `startFormData()` és `endFormData` az űrlapadatok létrehozásához:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Hozza létre a `ContentType` értéket a `CONTENT_TYPE_HEADER_PARAMS` és az űrlap adathatárának formázásával:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Az API-válasz beszerzéséhez hívja meg `BingImageSearch()` és nyomtassa ki a választ:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>HttpClient használata

Ha `HttpClient`használ, a `MultipartFormDataContent` osztály használatával hozhatja létre az űrlapadatokat. A következő kódrészletek használatával cserélje le az előző példában szereplő megfelelő metódusokat.

Cserélje le a `Main` metódust a következő kódra:

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

Cserélje le a `BingImageSearch` metódust a következő kódra:

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
> [Visual Search egyoldalas Webalkalmazás létrehozása](../tutorial-bing-visual-search-single-page-app.md)
