---
title: 'Gyors útmutató: Szöveg lefordítása C# -fordítói szöveg'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban szöveget fordít le egy nyelvről egy másikra a Translator Text API és C# segítségével.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: swmachan
ms.openlocfilehash: 5b5451fc1c4b8127c4e2a561e25fe3eb730354a8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705649"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-using-c"></a>Gyors útmutató: Egy karakterlánc segítségével lefordítani a Translator Text API használatávalC#

Ebből a gyorsútmutatóból megtudhatja, hogyan egy szöveges karakterlánc angol, német, olasz, japán és használata a .NET Core, Thai lefordítani C# 7.1-es vagy újabb, és a Translator Text REST API.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

>[!TIP]
> Ha szeretné egyszerre az összes kód megtekintéséhez, ehhez a mintához forráskódja elérhető a [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp).

## <a name="prerequisites"></a>Előfeltételek

* C#7.1-es vagy újabb
* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet-csomag](https://www.nuget.org/packages/Newtonsoft.Json/)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* Egy Azure-előfizetői azonosító a Translator Text szolgáltatáshoz

## <a name="create-a-net-core-project"></a>Egy .NET Core-projekt létrehozása

Nyisson meg egy új parancssort (vagy a terminál-munkamenetben), és futtassa a következő parancsokat:

```console
dotnet new console -o translate-sample
cd translate-sample
```

Az első parancs két dolgot eredményez. Egy új .NET-konzolalkalmazást hoz létre, és létrehoz egy könyvtárat nevű `translate-sample`. A második parancs módosítja a projekt számára a könyvtárba.

Ezt követően kell telepíteni a Json.Net. A projekt könyvtárában futtassa:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Válassza ki a C# nyelvi változatát

Ez a rövid útmutatóhoz C# 7.1-es vagy újabb verziója. Néhány módon módosítsa a C# verzió a projekthez. Ebben az útmutatóban bemutatjuk, hogyan módosíthatja a `translate-sample.csproj` fájlt. Az összes rendelkezésre álló beállításokat, például a Visual Studióban, a nyelv módosítását lásd: [válassza ki a C# beállított nyelvi verzióhoz](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Nyissa meg a projektet, majd nyissa meg a `translate-sample.csproj`. Győződjön meg arról, hogy `LangVersion` 7.1-es vagy újabb verziójára van beállítva. Ha nincs a tulajdonságcsoport a beállított nyelvi verzióhoz, adja hozzá ezeket a sorokat:

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

## <a name="create-classes-for-the-json-response"></a>A JSON-válasz osztályok létrehozása

Ezután hozunk létre egy csoportot, ha deszerializálása során a JSON-választ adott vissza a Translator Text API által használt osztályok.

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

## <a name="create-a-function-to-translate-text"></a>Szöveg lefordítása a függvény létrehozása

Belül a `Program` osztály, hozzon létre egy aszinkron a hívott függvény `TranslateTextRequest()`. Ez a függvény négy argumentumot: `subscriptionKey`, `host`, `route`, és `inputText`.

```csharp
// This sample requires C# 7.1 or later for async/await.
// Async call to the Translator Text API
static public async Task TranslateTextRequest(string subscriptionKey, string host, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>A fordítási kérelem szerializálása

Következő lépésként hozzon létre, és az JSON-objektumot, amely tartalmazza a fordítandó szöveg. Tartsa szem előtt, az egynél több objektumot továbbíthatja a `body`.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Hozza létre az ügyfél és a egy kérés

Ezek a sorok hozza létre a `HttpClient` és a `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>A kérelem létrehozása és a válasz

Belül a `HttpRequestMessage` jelennek meg:

* Deklarálja a HTTP-metódus
* Hozza létre a kérés URI azonosítója
* A kérelem törzsében (szerializált JSON-objektum) beszúrása
* Adja hozzá a szükséges fejlécek
* Egy aszinkron kérés
* A korábban létrehozott-e az osztályokkal válasz

Adja hozzá a kódot a `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to Post.
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(host + route);
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

Ha egy több szolgáltatást a Cognitive Services-előfizetést használ, akkor is tartalmaznia kell a `Ocp-Apim-Subscription-Region` az a kérelem paramétereit. [További információ a több szolgáltatásos előfizetéshez való hitelesítés közben](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés az, hogy a hívás `TranslateTextRequest()` a a `Main` függvény. Ebben a példában azt Ön fordítása a német (`de`), olasz (`it`), japán (`ja`), és Thai (`th`). Keresse meg `static void Main(string[] args)` és cserélje le ezt a kódot:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/translate?api-version=3.0&to=de&to=it&to=ja&to=th";
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    // Prompts you for text to translate. If you'd prefer, you can
    // provide a string as textToTranslate.
    Console.Write("Type the phrase you'd like to translate? ");
    string textToTranslate = Console.ReadLine();
    await TranslateTextRequest(subscriptionKey, host, route, textToTranslate);
}
```

Láthatja, hogy a `Main`, kijelenti, hogy `subscriptionKey`, `host`, és `route`. Ezenkívül, hogy kéri a felhasználót a bemeneti `Console.Readline()` és hozzárendelését az értéket `textToTranslate`.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ennyi az egész, készen áll a mintaalkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

A minta futtatása után kell megjelennie a terminálban nyomtatott következő:

```bash
Detected input language: en
Confidence score: 1

Translated to de: Hallo Welt!
Translated to it: Salve, mondo!
Translated to ja: ハローワールド！
Translated to th: หวัดดีชาวโลก!
```

Ez az üzenet, amely fog kinézni a nyers JSON-ból épül:

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

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

Vessen egy pillantást az API-referencia az elvégezhető műveletek a Translator Text API ismertetése.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Lásd még

* [Szöveg átírása](quickstart-csharp-transliterate.md)
* [A beviteli nyelv azonosítása](quickstart-csharp-detect.md)
* [Alternatív fordítások beolvasása](quickstart-csharp-dictionary.md)
* [A támogatott nyelvek listájának beolvasása](quickstart-csharp-languages.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-csharp-sentences.md)
