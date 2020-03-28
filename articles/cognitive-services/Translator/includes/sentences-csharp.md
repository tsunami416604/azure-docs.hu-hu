---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 3d92d3f959e2ad44daa82d6b609b9357cee969c9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906870"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>.NET Core projekt létrehozása

Nyisson meg egy új parancssort (vagy terminálmunkamenetet), és futtassa a következő parancsokat:

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

Az első parancs két dolgot tesz. Létrehoz egy új .NET konzolalkalmazást, és `sentences-sample`létrehoz egy könyvtárat. A második parancs a projekt könyvtárára változik.

Ezután telepítenie kell Json.Net. A projekt könyvtárában futtassa a következőket:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Válassza ki a C# nyelvi verziót

Ehhez a rövid útmutatóhoz C 7.1-es vagy újabb verzió szükséges. A projekt C# verziójának módosítására többféleképpen is módosíthatja. Ebben az útmutatóban bemutatjuk, hogyan `sentences-sample.csproj` állíthatja be a fájlt. Az összes elérhető beállításról, például a Visual Studio nyelvének módosításáról a [C# nyelvi verzió kiválasztása című](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version)témakörben található.

Nyissa meg a `sentences-sample.csproj`projektet, majd nyissa meg a programot. Győződjön `LangVersion` meg róla, hogy 7.1-es vagy újabb beállítással van beállítva. Ha a nyelvi verzióhoz nincs tulajdonságcsoport, adja hozzá a következő sorokat:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Kötelező névterek hozzáadása a projekthez

A `dotnet new console` korábban futtatott parancs létrehozott `Program.cs`egy projektet, beleértve a . Ez a fájl az, ahol akkor tegye az alkalmazás kódját. Nyissa `Program.cs`meg a , és cserélje le a meglévőket utasításokkal. Ezek az utasítások biztosítják, hogy a mintaalkalmazás létrehozásához és futtatásához szükséges összes típushoz hozzáfér.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Osztályok létrehozása a JSON-válaszhoz

Ezután létrehozunk egy osztályt, amelyet a Translator Text API által visszaadott JSON-válasz deszerializálásakor használunk.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
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

## <a name="create-a-function-to-determine-sentence-length"></a>A mondat hosszának meghatározásához hozzon létre egy függvényt

Az `Program` osztályban hozzon létre `BreakSentenceRequest()`egy új függvényt, amelynek neve . Ez a függvény négy `subscriptionKey` `endpoint`argumentumot foglal el: , , `route`, és `inputText`.

```csharp
static public async Task BreakSentenceRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-break-sentence-request"></a>A szünetmondat-kérelem szerializálása

Ezután létre kell hoznia és szerializálnia kell a szöveget tartalmazó JSON-objektumot. Ne feledje, hogy a `body` tömbben több objektumot is átadhat.

```csharp
object[] body = new object[] { new { Text = inputText } };
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

Ha egy Cognitive Services többszolgáltatásos előfizetést használ, `Ocp-Apim-Subscription-Region` a kérelem paramétereit is meg kell egyeznie. [További információ a többszolgáltatásos előfizetés hitelesítéséről.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés `BreakSentenceRequest()` a `Main` függvény hívása. Keresse `static void Main(string[] args)` meg és cserélje ki ezzel a kóddal:

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

Észre fogja venni, `Main`hogy a , `subscriptionKey` `endpoint`ön `route`deklarál `breakSentenceText`, , és a szöveget, hogy értékelje.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ez az, készen áll a mintaalkalmazás futtatására. A parancssorból (vagy terminálmunkamenetből) keresse meg a projektkönyvtárat, és futtassa a következőket:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

A minta futtatása után a következő nyomtatott feliratot kell látnia a terminálra:

```bash
The detected language is \'en\'. Confidence is: 1.
The first sentence length is: 25
```

Ez az üzenet a nyers JSON-ból épül fel, amely így fog kinézni:

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Győződjön meg arról, hogy eltávolít minden bizalmas információt a mintaalkalmazás forráskódjából, például az előfizetési kulcsokból.

## <a name="next-steps"></a>További lépések

Tekintse meg az API-hivatkozást, hogy megértse, mit tehet a Translator Text API-val.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
