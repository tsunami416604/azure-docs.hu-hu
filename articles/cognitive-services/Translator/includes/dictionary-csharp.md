---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 55ad3591a8c2e7d5de6d1efe255e0f3a4b3c11bd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69907033"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>.NET Core projekt létrehozása

Nyisson meg egy új parancssort (vagy terminálmunkamenetet), és futtassa a következő parancsokat:

```console
dotnet new console -o alternate-sample
cd alternate-sample
```

Az első parancs két dolgot tesz. Létrehoz egy új .NET konzolalkalmazást, és `alternate-sample`létrehoz egy könyvtárat. A második parancs a projekt könyvtárára változik.

Ezután telepítenie kell Json.Net. A projekt könyvtárában futtassa a következőket:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Kötelező névterek hozzáadása a projekthez

A `dotnet new console` korábban futtatott parancs létrehozott `Program.cs`egy projektet, beleértve a . Ez a fájl az, ahol akkor tegye az alkalmazás kódját. Nyissa `Program.cs`meg a , és cserélje le a meglévőket utasításokkal. Ezek az utasítások biztosítják, hogy a mintaalkalmazás létrehozásához és futtatásához szükséges összes típushoz hozzáfér.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="get-subscription-information-from-environment-variables"></a>Előfizetési információk bekérése környezeti változókból

Adja hozzá a `Program` következő sorokat az osztályhoz. Ezek a sorok az előfizetési kulcsot és a végpontot a környezeti változókból olvassák, és hibát okoznak, ha bármilyen probléma merül fel.

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

## <a name="create-a-function-to-get-alternate-translations"></a>Függvény létrehozása alternatív fordítások bekéséhez

Az `Program` osztályon belül hozzon `AltTranslation`létre egy függvényt, amelynek neve . Ez az osztály beágyazi a szótár erőforrás hívásához használt kódot, és az eredményt konzolra nyomtatja.

```csharp
static void AltTranslation()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="construct-the-uri"></a>Az URI megépítése

Adja hozzá ezeket a sorokat a `AltTranslation` függvényhez. Észre fogja venni, hogy `api-version`a , két további paraméterrel együtt deklarálva lett. Ezek a paraméterek a fordítási bemenet és kimenet beállítására szolgálnak. Ebben a mintában ezek`en`az angol`es`( ) és a spanyol ( ).

```csharp
string route = "/dictionary/lookup?api-version=3.0";
static string params_ = "from=en&to=es";
static string uri = endpoint + path + params_;
```

Ezután létre kell hoznunk és szerializálnunk kell a JSON-objektumot, amely tartalmazza a lefordítani kívánt szöveget. Ne feledje, hogy a `body` tömbben több objektumot is átadhat.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Elephants" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Az ügyfél példányosítása és kérés

Ezek a sorok példányosítja a `HttpClient` és a: `HttpRequestMessage`

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>A kérelem összeállítása és a válasz nyomtatása

Belül `HttpRequestMessage` a you'll:

* A HTTP-módszer deklarálása
* A kérelem URI-jának kialakítása
* A kérelem törzsének beszúrása (szerializált JSON-objektum)
* Kötelező fejlécek hozzáadása
* Aszinkron kérelem kérése
* A válasz megjelenítése

Adja hozzá ezt `HttpRequestMessage`a kódot a következőhöz:

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

A `PrettyPrint` Hozzáadáshoz adja hozzá a JSON-válasz formázását:
```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

Ha egy Cognitive Services többszolgáltatásos előfizetést használ, `Ocp-Apim-Subscription-Region` a kérelem paramétereit is meg kell egyeznie. [További információ a többszolgáltatásos előfizetés hitelesítéséről.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés `AltTranslation()` a `Main` függvény hívása. Keresse `static void Main(string[] args)` meg és adja hozzá ezeket a sorokat:

```csharp
AltTranslation();
Console.WriteLine("Press any key to continue.");
Console.ReadKey();
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ez az, készen áll a mintaalkalmazás futtatására. A parancssorból (vagy terminálmunkamenetből) keresse meg a projektkönyvtárat, és futtassa a következőket:

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Győződjön meg arról, hogy eltávolít minden bizalmas információt a mintaalkalmazás forráskódjából, például az előfizetési kulcsokból.

## <a name="next-steps"></a>További lépések

Tekintse meg az API-hivatkozást, hogy megértse, mit tehet a Translator Text API-val.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
