---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 9aecaa6195509ec4c1f0d6b4b14b9bb30817da34
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906849"
---
[!INCLUDE [Prerequisites](prerequisites-go.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új Go projektet kedvenc IDE vagy szerkesztő használatával. Ezután másolja a következő kódrészletet egy `sentence-length.go` nevű fájlba a projektjében.

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

## <a name="create-the-main-function"></a>A fő funkció létrehozása

Ez a minta megpróbálja elolvasni a Translator Text előfizetési `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` `TRANSLATOR_TEXT_ENDPOINT`kulcsot és végpontot az alábbi környezeti változókból: és . Ha nem ismeri a környezeti változókat, `subscriptionKey` `endpoint` beállíthatja, karakterláncként, és megjegyzést fűzhet a feltételes utasításokhoz.

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
    uri := endpoint + "/breaksentence?api-version=3.0"
    /*
     * This calls our breakSentence function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    breakSentence(subscriptionKey, uri)
}
```

## <a name="create-a-function-to-determine-sentence-length"></a>A mondat hosszának meghatározásához hozzon létre egy függvényt

Hozzunk létre egy függvényt a mondat hosszának meghatározására. Ez a függvény egyetlen argumentumot, a Translator Text előfizetési kulcsot vesz igénybe.

```go
func breakSentence(subscriptionKey string, uri string)
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Ezután készítsük el az URL-címet. Az URL-cím `Parse()` a `Query()` és a módszerek használatával épül fel. Észre fogja venni, hogy a `Add()` paraméterek hozzá a módszerrel.

Másolja ezt a `breakSentence` kódot a függvénybe.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse(uri)
q := u.Query()
q.Add("languages", "en")
u.RawQuery = q.Encode()
```

>[!NOTE]
> A végpontokról, az útvonalakról és a kérelemparaméterekről a [Translator Text API 3.0: BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence)című témakörben talál további információt.

## <a name="create-a-struct-for-your-request-body"></a>Struct létrehozása a kérelem törzséhez

Ezután hozzon létre egy névtelen struktúrát a kérelemtörzsszámára, és kódolja JSON-ként a segítségével. `json.Marshal()` Adja hozzá ezt `breakSentence` a kódot a függvényhez.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "How are you? I am fine. What did you do today?"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>A kérelem felépítése

Most, hogy jsonként kódolta a kérelemtörzset, létrehozhatja a POST-kérelmet, és meghívhatja a Translator Text API-t.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

Ha egy Cognitive Services többszolgáltatásos előfizetést használ, `Ocp-Apim-Subscription-Region` a kérelem paramétereit is meg kell egyeznie. [További információ a többszolgáltatásos előfizetés hitelesítéséről.](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)

## <a name="handle-and-print-the-response"></a>A válasz leírója és nyomtatása

Adja hozzá ezt `breakSentence` a kódot a függvényhez a JSON-válasz dekódolásához, majd formázza és nyomtassa ki az eredményt.

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
go run sentence-length.go
```

Ha szeretné összevetni a saját kódját a miénkkel, a teljes mintakódot megtekintheti a [GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Mintaválasz

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>További lépések

Tekintse meg az API-hivatkozást, hogy megértse, mit tehet a Translator Text API-val.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
