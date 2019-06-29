---
title: 'Gyors útmutató: Támogatott nyelvek listája C# – Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a fordítás, átbetűzésű és a Translator Text API használatával szótár keresési támogatott nyelvek listájának lekérése.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 5b5bf33c70b9f8827f1f55378e9caf4d1c858bed
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449423"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-c"></a>Gyors útmutató: Használja a támogatott nyelvek listáját a Translator Text API használatávalC#

Ebben a rövid útmutatóban a fordítás, átbetűzésű és a Translator Text API használatával szótár keresési támogatott nyelvek listájának lekérése.

## <a name="prerequisites"></a>Előfeltételek

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet-csomag](https://www.nuget.org/packages/Newtonsoft.Json/)
* [A Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), vagy kedvenc szövegszerkesztőjével

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

## <a name="set-the-host-name-and-path"></a>Állítsa be a gazdagép nevét és elérési útja

Adja hozzá ezeket a sorokat a `GetLanguages` függvény.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/languages?api-version=3.0";
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
// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;
// Pretty print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Ha egy több szolgáltatást a Cognitive Services-előfizetést használ, akkor is tartalmaznia kell a `Ocp-Apim-Subscription-Region` az a kérelem paramétereit. [További információ a több szolgáltatásos előfizetéshez való hitelesítés közben](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication). 

Nyomtatása válasz "Viszonylag nyomtatási" (a válasz formázás), adja hozzá ezt a funkciót a Program osztályhoz:
```
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
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

Keresse meg az országot/régiót rövidítés a jelen [nyelvek listája](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

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
