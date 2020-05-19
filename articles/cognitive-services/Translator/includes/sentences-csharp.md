---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: dc8afd5a61a40b14792bb564d394604010718888
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586730"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>.NET Core-projekt létrehozása

Nyisson meg egy új parancssort (vagy terminál-munkamenetet), és futtassa a következő parancsokat:

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

Az első parancs két dolgot mutat be. Létrehoz egy új .NET-konzol alkalmazást, és létrehoz egy nevű könyvtárat `sentences-sample` . A második parancs a projekt könyvtárára változik.

Ezután telepítenie kell a Json.Net. A projekt címtárában futtassa a következőt:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>A C# nyelvi verziójának kiválasztása

Ehhez a rövid útmutatóhoz C# 7,1 vagy újabb verzió szükséges. A projekt C#-verziója többféleképpen módosítható. Ebben az útmutatóban bemutatjuk, hogyan módosíthatja a `sentences-sample.csproj` fájlt. Az összes elérhető lehetőség, például a nyelv módosítása a Visual Studióban: [válassza a C# nyelvi verzióját](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Nyissa meg a projektet, majd nyissa meg a t `sentences-sample.csproj` . Győződjön meg arról, hogy a értéke `LangVersion` 7,1 vagy újabb. Ha nincs a nyelvi verzióhoz tartozó tulajdonságérték, adja hozzá a következő sorokat:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Szükséges névterek hozzáadása a projekthez

A `dotnet new console` korábban létrehozott parancs egy projektet hozott létre, beleértve a következőket: `Program.cs` . Ez a fájl az alkalmazás kódjának elhelyezése. Nyissa meg `Program.cs` , és cserélje le a meglévő using utasításokat. Ezek az utasítások biztosítják, hogy hozzáférjen a minta alkalmazás létrehozásához és futtatásához szükséges összes típushoz.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Osztályok létrehozása a JSON-válaszhoz

Ezután létrehozunk egy olyan osztályt, amelyet a fordító által visszaadott JSON-válasz deszerializálása során használunk.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator.
/// </summary>
public class BreakSentenceResult
{
    public int[] SentLen { get; set; }
    public DetectedLanguage DetectedLanguage { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}
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

## <a name="create-a-function-to-determine-sentence-length"></a>Függvény létrehozása a mondat hosszának meghatározásához

A `Program` osztályban hozzon létre egy nevű új függvényt `BreakSentenceRequest()` . Ez a függvény négy argumentumot vesz igénybe: `subscriptionKey` ,, `endpoint` `route` és `inputText` .

```csharp
static public async Task BreakSentenceRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-break-sentence-request"></a>A tördelési mondat kérésének szerializálása

Ezután létre kell hoznia és szerializálnia kell a szöveget tartalmazó JSON-objektumot. Ne feledje, hogy több objektumot is át tud adni a `body` tömbben.

```csharp
object[] body = new object[] { new { Text = inputText } };
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
// Build the request.
// Set the method to POST
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(endpoint + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
BreakSentenceResult[] deserializedOutput = JsonConvert.DeserializeObject<BreakSentenceResult[]>(result);
foreach (BreakSentenceResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    Console.WriteLine("The first sentence length is: {0}", o.SentLen[0]);
}
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek is szerepelnie kell `Ocp-Apim-Subscription-Region` . [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés a függvény hívása `BreakSentenceRequest()` `Main` . Keresse meg `static void Main(string[] args)` és cserélje le a következő kódra:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string route = "/breaksentence?api-version=3.0";
    // Feel free to use any string.
    string breakSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await BreakSentenceRequest(subscriptionKey, endpoint, route, breakSentenceText);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

Megfigyelheti, hogy a-ben `Main` deklarálja,, `subscriptionKey` `endpoint` `route` és a kiértékelni kívánt szöveget `breakSentenceText` .

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ekkor készen áll a minta alkalmazás futtatására. A parancssorból (vagy a terminál-munkamenetből) navigáljon a projekt könyvtárába, és futtassa a következő parancsot:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

A minta futtatása után a következőnek kell megjelennie a terminálon:

```bash
The detected language is \'en\'. Confidence is: 1.
The first sentence length is: 25
```

Ez az üzenet a nyers JSON-ből készült, amely így fog kinézni:

```json
[
    {
        "detectedLanguage":
        {
            "language":"en",
            "score":1.0
        },
        "sentLen":[25,32,35]
    }
]
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ügyeljen arra, hogy eltávolítsa a mintául szolgáló alkalmazás forráskódjának bizalmas adatait, például az előfizetési kulcsokat.

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a fordítóval.

> [!div class="nextstepaction"]
> [API-referenciák](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
