---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 1802a8d68030dbc882b4687cae28668f5a1e7a29
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587019"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új go-projektet a kedvenc IDE/szerkesztőjével vagy az asztalon lévő új mappával. Ezután másolja ezt a kódrészletet a projektbe vagy mappájába egy nevű fájlba `dictionaryLookup.go` .

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>A fő függvény létrehozása

Ez a minta megkísérli beolvasni a fordítói előfizetési kulcsot és a végpontot ezekből a környezeti változókból: `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` és `TRANSLATOR_TEXT_ENDPOINT` . Ha nem ismeri a környezeti változókat, beállíthatja és karakterláncként is megadhatja `subscriptionKey` `endpoint` a feltételes utasításokat, és megjegyzéseket fűzhet hozzájuk.

Másolja a projektbe a következő kódot:

```go
func main() {
    /*
    * Read your subscription key from an env variable.
    * Please note: You can replace this code block with
    * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
    * want to use env variables. If so, be sure to delete the "os" import.
    */
    if "" == os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_SUBSCRIPTION_KEY.")
    }
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY")
    if "" == os.Getenv("TRANSLATOR_TEXT_ENDPOINT") {
      log.Fatal("Please set/export the environment variable TRANSLATOR_TEXT_ENDPOINT.")
    }
    endpoint := os.Getenv("TRANSLATOR_TEXT_ENDPOINT")
    uri := endpoint + "/dictionary/lookup?api-version=3.0"
    /*
     * This calls our breakSentence function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    dictionaryLookup(subscriptionKey, uri)
}
```

## <a name="create-a-function-to-get-alternate-translations"></a>Függvény létrehozása alternatív fordítások beszerzéséhez

Hozzon létre egy függvényt az alternatív fordítások beszerzéséhez. Ez a függvény egyetlen argumentumot, a fordító előfizetési kulcsát fogja végrehajtani.

```go
func dictionaryLookup(subscriptionKey string, uri string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Ezután hozzon létre egy URL-címet. Az URL-cím a `Parse()` és a `Query()` metódusok használatával készült. Figyelje meg, hogy a paramétereket a metódussal együtt adja hozzá a rendszer `Add()` . Ebben a példában angolról spanyolra vagyunk lefordítva.

Másolja ezt a kódot a `altTranslations` függvénybe.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
q.Add("from", "en")
q.Add("to", "es")
u.RawQuery = q.Encode()
```

>[!NOTE]
> További információ a végpontokról, az útvonalakról és a kérelmek paraméteréről: [Translator 3,0: szótár-keresés](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup).

## <a name="create-a-struct-for-your-request-body"></a>Struct létrehozása a kérés törzséhez

Ezután hozzon létre egy névtelen struktúrát a kérelem törzse számára, és kódolja JSON-ként a következővel: `json.Marshal()` . Adja hozzá ezt a kódot a `altTranslations` függvényhez.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Pineapples"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>A kérelem felépítése

Most, hogy már kódolta a kérés törzsét JSON-ként, felépítheti a POST-kérelmét, és meghívhatja a fordítót.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek is szerepelnie kell `Ocp-Apim-Subscription-Region` . [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>A válasz kezelése és nyomtatása

Adja hozzá ezt a kódot a `altTranslations` függvényhez a JSON-válasz dekódolásához, majd formázza és nyomtassa ki az eredményt.

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

Ez egy egyszerű program, amely meghívja a fordítót, és egy JSON-választ ad vissza. Most itt az ideje, hogy futtassa a programot:

```console
go run dictionaryLookup.go
```

Ha szeretné összevetni a saját kódját a miénkkel, a teljes mintakódot megtekintheti a [GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Mintaválasz

```json
[
  {
    "displaySource": "pineapples",
    "normalizedSource": "pineapples",
    "translations": [
      {
        "backTranslations": [
          {
            "displayText": "pineapples",
            "frequencyCount": 158,
            "normalizedText": "pineapples",
            "numExamples": 5
          },
          {
            "displayText": "cones",
            "frequencyCount": 13,
            "normalizedText": "cones",
            "numExamples": 5
          },
          {
            "displayText": "piña",
            "frequencyCount": 5,
            "normalizedText": "piña",
            "numExamples": 3
          },
          {
            "displayText": "ganks",
            "frequencyCount": 3,
            "normalizedText": "ganks",
            "numExamples": 2
          }
        ],
        "confidence": 0.7016,
        "displayTarget": "piñas",
        "normalizedTarget": "piñas",
        "posTag": "NOUN",
        "prefixWord": ""
      },
      {
        "backTranslations": [
          {
            "displayText": "pineapples",
            "frequencyCount": 16,
            "normalizedText": "pineapples",
            "numExamples": 2
          }
        ],
        "confidence": 0.2984,
        "displayTarget": "ananás",
        "normalizedTarget": "ananás",
        "posTag": "NOUN",
        "prefixWord": ""
      }
    ]
  }
]
```

## <a name="next-steps"></a>További lépések

Tekintse meg az API-referenciát, amely mindent megtudhat a fordítóval.

> [!div class="nextstepaction"]
> [API-referenciák](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
