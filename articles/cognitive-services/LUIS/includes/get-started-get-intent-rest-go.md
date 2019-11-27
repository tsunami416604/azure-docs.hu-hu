---
title: A REST-hívás beszerzése a Go-ban
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 5054ee9a23458944257a8010aaab6268d25042a7
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414513"
---
## <a name="prerequisites"></a>Előfeltételek

* [Go](https://golang.org/) programozási nyelv  
* [Visual Studio Code](https://code.visualstudio.com/)
* Nyilvános alkalmazás azonosítója: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

A go paranccsal lekérdezheti az [előrejelzési végpontot](https://aka.ms/luis-apim-v3-prediction) , és lekérheti az előrejelzés eredményét.

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
        
        // YOUR-KEY - your starter or prediction key
        var endpointKey = "YOUR-KEY"
        
        // YOUR-ENDPOINT - example is westus2.api.cognitive.microsoft.com
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

1. Cserélje le a következő értékeket:

    * `YOUR-KEY` az alapszintű kulccsal.
    * `YOUR-ENDPOINT` a végponttal. Például: `westus2.api.cognitive.microsoft.com`.

1. Ha a parancssor ugyanabban a címtárban található, ahol a fájlt létrehozta, adja meg a következő parancsot a go-fájl fordításához:

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

    Az olvashatósághoz formázott JSON:

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


## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, törölje a fájlt a fájlrendszerből. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hosszúságú kimondott szöveg és-betanítás hozzáadása](../get-started-get-model-rest-apis.md)