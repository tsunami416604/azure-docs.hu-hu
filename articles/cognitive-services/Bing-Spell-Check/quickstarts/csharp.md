---
title: 'Gyors útmutató: Ellenőrizze a helyesírást és a Bing Spell Check REST API és aC#'
titlesuffix: Azure Cognitive Services
description: Ismerkedés a Bing Spell Check REST API használatával a helyesírás-és nyelvtani.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: 7a17c695482f2e9c8158c437c9c40c0abcb07e67
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616288"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Gyors útmutató: Ellenőrizze a helyesírást és a Bing Spell Check REST API és aC#

Ez a rövid útmutató segítségével, a Bing Spell Check REST API első hívását. Ez egyszerű C# alkalmazás egy kérést küld az API-t, és javasolt javítások listáját adja vissza. Bár ez az alkalmazás C# nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel. Az alkalmazás forráskódja elérhető a [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
* A telepítendő `Newtonsoft.Json` a Visual studióban NuGet csomag:
    1. A Megoldáskezelőben, a jobb gombbal a megoldásfájlt.
    1. Válassza ki **NuGet-csomagok kezelése megoldáshoz**.
    1. Keresse meg `Newtonsoft.Json` és telepítse a csomagot.
* Linux/MacOS rendszeren használja, ha az alkalmazás használatával futtatható [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új konzol megoldást nevű `SpellCheckSample` a Visual Studióban. Ezután adja hozzá a következő névtereket a fő kódfájlhoz.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Hozzon létre változókat az API-végpont, az előfizetési kulcs és a szöveg helyesírás be van jelölve.

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. Hozzon létre egy változót a keresési paraméterek. Fűzze hozzá a piaci kód után `mkt=`. A piaci kódja az ország, a kérést. Ezenkívül hozzáfűzéssel a helyesírás-ellenőrzése után `&mode=`. Módban, vagy `proof` (a legtöbb helyesírási vagy nyelvtani hibát kivételekkel) vagy `spell` (a legtöbb helyesírási, de nem annyi nyelvtani hibát kivételekkel).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Hozzon létre, és helyesírás-ellenőrzés kérés küldése

1. Hozzon létre egy aszinkron a hívott függvény `SpellCheck()` egy kérést küldhet az API-t. Hozzon létre egy `HttpClient`, és adja hozzá az előfizetési kulcs, a `Ocp-Apim-Subscription-Key` fejléc. Hajtsa végre az alábbi lépéseket a függvényen belül.

    ```csharp
    async static void SpellCheck()
    {
        HttpClient client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = new HttpResponseMessage();
        // add the rest of the code snippets here (except for main())...
    }

2. Create the URI for your request by appending your host, path, and parameters.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Hozzon létre egy listát egy `KeyValuePair` a szöveget tartalmazó objektumot, és ezzel hozzon létre egy `FormUrlEncodedContent` objektum. A fejléc-információkat, és használata `PostAsync()` a kérelem elküldéséhez.

    ```csharp
    List<KeyValuePair<string, string>> values = new List<KeyValuePair<string, string>>();
    values.Add(new KeyValuePair<string, string>("text", text));
    
    using (FormUrlEncodedContent content = new FormUrlEncodedContent(values))
    {
        content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
        response = await client.PostAsync(uri, content);
    }
    ```

## <a name="get-and-print-the-api-response"></a>GET, és nyomtassa ki az API-válasz

### <a name="get-the-client-id-header"></a>Az ügyfél-Azonosítójának fejlécét beolvasása

Ha a válasz tartalmaz egy `X-MSEdge-ClientID` fejléc, az értéket, és nyomtassa ki.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>A válasz

A választ kaphat az API-ból. A JSON-objektum deszerializálása, és nyomtassa ki a konzolhoz.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>A helyesírás-ellenőrzés függvény hívása

A projekt fő függvényben hívja `SpellCheck()`.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="example-json-response"></a>Példa JSON-válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorials/spellcheck.md)

- [Mi az a Bing Spell Check API?](../overview.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
