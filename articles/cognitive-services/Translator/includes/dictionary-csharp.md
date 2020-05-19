---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 3dc58b68270707eb5e92214def85ec8cf9cb3f5b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587021"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>.NET Core-projekt létrehozása

Nyisson meg egy új parancssort (vagy terminál-munkamenetet), és futtassa a következő parancsokat:

```console
dotnet new console -o alternate-sample
cd alternate-sample
```

Az első parancs két dolgot mutat be. Létrehoz egy új .NET-konzol alkalmazást, és létrehoz egy nevű könyvtárat `alternate-sample` . A második parancs a projekt könyvtárára változik.

Ezután telepítenie kell a Json.Net. A projekt címtárában futtassa a következőt:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Szükséges névterek hozzáadása a projekthez

A `dotnet new console` korábban létrehozott parancs egy projektet hozott létre, beleértve a következőket: `Program.cs` . Ez a fájl az alkalmazás kódjának elhelyezése. Nyissa meg `Program.cs` , és cserélje le a meglévő using utasításokat. Ezek az utasítások biztosítják, hogy hozzáférjen a minta alkalmazás létrehozásához és futtatásához szükséges összes típushoz.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="get-subscription-information-from-environment-variables"></a>Előfizetési adatok beolvasása környezeti változókból

Adja hozzá a következő sorokat a `Program` osztályhoz. Ezek a sorok beolvasják az előfizetési kulcsot és a végpontot a környezeti változókból, és hibát jeleznek, ha problémákba ütközik.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-get-alternate-translations"></a>Függvény létrehozása alternatív fordítások beszerzéséhez

`Program`Hozzon létre egy nevű függvényt a osztályon belül `AltTranslation` . Ez az osztály a szótári erőforrás meghívásához használt kódot tartalmazza, és kiírja az eredményt a konzolra.

```csharp
static void AltTranslation()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="construct-the-uri"></a>Az URI kiépítése

Adja hozzá ezeket a sorokat a `AltTranslation` függvényhez. Figyelje meg, hogy a-val együtt `api-version` két további paraméter is be van jelentve. Ezek a paraméterek a fordítás bemenetének és kimenetének beállítására szolgálnak. Ebben a példában az angol ( `en` ) és a spanyol ( `es` ).

```csharp
string route = "/dictionary/lookup?api-version=3.0";
static string params_ = "from=en&to=es";
static string uri = endpoint + path + params_;
```

Ezután létre kell hoznia és szerializálnia kell a lefordítani kívánt szöveget tartalmazó JSON-objektumot. Ne feledje, hogy több objektumot is át tud adni a `body` tömbben.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Elephants" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Az ügyfél példányának létrehozása és kérelem elkészítése

Ezek a sorok a `HttpClient` és a következőket hozzanak létre `HttpRequestMessage` :

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>A kérelem kiépítése és a válasz nyomtatása

A a következőkön belül fog megjelenni `HttpRequestMessage` :

* A HTTP-metódus deklarálása
* A kérelem URI-ja felépítése
* A kérelem törzsének beszúrása (szerializált JSON-objektum)
* Kötelező fejlécek hozzáadása
* Aszinkron kérelem létrehozása
* A válasz megjelenítése

Adja hozzá ezt a kódot a következőhöz `HttpRequestMessage` :

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(uri);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Hozzáadás a `PrettyPrint` JSON-válasz formázásának hozzáadásához:
```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek is szerepelnie kell `Ocp-Apim-Subscription-Region` . [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés a függvény hívása `AltTranslation()` `Main` . Keresse meg `static void Main(string[] args)` és adja hozzá a következő sorokat:

```csharp
AltTranslation();
Console.WriteLine("Press any key to continue.");
Console.ReadKey();
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ekkor készen áll a minta alkalmazás futtatására. A parancssorból (vagy a terminál-munkamenetből) navigáljon a projekt könyvtárába, és futtassa a következő parancsot:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ügyeljen arra, hogy eltávolítsa a mintául szolgáló alkalmazás forráskódjának bizalmas adatait, például az előfizetési kulcsokat.

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a fordítóval.

> [!div class="nextstepaction"]
> [API-referenciák](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
