---
title: Szándék a REST-hívással a Go-ban
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 02cb7738e20df6aba8690c9fe2ee718144bad114
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987779"
---
## <a name="prerequisites"></a>Előfeltételek

* [Go](https://golang.org/) programozási nyelv
* [Visual Studio kód](https://code.visualstudio.com/)
* Nyilvános alkalmazásazonosító:`df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>LUIS-futásidejű kulcs létrehozása előrejelzésekhez

1. Bejelentkezés az [Azure-portálra](https://portal.azure.com)
1. Kattintson [a **Nyelvismertetés létrehozása gombra.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Adja meg a **Runtime** kulcs összes szükséges beállítását:

    |Beállítás|Érték|
    |--|--|
    |Név|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válassza ki a közeli és elérhető helyeket|
    |Tarifacsomag|`F0`- a minimális tarifaszint|
    |Erőforráscsoport|Elérhető erőforráscsoport kiválasztása|

1. Kattintson a **Létrehozás gombra,** és várja meg az erőforrás létrehozását. Létrehozása után keresse meg az erőforráslapot.
1. Gyűjtse `endpoint` össze `key`a konfigurált és a.

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

Az Ugrás használatával lekérdezheti az [előrejelzési végpontot,](https://aka.ms/luis-apim-v3-prediction) és előrejelzési eredményt kaphat.

1. Hozzon létre egy új fájlt `predict.go` néven. Adja hozzá a következő kódot:

    ```go
    package main

    /* Do dependencies */
    import (
        "fmt"
        "net/http"
        "net/url"
        "io/ioutil"
        "log"
    )
    func main() {

        // public app
        var appID = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"

        // utterance for public app
        var utterance = "turn on all lights"

        // YOUR-KEY - your **Runtime** key
        var endpointKey = "YOUR-KEY"

        // YOUR-ENDPOINT - example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        endpointPrediction(appID, endpointKey, endpoint, utterance)
    }
    func endpointPrediction(appID string, endpointKey string, endpoint string, utterance string) {

        var endpointUrl = fmt.Sprintf("https://%s/luis/prediction/v3.0/apps/%s/slots/production/predict?subscription-key=%s&verbose=true&show-all-intents=true&query=%s", endpoint, appID, endpointKey, url.QueryEscape(utterance))

        response, err := http.Get(endpointUrl)

        if err!=nil {
            // handle error
            fmt.Println("error from Get")
            log.Fatal(err)
        }

        response2, err2 := ioutil.ReadAll(response.Body)

        if err2!=nil {
            // handle error
            fmt.Println("error from ReadAll")
            log.Fatal(err2)
        }

        fmt.Println("response")
        fmt.Println(string(response2))
    }
    ```

1. Cserélje `YOUR-KEY` le `YOUR-ENDPOINT` a és az értékeket a saját **előrejelzési Runtime** kulcs és végpont.

    |Információ|Cél|
    |--|--|
    |`YOUR-KEY`|A 32 karakterből álló **előrejelzési Runtime** kulcs.|
    |`YOUR-ENDPOINT`| Az előrejelzési URL-végpont. Például: `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Ha a parancssorba ugyanabban a könyvtárban található, ahol a fájlt létrehozta, írja be a következő parancsot a Go fájl fordításához:

    ```console
    go build predict.go
    ```

1. Futtassa a Go-alkalmazást a parancssorból a következő szöveg beírásával:

    ```console
    go run predict.go
    ```

    A parancssori válasz a következő:

    ```console
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value a7b206911f714e71a1ddae36928a61cc
    endpoint has value westus2.api.cognitive.microsoft.com
    utterance has value turn on all lights
    response
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    JSON formázott olvashatóság:

    ```json
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte ezt a rövid útmutatót, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kimondott szöveg hozzáadása és betanítása](../get-started-get-model-rest-apis.md)