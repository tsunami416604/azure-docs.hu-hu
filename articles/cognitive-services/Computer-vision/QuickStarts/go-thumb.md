---
title: 'Gyors útmutató: Miniatűr létrehozása – REST, go'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban miniatűrt hozhat létre egy képből a Computer Vision API és Go használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 89c01fc4a48d9264b12e22c593cd9d68d0fd4ab4
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137943"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-with-go"></a>Gyors útmutató: Miniatűr létrehozása a Computer Vision REST API a go használatával

Ebben a rövid útmutatóban létrehoz egy miniatűrt a rendszerképből Computer Vision REST API használatával. Megadhatja a magasságot és a szélességet, ami eltérő lehet a bemeneti kép oldalaránya alapján. A Computer Vision az intelligens vágás használatával intelligens módon azonosítja a fontos területet, és az adott régió alapján készíti el a levágási koordinátákat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell a [Góval](https://golang.org/dl/).
- Szüksége lesz egy Computer Vision-előfizetői azonosítóra. A [kipróbálási Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)ingyenes próbaverziós kulcsot is beszerezhet. Vagy kövesse a [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Computer Visionra való előfizetéshez és a kulcs beszerzéséhez című témakör utasításait. Ezután [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a kulcs-és szolgáltatás végponti `COMPUTER_VISION_SUBSCRIPTION_KEY` karakterláncához, a nevet és `COMPUTER_VISION_ENDPOINT`a-t.

## <a name="create-and-run-the-sample"></a>A minta létrehozása és futtatása

A minta létrehozásához és futtatásához az alábbi lépéseket kell végrehajtania:

1. Másolja az alábbi kódot egy szövegszerkesztőbe.
1. Ha szeretné, cserélje le az `imageUrl` értéket annak a képnek az URL-címére, amelyhez miniatűrt szeretne létrehozni.
1. Mentse a kódot fájlként `.go` kiterjesztéssel. Például: `get-thumbnail.go`.
1. Nyisson meg egy parancsablakot.
1. A parancssorban futtassa a `go build` parancsot a fájl csomagjának lefordításához. Például: `go build get-thumbnail.go`.
1. Amikor a rendszer erre kéri, futtassa a fordított csomagot. Például: `get-thumbnail`.

```go
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

func main() {
    // Add your Computer Vision subscription key and endpoint to your environment variables.
    subscriptionKey := os.Getenv("COMPUTER_VISION_SUBSCRIPTION_KEY")
    if (subscriptionKey == "") {
        log.Fatal("\n\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n" +
            "**Restart your shell or IDE for changes to take effect.**\n")

    endpoint := os.Getenv("COMPUTER_VISION_ENDPOINT")
    if ("" == endpoint) {
        log.Fatal("\n\nSet the COMPUTER_VISION_ENDPOINT environment variable.\n" +
            "**Restart your shell or IDE for changes to take effect.**")
    }
    const uriBase = endpoint + "vision/v2.0/generateThumbnail"
    const imageUrl =
        "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
    const uri = uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the HTTP client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the POST request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }

    // Parse the JSON data
    var f interface{}
    json.Unmarshal(data, &f)

    // Format and display the JSON result
    jsonFormatted, _ := json.MarshalIndent(f, "", "  ")
    fmt.Println(string(jsonFormatted))
}
```

## <a name="examine-the-response"></a>A válasz vizsgálata

A sikeres válasz tartalmazza a miniatűrkép bináris adatait. Ha a kérés meghiúsul, a válasz tartalmaz egy hibakódot és egy üzenetet, amely segít meghatározni a hiba okát.

## <a name="next-steps"></a>További lépések

Tekintse át a Computer Vision API a képek elemzéséhez, a hírességek és tereptárgyak észleléséhez, a miniatűr létrehozásához, valamint a nyomtatott és a kézírásos szöveg kinyeréséhez. A Computer Vision API-val való gyors kísérletezéshez próbálja ki az [Open API-tesztkonzolt](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tekintse át a Computer Vision API-t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
