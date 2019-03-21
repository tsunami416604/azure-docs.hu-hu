---
title: 'Gyors útmutató: Szöveg átalakítása parancsfájl, a Go – Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban szöveget alakít át egy nyelven egy szkriptből egy másikba a Translator Text API és Go segítségével.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: fe8b1807ea08529ad6be11493de659bd32f0a129
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57858218"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-go"></a>Gyors útmutató: A Translator Text API használatával átbetűzés szöveget a Go használatával

Ebben a rövid útmutatóban szöveget alakít át egy nyelven egy szkriptből egy másikba a Translator Text API segítségével.

Ehhez a rövid útmutatóhoz szükség van egy [Azure Cognitive Services-fiókra](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account), amely tartalmaz egy Translator Text-erőforrást. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* [Go](https://golang.org/doc/install)
* Egy Azure-előfizetői azonosító a Translator Text szolgáltatáshoz

## <a name="create-a-project-and-import-required-modules"></a>Projekt létrehozása és a szükséges modulok importálása

A kedvenc integrált Fejlesztőkörnyezetével vagy szerkesztőjével használatával új Go-projekt létrehozása. Ezután másolja a következő kódrészletet egy `transliterate-text.go` nevű fájlba a projektjében.

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
     * This calls our transliterate function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    transliterate(subscriptionKey)
}
```

## <a name="create-a-function-to-transliterate-text"></a>Hozzon létre egy függvényt, amely szöveges átírás

Hozzunk létre egy függvényt, hogy átírás szöveget. Ez a funkció a Translator Text előfizetési kulcs egyetlen argumentumot vesz igénybe.

```go
func transliterate(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Következő lépésként hozzunk létre az URL-címet. Az URL-címe használatával lett összeállítva a `Parse()` és `Query()` módszereket. Láthatja, hogy a hozzáadott paraméter a `Add()` metódust. Ebben a példában egy japán nyelvű szövegből hozunk létre latin betűs átiratot.

Másolja be ezt a kódot a `transliterate` függvény.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0")
q := u.Query()
q.Add("language", "ja")
q.Add("fromScript", "jpan")
q.Add("toScript", "latn")
u.RawQuery = q.Encode()
```

>[!NOTE]
> Végpontok, útvonalak és a kérelem paramétereinek kapcsolatos további információkért lásd: [Translator Text API 3.0: Átbetűzés](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

## <a name="create-a-struct-for-your-request-body"></a>Egy struct a kérés törzsének létrehozása

Ezután hozzon létre egy névtelen struktúráját a kérelem törzsében, és a JSON-ként kódolni, `json.Marshal()`. Adja hozzá a kódot a `transliterate` függvény.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "こんにちは"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>A kérelem létrehozása

Most, hogy a kéréstörzs JSON-fájlként már kódolva, a POST-kérés hozhat létre, és a Translator Text API hívása.

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

## <a name="handle-and-print-the-response"></a>Kezelni, és a válasz

Adja hozzá a kódot a `transliterate` függvény dekódolni a JSON-válasz, majd formázza és nyomtassa ki az eredményt.

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
go run transliterate-text.go
```

Ha szeretné összevetni a saját kódját a miénkkel, a teljes mintakódot megtekintheti a [GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## <a name="sample-response"></a>Mintaválasz

```json
[
  {
    "script": "latn",
    "text": "konnichiwa"
  }
]
```

## <a name="next-steps"></a>További lépések

A Cognitive Services API-k Go-csomagjait a GitHubon ismerheti meg, a [Góhoz készült Azure SDK-val](https://github.com/Azure/azure-sdk-for-go) kapcsolatos témakörben.

> [!div class="nextstepaction"]
> [A Go-csomagok megismerése a GitHubon](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>Lásd még

Ismerje meg, hogyan használható a Translator Text API-t:

* [Szöveg lefordítása](quickstart-go-translate.md)
* [A beviteli nyelv azonosítása](quickstart-go-detect.md)
* [Alternatív fordítások beolvasása](quickstart-go-dictionary.md)
* [A támogatott nyelvek listájának beolvasása](quickstart-go-languages.md)
* [Bemenet mondatai hosszának meghatározása](quickstart-go-sentences.md)
