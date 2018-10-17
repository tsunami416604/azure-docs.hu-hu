---
title: 'Rövid útmutató: Szöveget konvertáló parancsfájl – Translator Text, Go'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban szöveget alakít át egy nyelven egy szkriptből egy másikba a Translator Text API és Go segítségével.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: c4930e03f5d366eb3f21b44c5be90c20230065c3
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126751"
---
# <a name="quickstart-transliterate-text-with-go"></a>Rövid útmutató: Szöveg átírása a Go használatával

Ebben a rövid útmutatóban szöveget alakít át egy nyelven egy szkriptből egy másikba a Translator Text API segítségével.

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához telepítenie kell a [Go disztribúciót](https://golang.org/doc/install). A mintakód csak **alapvető** kódtárakat használ, így nincsenek külső függőségek.

A Translator Text API használatához szüksége van egy előfizetési kulcsra is. Lásd [a Translator Text API regisztrációját](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Transliterate kérés

A következő egy szöveget alakít át egy nyelven egy szkriptből egy másik szkriptbe a [Transliterate](./reference/v3-0-transliterate.md) metódussal.

1. Hozzon létre egy új Go-projektet a kedvenc kódszerkesztőjében.
2. Adja hozzá az alábbi kódot.
3. A `subscriptionKey` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Mentse a fájlt „.go” kiterjesztéssel.
5. Nyisson meg parancssort egy számítógépen, amelyen a Go telepítve van.
6. Állítsa össze a fájlt (például: „go build quickstart-transliterate.go”).
7. Futtassa a fájlt (például: „quickstart-transliterate”).

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
    "strings"
    "time"
)

func main() {
    // Replace the subscriptionKey string value with your valid subscription key
    const subscriptionKey = "<Subscription Key>"

    const uriBase = "https://api.cognitive.microsofttranslator.com"
    const uriPath = "/transliterate?api-version=3.0"

    // Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script
    const params = "&language=ja&fromScript=jpan&toScript=latn"

    const uri = uriBase + uriPath + params

    // Transliterate "good afternoon".
    const text = "こんにちは"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\"}]")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.FormatInt(req.ContentLength, 10))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="transliterate-response"></a>Transliterate válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
[
  {
    "script": "latn",
    "text": "konnnichiha"
  }
]
```

## <a name="next-steps"></a>További lépések

A Cognitive Services API-k Go-csomagjait a GitHubon ismerheti meg, a [Góhoz készült Azure SDK-val](https://github.com/Azure/azure-sdk-for-go) kapcsolatos témakörben.

> [!div class="nextstepaction"]
> [A Go-csomagok megismerése a GitHubon](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
