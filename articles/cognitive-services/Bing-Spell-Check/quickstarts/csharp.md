---
title: 'Rövid útmutató: Helyesírás-ellenőrzés a REST API-val és a C# - Bing helyesírás-ellenőrzés'
titleSuffix: Azure Cognitive Services
description: Első lépések a Bing Helyesírás-ellenőrző REST API-val a helyesírás és a nyelvhelyesség ellenőrzéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 036ea00362b604957a1887127fca0b8d775d4e7b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382949"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Rövid útmutató: Helyesírás-ellenőrzés a Bing helyesírás-ellenőrző REST API-val és a C-vel #

Ezzel a rövid útmutatóval elsőként hívhatja meg a Bing Helyesírás-ellenőrző REST API-t. Ez az egyszerű C# alkalmazás kérelmet küld az API-nak, és visszaadja a javasolt javítások listáját. Bár ez az alkalmazás C# nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel. Az alkalmazás forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs)

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017-es vagy újabb verzióinak](https://www.visualstudio.com/downloads/)bármely kiadása.
* A `Newtonsoft.Json` Telepítés NuGet csomagként a Visual Studio-ban:
    1. A **Megoldáskezelőben**kattintson a jobb gombbal a Megoldás fájlra.
    1. Válassza **a NuGet csomagok kezelése a megoldáshoz**lehetőséget.
    1. Keresse `Newtonsoft.Json` meg és telepítse a csomagot.
* Ha Linux/MacOS-t használsz, ez az alkalmazás [monóval](https://www.mono-project.com/)futtatható.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy `SpellCheckSample` új konzolmegoldást, amelyet a Visual Studio nevez meg. Ezután adja hozzá a következő névtereket a fő kódfájlhoz.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a helyesírás-ellenőrzésnek kivetendő szöveghez. Használhatja az alábbi globális végpontot, vagy az [egyéni altartomány-végpontot,](../../../cognitive-services/cognitive-services-custom-subdomains.md) amely az azure-portálon jelenik meg az erőforráshoz.

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

3. Hozzon létre egy változót a keresési paraméterekhez. Fűzze hozzá a `mkt=`piaci kódot után . A piaci kód az az ország, ahonnan a kérelmet benyújtod. A helyesírás-ellenőrzési módat is `&mode=`csatolja a után. A mód `proof` vagy (a legtöbb helyesírási/nyelvtani hibát elkap) vagy `spell` (a legtöbb helyesírást, de nem annyi nyelvtani hibát) kapja meg).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Helyesírás-ellenőrzési kérelem létrehozása és küldése

1. Hozzon létre egy aszinkron `SpellCheck()` függvényt, amelyet az API-nak küldött kérelem küldésére hívnak meg. Hozzon `HttpClient`létre egy t, `Ocp-Apim-Subscription-Key` és adja hozzá az előfizetési kulcsot a fejléchez. Ezután hajtsa végre a következő lépéseket a funkción belül.

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Hozza létre a kérelem URI-ját a gazdagép, az elérési út és a paraméterek hozzáfűzésével.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Hozzon létre `KeyValuePair` egy listát a szöveget tartalmazó objektummal, és használja `FormUrlEncodedContent` azt objektum létrehozására. Állítsa be a fejlécadatait, és küldje `PostAsync()` el a kérelmet.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>Az API-válasz beszerezni és kinyomtatni

### <a name="get-the-client-id-header"></a>Az ügyfélazonosító fejlécének beolvasása

Ha a válasz `X-MSEdge-ClientID` fejlécet tartalmaz, vegye le az értéket, és nyomtassa ki.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>A válasz beszerezése

A válasz beszerezni az API-t. Deszerializálja a JSON-objektumot, és nyomtassa ki a konzolra.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>A helyesírás-ellenőrző függvény felhívása

A projekt fő funkciójában `SpellCheck()`hívja a .

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Készítse el és futtassa a projektet. Visual Studio használata esetén nyomja le az **F5** billentyűt a fájl hibakereséséhez.

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorials/spellcheck.md)

- [Mi a Bing Spell Check API?](../overview.md)
- [A Bing Spell Check API 7-es verzió referenciája](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
