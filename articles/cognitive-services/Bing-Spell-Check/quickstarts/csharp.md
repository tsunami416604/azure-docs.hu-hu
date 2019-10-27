---
title: 'Gyors útmutató: helyesírás ellenőrzése a Bing Spell Check REST API ésC#'
titleSuffix: Azure Cognitive Services
description: Az Bing Spell Check REST API használatának első lépései a helyesírás és a nyelvtan ellenőrzéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: 93b5c395a0d121305c092229d862bf9ecaa4789c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72936056"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Gyors útmutató: helyesírás ellenőrzése a Bing Spell Check REST API ésC#

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Spell Check REST API. Ez az C# egyszerű alkalmazás egy kérelmet küld az API-nak, és a javasolt javítások listáját adja vissza. Bár ez az alkalmazás C# nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel. Az alkalmazás forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017 vagy újabb](https://www.visualstudio.com/downloads/)verziójának bármely kiadása.
* `Newtonsoft.Json` telepítése NuGet-csomagként a Visual Studióban:
    1. A **megoldáskezelő**kattintson a jobb gombbal a megoldás fájljára.
    1. Válassza **a megoldás NuGet-csomagok kezelése**lehetőséget.
    1. Keresse meg `Newtonsoft.Json` és telepítse a csomagot.
* Ha Linux/MacOS rendszert használ, akkor az alkalmazás a [mono](https://www.mono-project.com/)használatával futtatható.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy `SpellCheckSample` nevű új konzolos megoldást a Visual Studióban. Ezután adja hozzá a következő névtereket a fő kódfájlhoz.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a helyesírási ellenőrzéshez használt szöveghez.

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

3. Hozzon létre egy változót a keresési paraméterekhez. `mkt=`után fűzze hozzá a piaci kódot. A piaci kód az az ország, ahonnan a kérést elvégzi. A `&mode=`után fűzze hozzá a helyesírás-ellenőrzési módot is. A mód `proof` (a legtöbb helyesírási/nyelvtani hibát kihasználva) vagy `spell` (a legtöbb helyesírást, de nem annyi nyelvtani hibát).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Helyesírás-ellenőrzési kérelem létrehozása és elküldése

1. Hozzon létre egy `SpellCheck()` nevű aszinkron függvényt, amely egy kérést küld az API-nak. Hozzon létre egy `HttpClient`, és adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez. Ezután hajtsa végre a következő lépéseket a függvényen belül.

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Hozza létre a kérelem URI-JÁT a gazdagép, az elérési út és a paraméterek hozzáfűzésével.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Hozzon létre egy listát a szöveget tartalmazó `KeyValuePair` objektummal, és hozzon létre egy `FormUrlEncodedContent` objektumot. Adja meg a fejléc adatait, és a `PostAsync()` használatával küldje el a kérést.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>Az API-válasz beolvasása és nyomtatása

### <a name="get-the-client-id-header"></a>Az ügyfél-azonosító fejlécének beolvasása

Ha a válasz `X-MSEdge-ClientID` fejlécet tartalmaz, szerezze be az értéket, és nyomtassa ki.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Válasz beolvasása

Az API válaszának beolvasása. Deszerializálja a JSON-objektumot, és kinyomtatja a konzolra.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>A helyesírás-ellenőrzési függvény meghívása

A projekt fő függvényében hívja meg a `SpellCheck()`.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="example-json-response"></a>Példa JSON-válaszra

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorials/spellcheck.md)

- [Mi a Bing Spell Check API?](../overview.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
