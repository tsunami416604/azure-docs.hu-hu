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
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 7961fb05f7ca9c6e6b61330e7dff53f2d5a41001
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535314"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Gyors útmutató: A Bing Visual Search REST API használatával kép elemzések lekérése ésC#

Ez a rövid útmutató segítségével a Bing Visual Search API az első hívását, és megtekintheti a keresési eredmények között. Ez egyszerű C# alkalmazás feltölt egy képet az API-hoz, és a visszaadott kapcsolatos információkat jeleníti meg.

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* A [Json.NET](https://www.newtonsoft.com/json) keretrendszer, amely NuGet-csomagként letölthető.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új, `BingSearchApisQuickStart` nevű konzolmegoldást a Visual Studióban. Ezután adja hozzá a következő névtereket a fő kódfájlhoz.

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Adja hozzá a változókat az előfizetési kulcs, végpont és a feltölteni kívánt kép elérési útját.

    ```csharp
        const string accessKey = "<yoursubscriptionkeygoeshere>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<pathtoimagegoeshere>";
    ```


1. Hozzon létre egy meghívott metódus `GetImageFileName()` az elérési utat a rendszerkép lekérése
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

2. Hozzon létre egy metódus a bináris karaktereket a kép lekéréséhez.

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Az űrlap-adatok létrehozása

Ha feltöltenek egy helyi lemezképet, az űrlapadatokat, az API-nak küldött megfelelően kell formázni. A tartalom-szabályozó fejléc tartalmaznia kell a `name` paramétert állítsa "image", és a `filename` paraméter bármilyen karakterlánc lehet beállítani. A képernyő tartalmát a rendszerkép a bináris fájlt tartalmaz. A legnagyobb feltölthető képméret 1 MB.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Az űrlap adatok formázásához, a POST űrlapadatok megfelelően formázási határ karakterláncok hozzáadása, amely meghatározza, hogy elején, teljes és az adatok soremelés karaktert.

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Az alábbi változókat paraméterek hozzáadása az űrlapadatok használható. 

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Hozzon létre egy függvényt, nevű `BuildFormDataStart()` létrehozása az űrlap adatokat, a hálózathatár-karakterláncok és a lemezkép elérési útja elején része.
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Hozzon létre egy függvényt, nevű `BuildFormDataEnd()` hozhat létre a befejezési része az űrlap adatokat, a határ karakterláncok használatával.
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>A Bing Visual Search API meghívása

1. Hozzon létre egy függvényt, a Bing Visual Search-végpont meghívására, és a json-választ adja vissza. A függvény a kezdő vehet igénybe, és a befejező rész fo az űrlapadatokat, egy bájttömböt a rendszerkép-adatok és a egy contentType értéket tartalmazó.

2. Használja a `WebRequest` URI, contentType értéket, és a fejlécek tárolásához.  

3. Használat `request.GetRequestStream()` az űrlap és a bináris adatokat írni. Kérje le a választ. Ez a funkció az alábbi kódhoz hasonlóan kell kinéznie:
        
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

1. Az alkalmazás fő metódusban beolvasása a fájl nevét és a lemezkép bináris a rendszerképet. 

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Állítsa be a bejegyzés törzse, a határ formázását. Ezután hívja meg `startFormData()` és `endFormData` , az űrlap adatok létrehozásához. 

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Hozzon létre a ContentType értéket formázás `CONTENT_TYPE_HEADER_PARAMS` és az űrlap adatok határ.

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Az API-válasz lekérése meghívásával `BingImageSearch()`. Ezután nyomtassa ki a választ.

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>HttpClient használata

HttpClient használata esetén a MultipartFormDataContent segítségével létrehozhatja az űrlapadatokat. Cserélje le az előző példában található elnevezett metódusokat a következő kódszakaszokra.

Cserélje le a Main metódust a következő kódra:

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

Cserélje le a BingImageSearch metódust a következő kódra:

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
> [Egyéni keresés webes alkalmazás készítése](../tutorial-bing-visual-search-single-page-app.md)
