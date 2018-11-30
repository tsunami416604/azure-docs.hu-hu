---
title: 'Rövid útmutató: Támogatott nyelvek lekérése, C# – Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a fordítás, átbetűzésű és a Translator Text API használatával szótár keresési támogatott nyelvek listájának lekérése.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: erhopf
ms.openlocfilehash: cc5d9efd017ec2045cc94bbad98e26e8b95e071d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334686"
---
# <a name="quickstart-get-supported-languages-with-the-translator-text-rest-api-c"></a>Rövid útmutató: Támogatott nyelvek lekérése a Translator Text REST API használatával (C#)

Ebben a rövid útmutatóban a fordítás, átbetűzésű és a Translator Text API használatával szótár keresési támogatott nyelvek listájának lekérése.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet-csomag](https://www.nuget.org/packages/Newtonsoft.Json/)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével
* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcsa

## <a name="create-a-net-core-project"></a>Egy .NET Core-projekt létrehozása

Nyisson meg egy új parancssort (vagy a terminál-munkamenetben), és futtassa a következő parancsokat:

```console
dotnet new console -o languages-sample
cd languages-sample
```

Az első parancs két dolgot eredményez. Egy új .NET-konzolalkalmazást hoz létre, és létrehoz egy könyvtárat nevű `languages-sample`. A második parancs módosítja a projekt számára a könyvtárba.

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

## <a name="create-a-function-to-get-a-list-of-languages"></a>Hozzon létre egy függvényt, amely nyelvek listája

Belül a `Program` osztály, hozzon létre egy függvényt, nevű `GetLanguages`. Ez az osztály magában foglalja a nyelvek erőforrás meghívásához használt kódot, és kinyomtatja az eredményt a konzolon.

```csharp
static void GetLanguages()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Az előfizetési kulcs, állomás neve és elérési útja

Adja hozzá ezeket a sorokat a `GetLanguages` függvény.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/languages?api-version=3.0";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
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
* Adja hozzá a szükséges fejlécek
* Egy aszinkron kérés
* A válasz megjelenítése

Adja hozzá a kódot a `HttpRequestMessage`:

```csharp
// Set the method to GET
request.Method = HttpMethod.Get;

// Construct the full URI
request.RequestUri = new Uri(host + route);

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

Az utolsó lépés az, hogy a hívás `GetLanguages()` a a `Main` függvény. Keresse meg `static void Main(string[] args)` , és adja hozzá ezeket a sorokat:

```csharp
GetLanguages();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ennyi az egész, készen áll a mintaalkalmazás futtatásához. A parancssor (vagy a terminál-munkamenetben) lépjen a projektkönyvtárba, majd futtassa:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
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
* [Bemenet mondatai hosszának meghatározása](quickstart-csharp-sentences.md)
