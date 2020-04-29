---
title: 'Gyors útmutató: helyesírás ellenőrzése a REST API és C#-Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: Az Bing Spell Check REST API használatának első lépései a helyesírás és a nyelvtan ellenőrzéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 036ea00362b604957a1887127fca0b8d775d4e7b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75382949"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Gyors útmutató: helyesírás ellenőrzése a Bing Spell Check REST API és C #

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Spell Check REST API. Ez az egyszerű C#-alkalmazás egy kérelmet küld az API-nak, és a javasolt javítások listáját adja vissza. Bár ez az alkalmazás C# nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel. Az alkalmazás forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017 vagy újabb](https://www.visualstudio.com/downloads/)verziójának bármely kiadása.
* Telepítés `Newtonsoft.Json` NuGet-csomagként a Visual Studióban:
    1. A **megoldáskezelő**kattintson a jobb gombbal a megoldás fájljára.
    1. Válassza **a megoldás NuGet-csomagok kezelése**lehetőséget.
    1. Keresse meg `Newtonsoft.Json` és telepítse a csomagot.
* Ha Linux/MacOS rendszert használ, akkor az alkalmazás a [mono](https://www.mono-project.com/)használatával futtatható.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új, `SpellCheckSample` a Visual Studióban megnevezett konzol-megoldást. Ezután adja hozzá a következő névtereket a fő kódfájlhoz.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a helyesírási ellenőrzéshez használt szöveghez. Használhatja az alábbi globális végpontot, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpontot.

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

3. Hozzon létre egy változót a keresési paraméterekhez. Adja hozzá a piac kódját `mkt=`a következő után:. A piaci kód az az ország, ahonnan a kérést elvégzi. Továbbá a helyesírás-ellenőrzési mód hozzáfűzése a következő `&mode=`után:. A mód vagy `proof` (a legtöbb helyesírási/nyelvtani hibát kigyűjti) vagy `spell` (a legtöbb helyesírási hiba, de nem annyi nyelvtani hiba).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Helyesírás-ellenőrzési kérelem létrehozása és elküldése

1. Hozzon létre egy nevű `SpellCheck()` aszinkron függvényt, amely egy kérést küld az API-nak. Hozzon `HttpClient`létre egy, és adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez. Ezután hajtsa végre a következő lépéseket a függvényen belül.

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

3. Hozzon létre egy listát `KeyValuePair` a szöveget tartalmazó objektummal, és hozzon létre egy `FormUrlEncodedContent` objektumot. Adja meg a fejléc adatait, és `PostAsync()` a paranccsal küldje el a kérelmet.

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

A projekt fő függvényében hívja `SpellCheck()`meg a t.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Hozza létre és futtassa a projektet. Ha a Visual studiót használja, nyomja le az **F5** billentyűt a fájl hibakereséséhez.

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
