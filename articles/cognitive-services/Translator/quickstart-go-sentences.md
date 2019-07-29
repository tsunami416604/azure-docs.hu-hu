---
title: 'Gyors útmutató: Mondat hosszának beolvasása, go-Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban kideríti egy szövegben a mondatok hosszát a Translator Text API és a Go segítségével.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: a71e0859d7d677865cbacbf24de8b0e17861a192
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595177"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-go"></a>Gyors útmutató: A Translator Text API használata a mondat hosszának meghatározásához a go használatával

Ebből a rövid útmutatóból megtudhatja, hogyan határozhatja meg a mondatok hosszát (karakterekben) a Go és a Translator Text REST API használatával.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

>[!TIP]
> Ha az összes kódot egyszerre szeretné látni, a minta forráskódja elérhető a [githubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Go](https://golang.org/doc/install)
* Egy Azure-előfizetői azonosító a Translator Text szolgáltatáshoz

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

Hozzon létre egy új go-projektet a kedvenc IDE vagy szerkesztő használatával. Ezután másolja a következő kódrészletet egy `sentence-length.go` nevű fájlba a projektjében.

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

Ez a minta megpróbálja beolvasni a Translator Text-előfizetői azonosítót a `TRANSLATOR_TEXT_KEY` környezeti változóból. Ha még nem ismeri a környezeti változókat, beállíthatja a `subscriptionKey` sztringet, és megjegyzéssé teheti a feltételes utasítást.

Másolja a projektbe a következő kódot:

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables. If so, be sure to delete the "os" import.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our breakSentence function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    breakSentence(subscriptionKey)
}
```

## <a name="create-a-function-to-determine-sentence-length"></a>Függvény létrehozása a mondat hosszának meghatározásához

Hozzunk létre egy függvényt a mondat hosszának meghatározásához. Ez a függvény egyetlen argumentumot, a Translator Text előfizetési kulcsot fogja megtenni.

```go
func breakSentence(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Ezután hozzon létre egy URL-címet. Az URL-cím a és `Parse()` `Query()` a metódusok használatával készült. Figyelje meg, hogy a paramétereket a metódussal `Add()` együtt adja hozzá a rendszer.

Másolja ezt a kódot a `breakSentence` függvénybe.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0")
q := u.Query()
q.Add("languages", "en")
u.RawQuery = q.Encode()
```

>[!NOTE]
> További információ a végpontokról, az útvonalakról és a kérelmek paraméteréről [: Translator Text API 3,0: BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence).

## <a name="create-a-struct-for-your-request-body"></a>Struct létrehozása a kérés törzséhez

Ezután hozzon létre egy névtelen struktúrát a kérelem törzse számára, és kódolja `json.Marshal()`JSON-ként a következővel:. Adja hozzá ezt a kódot `breakSentence` a függvényhez.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "How are you? I am fine. What did you do today?"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>A kérelem létrehozása

Most, hogy kódolta a kérés törzsét JSON-ként, felépítheti a POST-kérést, és meghívhatja a Translator Text API.

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

Ha Cognitive Services több szolgáltatásra kiterjedő előfizetést használ, akkor a kérés paramétereinek `Ocp-Apim-Subscription-Region` is szerepelnie kell. [További információ a többszolgáltatásos előfizetés hitelesítéséről](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="handle-and-print-the-response"></a>A válasz kezelése és nyomtatása

Adja hozzá ezt a kódot `breakSentence` a függvényhez a JSON-válasz dekódolásához, majd formázza és nyomtassa ki az eredményt.

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

Tekintse meg az API-referenciát, amely mindent megtudhat a Translator Text API.

> [!div class="nextstepaction"]
> [API-leírások](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Lásd még

Ismerje meg, hogyan használhatja a Translator Text API a következőhöz:

* [Szöveg lefordítása](quickstart-go-translate.md)
* [Szöveg átírása](quickstart-go-transliterate.md)
* [A beviteli nyelv azonosítása](quickstart-go-detect.md)
* [Alternatív fordítások beolvasása](quickstart-go-dictionary.md)
* [A támogatott nyelvek listájának beolvasása](quickstart-go-languages.md)
