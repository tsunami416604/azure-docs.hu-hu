---
title: 'Rövid útmutató: A Text Analytics API meghívása a Go használatával'
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services Text Analytics API-jának gyors használatának megkezdéséhez információkat és kódmintákat kaphat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 03311cb873420f741ca0150dde59fb27eaa5d76f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378762"
---
# <a name="quickstart-using-go-to-call-the-text-analytics-cognitive-service"></a>Rövid útmutató: A Text Analytics Cognitive Services meghívása a Go használatával 
<a name="HOLTop"></a>

Ez a cikk [bemutatja, hogyan észlelheti a nyelvet,](#Detect)elemezheti a [véleményeket,](#SentimentAnalysis)hogyan nyerheti ki a [kulcskifejezéseket,](#KeyPhraseExtraction)és [hogyan azonosíthatja](#Entities) a csatolt entitásokat a [Szövegelemzési API-k](//go.microsoft.com/fwlink/?LinkID=759711) go használatával.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]


<a name="Detect"></a>

## <a name="detect-language"></a>Nyelv felismerése

A Language Detection API a [Detect Language metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) használatával felismeri a szöveges dokumentumok nyelvét.

1. Hozzon létre egy új Go-projektet a kedvenc kódszerkesztőjében.
1. Adja hozzá az alábbi kódot.
1. Másolja a Text Analytics API-kulcs át a kódot.
1. Mentse a fájlt „.go” kiterjesztéssel.
1. Nyisson meg egy parancssort egy számítógépen, amelyen a Go telepítve van a gyökérmappából.
1. Állítsa össze a fájlt (például: `go build detect.go`).
1. Futtassa a fájlt (például: `go run detect.go`).

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    
    var subscriptionKey string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"
    const uriPath = "/text/analytics/v2.1/languages"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "text": "This is a document written in English."},
        {"id": "2", "text": "Este es un document escrito en Español."},
        {"id": "3", "text": "这是一个用中文写的文件"},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="detect-language-response"></a>Detect language válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Vélemények elemzése

A Sentiment Analysis API a szöveges bejegyzések hangulatát érzékeli a [Sentiment metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) használatával. A következő példa két dokumentumhoz rendel pontszámot, az egyik angol, a másik spanyol nyelvű.

1. Hozzon létre egy új Go-projektet a kedvenc kódszerkesztőjében.
1. Adja hozzá az alábbi kódot.
1. Másolja a Text Analytics-kulcsot és a végpontot a kódba.
1. Mentse a fájlt „.go” kiterjesztéssel.
1. Nyisson meg egy parancssort egy számítógépen, amelyen a Go telepítve van a gyökérmappából.
1. Állítsa össze a fájlt (például: `go build sentiment.go`).
1. Futtassa a fájlt (például: `go run sentiment.go`).

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKey string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    const uriPath = "/text/analytics/v2.1/sentiment"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="analyze-sentiment-response"></a>Analyze sentiment válasz

Az eredmény akkor lesz pozitív, ha az 1.0-hoz közelebb ért, és negatív, ha közelebb van a 0.0-hoz.
A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Kulcsszavak kinyerése

A Key Phrase Extraction API kulcskifejezéseket nyer ki a szöveges dokumentumokból a [Key Phrases metódus](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) használatával. A következő példa kulcskifejezéseket nyer ki angol és spanyol nyelvű dokumentumokhoz.

1. Hozzon létre egy új Go-projektet a kedvenc kódszerkesztőjében.
1. Adja hozzá az alábbi kódot.
1. Másolja a Text Analytics-kulcsot és a végpontot a kódba.
1. Mentse a fájlt „.go” kiterjesztéssel.
1. Nyisson meg parancssort egy számítógépen, amelyen a Go telepítve van.
1. Állítsa össze a fájlt (például: `go build key-phrases.go`).
1. Futtassa a fájlt (például: `go run key-phrases.go`).

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    var subscriptionKey string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"
    
    const uriPath = "/text/analytics/v2.1/keyPhrases"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
        {"id": "3", "language": "en", "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="extract-key-phrases-response"></a>Extract key phrases válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Entitások azonosítása

Az Entities API azonosítja a szöveges dokumentumok jól ismert entitásait az [Entities metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) használatával. [Az entitások kinyerik](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) a szavakat a szövegből, például az "Egyesült Államok", majd megadják a szó(ok) típusát és/vagy Wikipédia-hivatkozását. Az "Egyesült Államok" `location`típusa a , míg `https://en.wikipedia.org/wiki/United_States`a Wikipédiára mutató hivatkozás .  Az alábbi példa angol nyelvű dokumentumok entitásait azonosítja.

1. Hozzon létre egy új Go-projektet a kedvenc kódszerkesztőjében.
1. Adja hozzá az alábbi kódot.
1. Mentse a fájlt „.go” kiterjesztéssel.
1. Nyisson meg parancssort egy számítógépen, amelyen a Go telepítve van.
1. Állítsa össze a fájlt (például: `go build entities.go`).
1. Futtassa a fájlt (például: `go run entities.go`).

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {

    var subscriptionKey string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"
    
    const uriPath = "/text/analytics/v2.1/entities"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "Microsoft is an It company."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
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

## <a name="entity-extraction-response"></a>Entitáskinyerési válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-val](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még

 [Szövegelemzés – áttekintés](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
