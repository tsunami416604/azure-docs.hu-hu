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
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: df77ea73338f37925c0b59a8838c31617ff22ad8
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919403"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Gyors útmutató: helyesírás ellenőrzése a Bing Spell Check REST API és C #

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Spell Check REST API. Ez az egyszerű C#-alkalmazás egy kérelmet küld az API-nak, és a javasolt javítások listáját adja vissza. 

Bár ez az alkalmazás C# nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. Az alkalmazás forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017 vagy újabb](https://www.visualstudio.com/downloads/)verziójának bármely kiadása.
* A NuGet-csomag Newtonsoft.Js. 
     
   A csomag telepítése a Visual Studióban:

     1. A **megoldáskezelő**kattintson a jobb gombbal a megoldás fájljára.
     1. Válassza **a megoldás NuGet-csomagok kezelése**lehetőséget.
     1. Keressen rá a *Newtonsoft.Jsre* , és telepítse a csomagot.

* Ha Linux/MacOS rendszert használ, akkor az alkalmazást a [mono](https://www.mono-project.com/)használatával futtathatja.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy SpellCheckSample nevű új konzolos megoldást a Visual Studióban. Ezután adja hozzá a következő névtereket a fő kódhoz:
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a helyesírási ellenőrzéshez használt szöveghez. Használhatja a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

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

3. Hozzon létre egy karakterláncot a keresési paraméterekhez: 

   1. Rendelje hozzá a piaci kódot a `mkt` paraméterhez az `=` operátorral. A piaci kód annak az országnak/régiónak a kódja, amelyre a kérést végzi. 

   1. Adja hozzá a `mode` paramétert a `&` kezelőhöz, majd rendelje hozzá a helyesírás-ellenőrzési módot. A mód lehet (a `proof` legtöbb helyesírási/nyelvtani hibát felhasználhatja) `spell` , vagy (a legtöbb helyesírási hibát kigyűjti, de nem annyi nyelvtani hibát).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Helyesírás-ellenőrzési kérelem létrehozása és elküldése

1. Hozzon létre egy nevű aszinkron függvényt `SpellCheck()` , amely egy kérést küld az API-nak. Hozzon létre egy `HttpClient` , és adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez. A függvényen belül kövesse a következő lépéseket.

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

3. Hozzon létre egy listát a `KeyValuePair` szöveget tartalmazó objektummal, és hozzon létre egy `FormUrlEncodedContent` objektumot. Adja meg a fejléc adatait, és a paranccsal `PostAsync()` küldje el a kérelmet.

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

A `Main()` projekt függvényében hívja meg a t `SpellCheck()` .

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
- [Bing Spell Check API v7-dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
