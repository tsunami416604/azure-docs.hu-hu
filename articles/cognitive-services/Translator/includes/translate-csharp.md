---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 64a934196bb964561f36b9d95a2467b149847225
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906725"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>.NET Core projekt létrehozása

Nyisson meg egy új parancssort (vagy terminálmunkamenetet), és futtassa a következő parancsokat:

```console
dotnet new console -o translate-sample
cd translate-sample
```

Az első parancs két dolgot tesz. Létrehoz egy új .NET konzolalkalmazást, és `translate-sample`létrehoz egy könyvtárat. A második parancs a projekt könyvtárára változik.

Ezután telepítenie kell Json.Net. A projekt könyvtárában futtassa a következőket:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Válassza ki a C# nyelvi verziót

Ehhez a rövid útmutatóhoz C 7.1-es vagy újabb verzió szükséges. A projekt C# verziójának módosítására többféleképpen is módosíthatja. Ebben az útmutatóban bemutatjuk, hogyan `translate-sample.csproj` állíthatja be a fájlt. Az összes elérhető beállításról, például a Visual Studio nyelvének módosításáról a [C# nyelvi verzió kiválasztása című](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version)témakörben található.

Nyissa meg a `translate-sample.csproj`projektet, majd nyissa meg a programot. Győződjön `LangVersion` meg róla, hogy 7.1-es vagy újabb beállítással van beállítva. Ha a nyelvi verzióhoz nincs tulajdonságcsoport, adja hozzá a következő sorokat:

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

Ezután hozzon létre egy osztályok, amelyek a Translator Text API által visszaadott JSON-válasz deszerializálásakor használt osztályok.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class TranslationResult
{
    public DetectedLanguage DetectedLanguage { get; set; }
    public TextResult SourceText { get; set; }
    public Translation[] Translations { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}

public class TextResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}

public class Translation
{
    public string Text { get; set; }
    public TextResult Transliteration { get; set; }
    public string To { get; set; }
    public Alignment Alignment { get; set; }
    public SentenceLength SentLen { get; set; }
}

public class Alignment
{
    public string Proj { get; set; }
}

public class SentenceLength
{
    public int[] SrcSentLen { get; set; }
    public int[] TransSentLen { get; set; }
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

## <a name="create-a-function-to-translate-text"></a>Szöveg fordítására vonatkozó függvény létrehozása

Az `Program` osztályban hozzon létre egy aszinkron függvényt, amelynek neve `TranslateTextRequest()`. Ez a függvény négy `subscriptionKey` `host`argumentumot foglal el: , , `route`, és `inputText`.

```csharp
// This sample requires C# 7.1 or later for async/await.
// Async call to the Translator Text API
static public async Task TranslateTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>A fordítási kérelem szerializálása

Ezután létre kell hoznunk és szerializálnunk kell a JSON-objektumot, amely tartalmazza a lefordítani kívánt szöveget. Ne feledje, hogy a alkalmazásban több `body`objektumot is átadhat.

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
* A válasz nyomtatása a korábban létrehozott osztályok használatával

Adja hozzá ezt `HttpRequestMessage`a kódot a következőhöz:

```csharp
// Build the request.
// Set the method to Post.
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
TranslationResult[] deserializedOutput = JsonConvert.DeserializeObject<TranslationResult[]>(result);
// Iterate over the deserialized results.
foreach (TranslationResult o in deserializedOutput)
{
    // Print the detected input language and confidence score.
    Console.WriteLine("Detected input language: {0}\nConfidence score: {1}\n", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    // Iterate over the results and print each translation.
    foreach (Translation t in o.Translations)
    {
        Console.WriteLine("Translated to {0}: {1}", t.To, t.Text);
    }
}
```

Ha egy Cognitive Services többszolgáltatásos előfizetést használ, `Ocp-Apim-Subscription-Region` a kérelem paramétereit is meg kell egyeznie. [További információ a többszolgáltatásos előfizetés hitelesítéséről.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés `TranslateTextRequest()` a `Main` függvény hívása. Ebben a mintában német ( ),`de`olasz`it`( ),`ja`japán (`th`) és thai ( ) fordításra fordítunk. Keresse `static void Main(string[] args)` meg és cserélje ki ezzel a kóddal:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate
    string route = "/translate?api-version=3.0&to=de&to=it&to=ja&to=th";
    // Prompts you for text to translate. If you'd prefer, you can
    // provide a string as textToTranslate.
    Console.Write("Type the phrase you'd like to translate? ");
    string textToTranslate = Console.ReadLine();
    await TranslateTextRequest(subscriptionKey, endpoint, route, textToTranslate);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

Észre fogod venni, hogy `Main`a, `subscriptionKey` `endpoint`te `route`deklarál , és . Ezenkívül a felhasználótól kéri a `Console.Readline()` bevitelt, és `textToTranslate`hozzárendeli az értéket.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ez az, készen áll a mintaalkalmazás futtatására. A parancssorból (vagy terminálmunkamenetből) keresse meg a projektkönyvtárat, és futtassa a következőket:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

A minta futtatása után a következő nyomtatott feliratot kell látnia a terminálra:

```bash
Detected input language: en
Confidence score: 1

Translated to de: Hallo Welt!
Translated to it: Salve, mondo!
Translated to ja: ハローワールド！
Translated to th: หวัดดีชาวโลก!
```

Ez az üzenet a nyers JSON-ból épül fel, amely így fog kinézni:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      },
      {
        "text": "ハローワールド！",
        "to": "ja"
      },
      {
        "text": "หวัดดีชาวโลก!",
        "to": "th"
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
