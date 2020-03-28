---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 05355ad37183d4c14cb8f6598141292ded0386d9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906962"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Go projektet kedvenc IDE vagy szerkesztő vagy új mappával az asztalon. Ezután másolja ezt a kódrészletet a projektbe/mappába egy fájlban, amelynek neve `get-languages.go`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>A fő funkció létrehozása

Hozzuk létre az alkalmazásunk fő funkcióját. Észre fogod venni, hogy ez egy sor nyikód. Ennek az az oka, hogy egyetlen függvényt hozunk létre a translator text támogatott nyelveinek listájának be- és nyomtatásához.

Ez a minta megpróbálja olvasni a fordító szövegvégpontját egy környezeti változóból: `TRANSLATOR_TEXT_ENDPOINT`. Ha még nem ismeri a környezeti változókat, beállíthatja a `endpoint` sztringet, és megjegyzéssé teheti a feltételes utasítást.

Másolja a projektbe a következő kódot:

```go
func main() {
    if "" == os.Getenv("TRANSLATOR_TEXT_ENDPOINT") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_ENDPOINT.")
    }
    endpoint := os.Getenv("TRANSLATOR_TEXT_ENDPOINT")
    uri := endpoint + "/languages?api-version=3.0"
    getLanguages(uri)
}
```

## <a name="create-a-function-to-get-a-list-of-supported-languages"></a>A támogatott nyelvek listájának leéséhez hozzon létre egy függvényt

Hozzunk létre egy függvényt a támogatott nyelvek listájának bekerüléséhez.

```go
func getLanguages(uri string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Ezután készítsük el az URL-címet. Az URL-cím `Parse()` a `Query()` és a módszerek használatával épül fel.

Másolja ezt a `getLanguages` kódot a függvénybe.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
u.RawQuery = q.Encode()
```

>[!NOTE]
> A végpontokkal, az útvonalakkal és a kérelem-paraméterekkel kapcsolatos további információért lásd a [Translator Text API 3.0 által támogatott nyelvek lekérését](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) ismertető témakört.

## <a name="build-the-request"></a>A kérelem felépítése

Most, hogy jsonként kódolta a kérelemtörzset, létrehozhatja a POST-kérelmet, és meghívhatja a Translator Text API-t.

```go
// Build the HTTP GET request
req, err := http.NewRequest("GET", u.String(), nil)
if err != nil {
    log.Fatal(err)
}
// Add required headers
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Ha egy Cognitive Services többszolgáltatásos előfizetést használ, `Ocp-Apim-Subscription-Region` a kérelem paramétereit is meg kell egyeznie. [További információ a többszolgáltatásos előfizetés hitelesítéséről.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="handle-and-print-the-response"></a>A válasz leírója és nyomtatása

Adja hozzá ezt `getLanguages` a kódot a függvényhez a JSON-válasz dekódolásához, majd formázza és nyomtassa ki az eredményt.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>Az alkalmazás összeállítása

Ezzel összeállított egy egyszerű programot, amely meghívja a Translator Text API-t, és visszaad egy JSON-választ. Most itt az ideje, hogy futtassa a programot:

```console
go run get-languages.go
```

Ha szeretné összevetni a saját kódját a miénkkel, a teljes mintakódot megtekintheti a [GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Mintaválasz

Keresse meg az ország/régió rövidítését ebben [a nyelveklistájában.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
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
  },
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
  }
}
```

## <a name="next-steps"></a>További lépések

Tekintse meg az API-hivatkozást, hogy megértse, mit tehet a Translator Text API-val.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
