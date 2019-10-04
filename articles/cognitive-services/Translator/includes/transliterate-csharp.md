---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 81fb599ca4987adccdb91baa7a74c33ae3af48d4
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906573"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Egy .NET Core-projekt létrehozása

Nyisson meg egy új parancssort (vagy a terminál-munkamenetben), és futtassa a következő parancsokat:

```console
dotnet new console -o transliterate-sample
cd transliterate-sample
```

Az első parancs két dolgot eredményez. Egy új .NET-konzolalkalmazást hoz létre, és létrehoz egy könyvtárat nevű `transliterate-sample`. A második parancs módosítja a projekt számára a könyvtárba.

Ezután telepítenie kell a Json.Net. A projekt címtárában futtassa a következőt:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Válassza ki a C# nyelvi változatát

Ez a rövid útmutatóhoz C# 7.1-es vagy újabb verziója. Néhány módon módosítsa a C# verzió a projekthez. Ebben az útmutatóban bemutatjuk, hogyan módosíthatja a `transliterate-sample.csproj` fájlt. Az összes rendelkezésre álló beállításokat, például a Visual Studióban, a nyelv módosítását lásd: [válassza ki a C# beállított nyelvi verzióhoz](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Nyissa meg a projektet, majd nyissa meg a `transliterate-sample.csproj`. Győződjön meg arról, hogy `LangVersion` 7.1-es vagy újabb verziójára van beállítva. Ha nincs a tulajdonságcsoport a beállított nyelvi verzióhoz, adja hozzá ezeket a sorokat:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Adja hozzá a projekthez szükséges névterek

A `dotnet new console` parancsot, amely futtatta korábban létrehozott egy projektet, beleértve a `Program.cs`. Ez a fájl meg, ahová az alkalmazás kódjában fog. Nyissa meg `Program.cs`, és cserélje le a meglévő using utasítások. Ezek az utasítások győződjön meg arról, hogy a minta-alkalmazás létrehozásához és futtatásához szükséges összes típusú hozzáférést.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Osztályok létrehozása a JSON-válaszhoz

Ezután létrehozunk egy olyan osztályt, amelyet a Translator Text API által visszaadott JSON-válasz deszerializálása során használunk.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class TransliterationResult
{
    public string Text { get; set; }
    public string Script { get; set; }
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

## <a name="create-a-function-to-transliterate-text"></a>Függvény létrehozása szöveg átbetűzés

Hozzon `Program` létre egy nevű `TransliterateTextRequest()`aszinkron függvényt a osztályon belül. Ez a függvény négy argumentumot `subscriptionKey`vesz `endpoint`igénybe: `inputText` `route`,, és.

```csharp
static public async Task TransliterateTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>A fordítási kérelem szerializálása

Ezután létre kell hoznia és szerializálnia kell a lefordítani kívánt szöveget tartalmazó JSON-objektumot. Ne feledje, hogy több objektumot is átadhat a `body`alkalmazásban.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Az ügyfél példányának létrehozása és kérelem elkészítése

Ezek a sorok a `HttpClient` és a `HttpRequestMessage`következőket hozzanak létre:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>A kérelem kiépítése és a válasz nyomtatása

A a `HttpRequestMessage` következőkön belül fog megjelenni:

* A HTTP-metódus deklarálása
* A kérelem URI-ja felépítése
* A kérelem törzsének beszúrása (szerializált JSON-objektum)
* Kötelező fejlécek hozzáadása
* Aszinkron kérelem létrehozása
* A válasz megjelenítése

Adja hozzá ezt a kódot `HttpRequestMessage`a következőhöz:

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
TransliterationResult[] deserializedOutput = JsonConvert.DeserializeObject<TransliterationResult[]>(result);
// Iterate over the deserialized results.
foreach (TransliterationResult o in deserializedOutput)
{
    Console.WriteLine("Transliterated to {0} script: {1}", o.Script, o.Text);
}
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek `Ocp-Apim-Subscription-Region` is szerepelnie kell. [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés a `TransliterateTextRequest()` `Main` függvény hívása. Ebben a példában a Japánról a latinra transliterating. Keresse `static void Main(string[] args)` meg és cserélje le a következő kódra:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate
    string route = "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
    string textToTransliterate = @"こんにちは";
    await TransliterateTextRequest(subscriptionKey, endpoint, route, textToTransliterate);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

Megfigyelheti, hogy `Main`a-ben `subscriptionKey`deklarálja `endpoint` `route`,, és a átbetűzés `textToTransliterate`szkriptet.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ekkor készen áll a minta alkalmazás futtatására. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

A minta futtatása után a következőnek kell megjelennie a terminálon:

```bash
Transliterated to latn script: Kon\'nichiwa
```

Ez az üzenet a nyers JSON-ből készült, amely így fog kinézni:

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
    }
]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a Translator Text API.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
