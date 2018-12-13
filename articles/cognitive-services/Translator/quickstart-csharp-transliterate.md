---
title: 'Rövid útmutató: Szövegszkript átalakítása, C# – Translator Text'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban fog elsajátíthatja átírás (átalakítás) szöveg egy parancsfájlból származó .NET Core és a Translator Text REST API egy másik használatával. Ebben a példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 11/21/2018
ms.author: erhopf
ms.openlocfilehash: d67fbdad32612f23b1de31f7d2a1d7b4dee3b68e
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52889140"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-c"></a>Gyors útmutató: A Translator Text API használatával átbetűzés szöveg használatávalC#

Ebből a gyorsútmutatóból megtudhatja, hogyan átírás (átalakítás) szöveg egy parancsfájlból származó .NET Core használatával való (C#) és a Translator Text REST API. A megadott példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet-csomag](https://www.nuget.org/packages/Newtonsoft.Json/)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* Egy Azure-előfizetői azonosító a Translator Text szolgáltatáshoz

## <a name="create-a-net-core-project"></a>Egy .NET Core-projekt létrehozása

Nyisson meg egy új parancssort (vagy a terminál-munkamenetben), és futtassa a következő parancsokat:

```console
dotnet new console -o transliterate-sample
cd transliterate-sample
```

Az első parancs két dolgot eredményez. Egy új .NET-konzolalkalmazást hoz létre, és létrehoz egy könyvtárat nevű `transliterate-sample`. A második parancs módosítja a projekt számára a könyvtárba.

Ezt követően kell telepíteni a Json.Net. A projekt könyvtárában futtassa:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Adja hozzá a projekthez szükséges névterek

A `dotnet new console` parancsot, amely futtatta korábban létrehozott egy projektet, beleértve a `Program.cs`. Ez a fájl meg, ahová az alkalmazás kódjában fog. Nyissa meg `Program.cs`, és cserélje le a meglévő using utasítások. Ezek az utasítások győződjön meg arról, hogy a minta-alkalmazás létrehozásához és futtatásához szükséges összes típusú hozzáférést.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-transliterate-text"></a>Hozzon létre egy függvényt, amely szöveges átírás

Belül a `Program` osztály, hozzon létre egy függvényt, nevű `TransliterateText`. Ez az osztály magában foglalja a Transliterate erőforrás meghívásához használt kódot, és kinyomtatja az eredményt a konzolon.

```csharp
static void TransliterateText()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Az előfizetési kulcs, állomás neve és elérési útja

Adja hozzá ezeket a sorokat a `TransliterateText` függvény. Láthatja, hogy az a `api-version`, két további paraméterek hozzáfűzött lett a `route`. Ezek a paraméterek segítségével állítsa be a beviteli nyelv és a parancsfájlok átbetűzésű. Ebben a példában japán nyelvre van beállítva (`jpan`) és a Latin (`latn`). Ellenőrizze, hogy frissíti az előfizetési kulcs értékét.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

Következő lépésként hozhat létre, és az JSON-objektumot, amely tartalmazza a kívánt átbetűzés szöveget. Tartsa szem előtt, az egynél több objektumot továbbíthatja a `body` tömb.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"こんにちは" } };
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
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(jsonResponse);
Console.WriteLine("Press any key to continue.");
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés az, hogy a hívás `TransliterateText()` a a `Main` függvény. Keresse meg `static void Main(string[] args)` , és adja hozzá ezeket a sorokat:

```csharp
TransliterateText();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ennyi az egész, készen áll a mintaalkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

```json
[
    {
        "script": "latn",
        "text": "konnnichiha"
    }
]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ellenőrizze, hogy a bizalmas adatok eltávolítása a mintaalkalmazás forráskódja, például a előfizetési kulcsok.

## <a name="next-steps"></a>További lépések

A GitHubon megismerheti a rövid útmutató és egyebek mintakódját, beleértve a fordítást és a nyelvfelismerést is, valamint más Translator Text-projekteket.

> [!div class="nextstepaction"]
> [A C#-példák megismerése a GitHubon](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Lásd még

* [Szöveg lefordítása](quickstart-csharp-translate.md)
* [A beviteli nyelv azonosítása](quickstart-csharp-detect.md)
* [Alternatív fordítások beolvasása](quickstart-csharp-dictionary.md)
* [A támogatott nyelvek listájának beolvasása](quickstart-csharp-languages.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-csharp-sentences.md)
