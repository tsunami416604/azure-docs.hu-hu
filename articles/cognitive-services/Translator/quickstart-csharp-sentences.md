---
title: 'Gyors útmutató: Első mondat visszaadni, C# – Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban fog elsajátíthatja meghatározása a mondat hossza .NET Core és a Translator Text API használatával.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: swmachan
ms.openlocfilehash: 27f515c775d4b1a77563ac10338fc2255e42731c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448185"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-c"></a>Gyors útmutató: A Translator Text API segítségével állapítsa meg, mondat hosszaC#

Ebből a gyorsútmutatóból megtudhatja, hogyan használata a .NET Core, mondat hosszúságok meghatározásához C# 7.1-es vagy újabb, és a Translator Text API.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

* C#7.1-es vagy újabb
* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet-csomag](https://www.nuget.org/packages/Newtonsoft.Json/)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* Egy Azure-előfizetői azonosító a Translator Text szolgáltatáshoz

## <a name="create-a-net-core-project"></a>Egy .NET Core-projekt létrehozása

Nyisson meg egy új parancssort (vagy a terminál-munkamenetben), és futtassa a következő parancsokat:

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

Az első parancs két dolgot eredményez. Egy új .NET-konzolalkalmazást hoz létre, és létrehoz egy könyvtárat nevű `sentences-sample`. A második parancs módosítja a projekt számára a könyvtárba.

Ezt követően kell telepíteni a Json.Net. A projekt könyvtárában futtassa:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Válassza ki a C# nyelvi változatát

Ez a rövid útmutatóhoz C# 7.1-es vagy újabb verziója. Néhány módon módosítsa a C# verzió a projekthez. Ebben az útmutatóban bemutatjuk, hogyan módosíthatja a `sentences-sample.csproj` fájlt. Az összes rendelkezésre álló beállításokat, például a Visual Studióban, a nyelv módosítását lásd: [válassza ki a C# beállított nyelvi verzióhoz](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Nyissa meg a projektet, majd nyissa meg a `sentences-sample.csproj`. Győződjön meg arról, hogy `LangVersion` 7.1-es vagy újabb verziójára van beállítva. Ha nincs a tulajdonságcsoport a beállított nyelvi verzióhoz, adja hozzá ezeket a sorokat:

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

Ezután amikor deszerializálása során a JSON-választ adott vissza a Translator Text API által használt osztály létrehozása fogunk.

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

## <a name="create-a-function-to-determine-sentence-length"></a>Mondat hossz megállapításához függvény létrehozása

Belül a `Program` osztály, hozzon létre egy függvényt, nevű `BreakSentence()`. Ez a függvény négy argumentumot: `subscriptionKey`, `host`, `route`, és `inputText`.

```csharp
static public async Task BreakSentenceRequest(string subscriptionKey, string host, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-break-sentence-request"></a>A szünet mondat kérelem szerializálása

Ezután szüksége hozhat létre, és az JSON-objektumot, amely tartalmazza a szöveget. Tartsa szem előtt, az egynél több objektumot továbbíthatja a `body` tömb.

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
* A válasz megjelenítése

Adja hozzá a kódot a `HttpRequestMessage`:

```csharp
// Build the request.
// Set the method to POST
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
BreakSentenceResult[] deserializedOutput = JsonConvert.DeserializeObject<BreakSentenceResult[]>(result);
foreach (BreakSentenceResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    Console.WriteLine("The first sentence length is: {0}", o.SentLen[0]);
}
```

Ha egy több szolgáltatást a Cognitive Services-előfizetést használ, akkor is tartalmaznia kell a `Ocp-Apim-Subscription-Region` az a kérelem paramétereit. [További információ a több szolgáltatásos előfizetéshez való hitelesítés közben](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication). 

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés az, hogy a hívás `BreakSentenceRequest()` a a `Main` függvény. Keresse meg `static void Main(string[] args)` és cserélje le ezt a kódot:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/breaksentence?api-version=3.0";
    // Feel free to use any string.
    string breakSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await BreakSentenceRequest(subscriptionKey, host, route, breakSentenceText);
}
```

Láthatja, hogy a `Main`, kijelenti, hogy `subscriptionKey`, `host`, `route`, és kiértékelheti, hogy a szöveg `breakSentenceText`.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ennyi az egész, készen áll a mintaalkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

A minta futtatása után kell megjelennie a terminálban nyomtatott következő:

```bash
The detected language is \'en\'. Confidence is: 1.
The first sentence length is: 25
```

Ez az üzenet, amely fog kinézni a nyers JSON-ból épül:

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

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

A GitHubon megismerheti a rövid útmutató és egyebek mintakódját, beleértve az átírást és a nyelvfelismerést is, valamint más Translator Text-projekteket.

> [!div class="nextstepaction"]
> [A C#-példák megismerése a GitHubon](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Lásd még

* [Szöveg lefordítása](quickstart-csharp-translate.md)
* [Szöveg átírása](quickstart-csharp-transliterate.md)
* [A beviteli nyelv azonosítása](quickstart-csharp-detect.md)
* [Alternatív fordítások beolvasása](quickstart-csharp-dictionary.md)
* [A támogatott nyelvek listájának beolvasása](quickstart-csharp-languages.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-csharp-sentences.md)
