---
title: Modell beszerezni e-t a REST-hívással a Go-ban
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 8d180eeffdbc41db6fa0e636daf7702faad47fcc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368447"
---
## <a name="prerequisites"></a>Előfeltételek

* Azure Language Understanding – Authoring resource 32 karakterkulcs és szerzői végpont URL-címe. Hozzon létre az [Azure Portalon](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) vagy az [Azure CLI-n](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli)keresztül.
* Importálja a [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) alkalmazást a cognitive-services-language-understanding GitHub-tárházból.
* Az importált TravelAgent alkalmazás LUIS-alkalmazásazonosítója. Az alkalmazásazonosító az alkalmazás irányítópultján látható.
* A kimondott szövegeket fogadó alkalmazáson belüli verzióazonosító. Az alapértelmezett azonosító a „0.1”.
* [Go](https://golang.org/) programozási nyelv
* [Visual Studio kód](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Modell módosítása programozott módon

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

        // NOTE: change to your authoring key
        var authoringKey = "YOUR-KEY"

        // NOTE: change to your authoring key's endpoint, for example, your-resource-name.api.cognitive.microsoft.com
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

1. Cserélje le a `YOUR-` saját értékeivel kezdődő értékeket.

    |Információ|Cél|
    |--|--|
    |`YOUR-KEY`|A 32 karakteres szerzői kulcs.|
    |`YOUR-ENDPOINT`| A szerzői URL-végpont. Például: `replace-with-your-resource-name.api.cognitive.microsoft.com`. Az erőforrás nevét az erőforrás létrehozásakor állította be.|
    |`YOUR-APP-ID`| A LUIS-alkalmazásazonosító. |

    A hozzárendelt kulcsok és erőforrások a LUIS-portálon, a Kezelés szakaszban, az **Azure-erőforrások** lapon láthatók. Az alkalmazásazonosító ugyanabban a Kezelés szakaszban, az **Alkalmazás beállítások** lapján érhető el.

1. Ha a parancssorba ugyanabban a könyvtárban található, ahol a fájlt létrehozta, írja be a következő parancsot a Go fájl fordításához:

    ```console
    go build model.go
    ```

1. Futtassa a Go-alkalmazást a parancssorból a következő szöveg beírásával:

    ```console
    go run model.go
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte ezt a rövid útmutatót, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyakorlati tanácsok egy alkalmazáshoz](../luis-concept-best-practices.md)