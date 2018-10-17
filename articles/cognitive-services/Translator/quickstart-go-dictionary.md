---
title: 'Rövid útmutató: Alternatív fordítások lekérése – Translator Text, Go'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a kifejezések alternatív fordításait és példáit keresi meg a szövegkörnyezetben a Translator Text API segítségével és Go használatával.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: 4ea21673c993c6dd9478f42bfee790b62317946f
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128876"
---
# <a name="quickstart-find-alternate-translations-and-usage-with-go"></a>Rövid útmutató: Alternatív fordítások és használat lekérése a Go használatával

Ebben a rövid útmutatóban a kifejezések lehetséges alternatív fordításaival kapcsolatos részleteket, valamint az alternatív fordítások használatának példáit keresi meg a Translator Text API segítségével.

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához telepítenie kell a [Go disztribúciót](https://golang.org/doc/install). A mintakód csak **alapvető** kódtárakat használ, így nincsenek külső függőségek.

A Translator Text API használatához szüksége van egy előfizetési kulcsra is. Lásd [a Translator Text API regisztrációját](translator-text-how-to-signup.md).

## <a name="dictionary-lookup-request"></a>Dictionary Lookup kérés

A következő egy szó alternatív fordításait kéri le a [Dictionary Lookup](./reference/v3-0-dictionary-lookup.md) metódussal.

1. Hozzon létre egy új Go-projektet a kedvenc kódszerkesztőjében.
2. Adja hozzá az alábbi kódot.
3. A `subscriptionKey` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Mentse a fájlt „.go” kiterjesztéssel.
5. Nyisson meg parancssort egy számítógépen, amelyen a Go telepítve van.
6. Állítsa össze a fájlt (például: „go build quickstart-lookup.go”).
7. Futtassa a fájlt (például: „quickstart-lookup”).

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
    const uriPath = "/dictionary/lookup?api-version=3.0"

    // From English to French
    const params = "&from=en&to=fr"

    const uri = uriBase + uriPath + params

    const text = "great"

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

## <a name="dictionary-lookup-response"></a>Dictionary Lookup válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
[
  {
    "normalizedSource": "great",
    "displaySource": "great",
    "translations": [
      {
        "normalizedTarget": "grand",
        "displayTarget": "grand",
        "posTag": "ADJ",
        "confidence": 0.2783,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 34358
          },
          {
            "normalizedText": "big",
            "displayText": "big",
            "numExamples": 15,
            "frequencyCount": 21770
          },
...
        ]
      },
      {
        "normalizedTarget": "super",
        "displayTarget": "super",
        "posTag": "ADJ",
        "confidence": 0.1514,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "super",
            "displayText": "super",
            "numExamples": 15,
            "frequencyCount": 12023
          },
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 10931
          },
...
        ]
      },
...
    ]
  }
]
```

## <a name="dictionary-examples-request"></a>Dictionary Examples kérés

A következő a szótárban lévő kifejezések használatának szövegkörnyezetbe foglalt példáit kéri le a [Dictionary Examples](./reference/v3-0-dictionary-examples.md) metódussal.

1. Hozzon létre egy új Go-projektet a kedvenc kódszerkesztőjében.
2. Adja hozzá az alábbi kódot.
3. A `subscriptionKey` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Mentse a fájlt „.go” kiterjesztéssel.
5. Nyisson meg parancssort egy számítógépen, amelyen a Go telepítve van.
6. Állítsa össze a fájlt (például: „go build quickstart-examples.go”).
7. Futtassa a fájlt (például: „quickstart-examples”).

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
    const uriPath = "/dictionary/examples?api-version=3.0"

    // From English to French
    const params = "&from=en&to=fr"

    const uri = uriBase + uriPath + params

    const text = "great"
    const translation = "formidable"

    r := strings.NewReader("[{\"Text\" : \"" + text + "\", \"Translation\" : \"" + translation + "\"}]")

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

## <a name="dictionary-examples-response"></a>Dictionary Examples válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
[
  {
    "normalizedSource": "great",
    "normalizedTarget": "formidable",
    "examples": [
      {
        "sourcePrefix": "You have a ",
        "sourceTerm": "great",
        "sourceSuffix": " expression there.",
        "targetPrefix": "Vous avez une expression ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
      {
        "sourcePrefix": "You played a ",
        "sourceTerm": "great",
        "sourceSuffix": " game today.",
        "targetPrefix": "Vous avez été ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
...
    ]
  }
]
```

## <a name="next-steps"></a>További lépések

A Cognitive Services API-k Go-csomagjait a GitHubon ismerheti meg, a [Góhoz készült Azure SDK-val](https://github.com/Azure/azure-sdk-for-go) kapcsolatos témakörben.

> [!div class="nextstepaction"]
> [A Go-csomagok megismerése a GitHubon](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)
