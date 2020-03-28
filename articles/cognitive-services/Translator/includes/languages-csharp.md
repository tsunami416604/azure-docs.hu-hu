---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: a7715577936b0e95392f2d561e4b492b20c9dbf5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906948"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>.NET Core projekt létrehozása

Nyisson meg egy új parancssort (vagy terminálmunkamenetet), és futtassa a következő parancsokat:

```console
dotnet new console -o languages-sample
cd languages-sample
```

Az első parancs két dolgot tesz. Létrehoz egy új .NET konzolalkalmazást, és `languages-sample`létrehoz egy könyvtárat. A második parancs a projekt könyvtárára változik.

Ezután telepítenie kell Json.Net. A projekt könyvtárában futtassa a következőket:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Kötelező névterek hozzáadása a projekthez

A `dotnet new console` korábban futtatott parancs létrehozott `Program.cs`egy projektet, beleértve a . Ez a fájl az, ahol akkor tegye az alkalmazás kódját. Nyissa `Program.cs`meg a , és cserélje le a meglévőket utasításokkal. Ezek az utasítások biztosítják, hogy a mintaalkalmazás létrehozásához és futtatásához szükséges összes típushoz hozzáfér.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="get-endpoint-information-from-an-environment-variable"></a>Végpontinformációk bekérése környezeti változóból

Adja hozzá a `Program` következő sorokat az osztályhoz. Ezek a sorok az előfizetési kulcsot és a végpontot a környezeti változókból olvassák, és hibát okoznak, ha bármilyen probléma merül fel.

```csharp
private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
```

## <a name="create-a-function-to-get-a-list-of-languages"></a>Függvény létrehozása a nyelvek listájának leéséhez

Az `Program` osztályban hozzon `GetLanguages`létre egy függvényt, amelynek neve . Ez az osztály magában foglalja a Nyelvek erőforrás hívásához használt kódot, és kinyomtatja az eredményt a konzolra.

```csharp
static void GetLanguages()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-route"></a>Az útvonal beállítása

Adja hozzá ezeket a sorokat a `GetLanguages` függvényhez.

```csharp
string route = "/languages?api-version=3.0";
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
* Kötelező fejlécek hozzáadása
* Aszinkron kérelem kérése
* A válasz megjelenítése

Adja hozzá ezt `HttpRequestMessage`a kódot a következőhöz:

```csharp
// Set the method to GET
request.Method = HttpMethod.Get;
// Construct the full URI
request.RequestUri = new Uri(endpoint + route);
// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;
// Pretty print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Ha egy Cognitive Services többszolgáltatásos előfizetést használ, `Ocp-Apim-Subscription-Region` a kérelem paramétereit is meg kell egyeznie. [További információ a többszolgáltatásos előfizetés hitelesítéséről.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

Ha a választ a "Pretty Print" (a válasz formázása) segítségével szeretné kinyomtatni, adja hozzá ezt a funkciót a Program osztályhoz:

```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Az utolsó lépés `GetLanguages()` a `Main` függvény hívása. Keresse `static void Main(string[] args)` meg és adja hozzá ezeket a sorokat:

```csharp
GetLanguages();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Ez az, készen áll a mintaalkalmazás futtatására. A parancssorból (vagy terminálmunkamenetből) keresse meg a projektkönyvtárat, és futtassa a következőket:

```console
dotnet run
```

## <a name="sample-response"></a>Mintaválasz

Keresse meg az ország/régió rövidítését ebben [a nyelveklistájában.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)

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

Győződjön meg arról, hogy eltávolít minden bizalmas információt a mintaalkalmazás forráskódjából, például az előfizetési kulcsokból.

## <a name="next-steps"></a>További lépések

Tekintse meg az API-hivatkozást, hogy megértse, mit tehet a Translator Text API-val.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
