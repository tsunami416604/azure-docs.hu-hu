---
title: Leképezés a REST-hívással a Node.js-ben
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 3bef3cbb321465893b3a05242f0d72d77d091d6b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733327"
---
## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/) programozási nyelv
* [Visual Studio Code](https://code.visualstudio.com/)
* Luis-alkalmazásazonosító – használja a nyilvános IoT-alkalmazásazonosítóját. `df67dcdb-c37d-46af-88e1-8b97951ca1c2` A gyorsútmutató kódban használt felhasználói lekérdezés az adott alkalmazásra jellemző.

## <a name="create-luis-runtime-key-for-predictions"></a>LUIS-futásidejű kulcs létrehozása előrejelzésekhez

1. Bejelentkezés az [Azure-portálra](https://portal.azure.com)
1. Kattintson [a **Nyelvismertetés létrehozása gombra.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Adja meg a **Runtime** kulcs összes szükséges beállítását:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válassza ki a közeli és elérhető helyeket|
    |Tarifacsomag|`F0`- a minimális tarifaszint|
    |Erőforráscsoport|Elérhető erőforráscsoport kiválasztása|

1. Kattintson a **Létrehozás gombra,** és várja meg az erőforrás létrehozását. Létrehozása után keresse meg az erőforráslapot.
1. Gyűjtse `endpoint` össze `key`a konfigurált és a.

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

A Node.js használatával lekérdezheti az [előrejelzési végpontot,](https://aka.ms/luis-apim-v3-prediction) és előrejelzési eredményt kaphat.

1. Másolja a következő kódrészletet egy `predict.js`fájlba, amelynek neve:

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

1. Cserélje `YOUR-KEY` le `YOUR-ENDPOINT` a és az értékeket a saját **előrejelzési Runtime** kulcs és végpont.

    |Információ|Cél|
    |--|--|
    |`YOUR-KEY`|A 32 karakterből álló **előrejelzési Runtime** kulcs.|
    |`YOUR-ENDPOINT`| Az előrejelzési URL-végpont. Például: `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Telepítse `request`a `request-promise`, `querystring` és függőségeket ezzel a paranccsal:

    ```console
    npm install request request-promise querystring
    ```

1. Futtassa az alkalmazást ezzel a paranccsal:

    ```console
    node predict.js
    ```

 1. Tekintse át az előrejelzési választ, amely JSON-ként ad vissza:

    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    A JSON válasz formázott olvashatóság:

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

Ha befejezte ezt a rövid útmutatót, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kimondott szöveg hozzáadása és betanítása](../get-started-get-model-rest-apis.md)