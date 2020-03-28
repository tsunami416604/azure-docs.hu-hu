---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 0c263ed1f18ceaa2db976632ea31b9fe1eb47a93
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69907193"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>.NET Core projekt létrehozása

Nyisson meg egy új parancssort (vagy terminálmunkamenetet), és futtassa a következő parancsokat:

```console
dotnet new console -o detect-sample
cd detect-sample
```

Az első parancs két dolgot tesz. Létrehoz egy új .NET konzolalkalmazást, és `detect-sample`létrehoz egy könyvtárat. A második parancs a projekt könyvtárára változik.

Ezután telepítenie kell Json.Net. A projekt könyvtárában futtassa a következőket:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Válassza ki a C# nyelvi verziót

Ehhez a rövid útmutatóhoz C 7.1-es vagy újabb verzió szükséges. A projekt C# verziójának módosítására többféleképpen is módosíthatja. Ebben az útmutatóban bemutatjuk, hogyan `detect-sample.csproj` állíthatja be a fájlt. Az összes elérhető beállításról, például a Visual Studio nyelvének módosításáról a [C# nyelvi verzió kiválasztása című](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version)témakörben található.

Nyissa meg a `detect-sample.csproj`projektet, majd nyissa meg a programot. Győződjön `LangVersion` meg róla, hogy 7.1-es vagy újabb beállítással van beállítva. Ha a nyelvi verzióhoz nincs tulajdonságcsoport, adja hozzá a következő sorokat:

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
public class DetectResult
{
    public string Language { get; set; }
    public float Score { get; set; }
    public bool IsTranslationSupported { get; set; }
    public bool IsTransliterationSupported { get; set; }
    public AltTranslations[] Alternatives { get; set; }
}
public class AltTranslations
{
    public string Language { get; set; }
    public float Score { get; set; }
    public bool IsTranslationSupported { get; set; }
    public bool IsTransliterationSupported { get; set; }
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

## <a name="create-a-function-to-detect-the-source-texts-language"></a>A forrásszöveg nyelvének észlelésére szolgáló függvény létrehozása

Az `Program` osztályban hozzon `DetectTextRequest()`létre egy függvényt, amelynek neve . Ez az osztály magában foglalja a detect erőforrás hívásához használt kódot, és kinyomtatja az eredményt a konzolra.

```csharp
static public async Task DetectTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-detect-request"></a>Szerializálja a észlelési kérelmet

Ezután létre kell hoznunk és szerializálnunk kell a JSON-objektumot, amely tartalmazza a nyelvfelismerést tartalmazó szöveget.

```csharp
System.Object[] body = new System.Object[] { new { Text = inputText } };
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
DetectResult[] deserializedOutput = JsonConvert.DeserializeObject<DetectResult[]>(result);
// Iterate over the deserialized response.
foreach (DetectResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.\nTranslation supported: {2}.\nTransliteration supported: {3}.\n",
        o.Language, o.Score, o.IsTranslationSupported, o.IsTransliterationSupported);
    // Create a counter
    int counter = 0;
    // Iterate over alternate translations.
    foreach (AltTranslations a in o.Alternatives)
    {
        counter++;
        Console.WriteLine("Alternative {0}", counter);
        Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.\nTranslation supported: {2}.\nTransliteration supported: {3}.\n",
            a.Language, a.Score, a.IsTranslationSupported, a.IsTransliterationSupported);
    }
}
```

Ha egy Cognitive Services többszolgáltatásos előfizetést használ, `Ocp-Apim-Subscription-Region` a kérelem paramétereit is meg kell egyeznie. [További információ a többszolgáltatásos előfizetés hitelesítéséről.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés `DetectTextRequest()` a `Main` függvény hívása. Keresse `static void Main(string[] args)` meg és cserélje ki ezzel a kóddal:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string route = "/detect?api-version=3.0";
    string detectSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await DetectTextRequest(subscriptionKey, endpoint, route, detectSentenceText);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```
## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ez az, készen áll a mintaalkalmazás futtatására. A parancssorból (vagy terminálmunkamenetből) keresse meg a projektkönyvtárat, és futtassa a következőket:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

A minta futtatása után a következő nyomtatott feliratot kell látnia a terminálra:

> [!NOTE]
> Keresse meg az ország/régió rövidítését ebben [a nyelveklistájában.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)

```bash
The detected language is 'en'. Confidence is: 1.
Translation supported: True.
Transliteration supported: False.

Alternative 1
The detected language is 'fil'. Confidence is: 0.82.
Translation supported: True.
Transliteration supported: False.

Alternative 2
The detected language is 'ro'. Confidence is: 1.
Translation supported: True.
Transliteration supported: False.
```

Ez az üzenet a nyers JSON-ból épül fel, amely így fog kinézni:

```json
[  
    {  
        "language":"en",
        "score":1.0,
        "isTranslationSupported":true,
        "isTransliterationSupported":false,
        "alternatives":[  
            {  
                "language":"fil",
                "score":0.82,
                "isTranslationSupported":true,
                "isTransliterationSupported":false
            },
            {  
                "language":"ro",
                "score":1.0,
                "isTranslationSupported":true,
                "isTransliterationSupported":false
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
