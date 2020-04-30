---
title: REST-hívás a Node. js-ben
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 3bef3cbb321465893b3a05242f0d72d77d091d6b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733327"
---
## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/) programozási nyelv
* [Visual Studio Code](https://code.visualstudio.com/)
* A LUIS-alkalmazás azonosítója – használja a nyilvános IoT-alkalmazás `df67dcdb-c37d-46af-88e1-8b97951ca1c2`azonosítóját. A rövid útmutató kódjában használt felhasználói lekérdezés az adott alkalmazásra jellemző.

## <a name="create-luis-runtime-key-for-predictions"></a>LUIS Runtime-kulcs létrehozása előrejelzésekhez

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com)
1. Kattintson a [Létrehozás gombra **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Adja meg az összes szükséges beállítást a **futásidejű** kulcshoz:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válasszon ki egy közeli és elérhető helyet|
    |Tarifacsomag|`F0`– a minimális díjszabási réteg|
    |Erőforráscsoport|Válasszon ki egy rendelkezésre álló erőforráscsoportot|

1. Kattintson a **Létrehozás** gombra, és várja meg az erőforrás létrehozását. A létrehozást követően navigáljon az erőforrás lapra.
1. A configured `endpoint` és `key`a a összegyűjtése.

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

A Node. js segítségével lekérdezheti az [előrejelzési végpontot](https://aka.ms/luis-apim-v3-prediction) , és lekérheti az előrejelzés eredményét.

1. Másolja a következő kódrészletet egy nevű `predict.js`fájlba:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    var querystring = require('querystring');

    // Analyze text
    //
    getPrediction = async () => {

        // YOUR-KEY - Language Understanding runtime key
        var endpointKey = "YOUR-KEY";

        // YOUR-ENDPOINT Language Understanding endpoint URL, an example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT";

        // Set the LUIS_APP_ID environment variable
        // to df67dcdb-c37d-46af-88e1-8b97951ca1c2, which is the ID
        // of a public sample application.
        var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

        var utterance = "turn on all lights";

        // Create query string
        var queryParams = {
            "show-all-intents": true,
            "verbose":  true,
            "query": utterance,
            "subscription-key": endpointKey
        }

        // append query string to endpoint URL
        var URI = `https://${endpoint}/luis/prediction/v3.0/apps/${appId}/slots/production/predict?${querystring.stringify(queryParams)}`

        // HTTP Request
        const response = await requestpromise(URI);

        // HTTP Response
        console.log(response);

    }

    // Pass an utterance to the sample LUIS app
    getPrediction().then(()=>console.log("done")).catch((err)=>console.log(err));
    ```

1. Cserélje le `YOUR-KEY` az `YOUR-ENDPOINT` és az értékeket a saját előrejelzési **futtatókörnyezeti** kulcsára és végpontra.

    |Információ|Cél|
    |--|--|
    |`YOUR-KEY`|Az 32 karakteres előrejelzési **futtatókörnyezet** kulcsa.|
    |`YOUR-ENDPOINT`| Az előrejelzési URL-végpont. Például: `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Telepítse a `request`, `request-promise`a és `querystring` a függőségeket a következő paranccsal:

    ```console
    npm install request request-promise querystring
    ```

1. Futtassa az alkalmazást a következő paranccsal:

    ```console
    node predict.js
    ```

 1. Tekintse át az előrejelzési választ, amely JSON-ként lesz visszaadva:

    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    Az olvashatóságra formázott JSON-Válasz:

    ```JSON
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

Ha elkészült a rövid útmutatóval, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hosszúságú kimondott szöveg és-betanítás hozzáadása](../get-started-get-model-rest-apis.md)