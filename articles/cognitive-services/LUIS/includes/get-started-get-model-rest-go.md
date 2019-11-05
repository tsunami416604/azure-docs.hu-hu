---
title: Modell lekérése REST-hívással a Go-ban
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: ec61abca19579426818e227687e08e66b73969cb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503758"
---
## <a name="prerequisites"></a>Előfeltételek

* Alapszintű kulcs.
* Importálja a [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) alkalmazást a kognitív-Services-Language-Understanding GitHub adattárba.
* Az importált TravelAgent alkalmazás LUIS-alkalmazásának azonosítója. Az alkalmazásazonosító az alkalmazás irányítópultján látható.
* A hosszúságú kimondott szöveg fogadó alkalmazásban található verzióazonosító. Az alapértelmezett azonosító a „0.1”.
* [Go](https://golang.org/) programozási nyelv  
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="get-luis-key"></a>LUIS-kulcs lekérése

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Modell programozott módosítása

A go használatával adjon hozzá egy géppel megtanult entitás [API](https://aka.ms/luis-apim-v3-authoring) -t az alkalmazáshoz. 

1. Hozzon létre egy új fájlt `predict.go` néven. Adja hozzá a következő kódot:
    
    ```go
    // dependencies
    package main
    import (
        "fmt"
        "net/http"
        "io/ioutil"
        "log"
        "strings"
    )
    
    // main function
    func main() {
    
        // NOTE: change to your app ID
        var appID = "YOUR-APP-ID"
    
        // NOTE: change to your starter key
        var authoringKey = "YOUR-KEY"
    
        // NOTE: change to your starter key's endpoint, for example, westus.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"  
    
        var version = "0.1"
    
        var exampleUtterances = `
        [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ]
        `
    
        fmt.Println("add example utterances requested")
        addUtterance(authoringKey, appID, version, exampleUtterances, endpoint)
    
        fmt.Println("training selected")
        requestTraining(authoringKey, appID, version, endpoint)
    
        fmt.Println("training status selected")
        getTrainingStatus(authoringKey, appID, version, endpoint)
    }
    
    // get utterances from file and add to model
    func addUtterance(authoringKey string, appID string,  version string, labeledExampleUtterances string, endpoint string){
    
        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/examples", endpoint, appID, version)
    
        httpRequest("POST", authoringUrl, authoringKey, labeledExampleUtterances)
    }
    func requestTraining(authoringKey string, appID string,  version string, endpoint string){
    
        trainApp("POST", authoringKey, appID, version, endpoint)
    }
    func trainApp(httpVerb string, authoringKey string, appID string,  version string, endpoint string){
    
        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/train", endpoint, appID, version)
    
        httpRequest(httpVerb,authoringUrl, authoringKey, "")
    }
    func getTrainingStatus(authoringKey string, appID string, version string, endpoint string){
    
        trainApp("GET", authoringKey, appID, version, endpoint)
    }
    // generic HTTP request
    // includes setting header with authoring key
    func httpRequest(httpVerb string, url string, authoringKey string, body string){
    
        client := &http.Client{}
    
        request, err := http.NewRequest(httpVerb, url, strings.NewReader(body))
        request.Header.Add("Ocp-Apim-Subscription-Key", authoringKey)
    
        fmt.Println("body")
        fmt.Println(body)
    
        response, err := client.Do(request)
        if err != nil {
            log.Fatal(err)
        } else {
            defer response.Body.Close()
            contents, err := ioutil.ReadAll(response.Body)
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println("   ", response.StatusCode)
            fmt.Println(string(contents))
        }
    }    
    ```

1. Cserélje le a következő értékeket:

    * `YOUR-KEY` az alapszintű kulccsal
    * `YOUR-ENDPOINT` a végponttal, például `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` az alkalmazás azonosítójával

1. Ha a parancssor ugyanabban a címtárban található, ahol a fájlt létrehozta, adja meg a következő parancsot a go-fájl fordításához:

    ```console
    go build model.go
    ```  

1. Futtassa a Go-alkalmazást a parancssorból a következő szöveg beírásával: 

    ```console
    go run model.go
    ```

## <a name="luis-keys"></a>LUIS-kulcsok

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a rövid útmutatóval, törölje a fájlt a fájlrendszerből. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ajánlott eljárások az alkalmazásokhoz](../luis-concept-best-practices.md)