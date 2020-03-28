---
title: 'Modell beszereznie A REST-hívással c-ben #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: bbb2ae0b10af795d71f0a78c045bec0c216ee378
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368393"
---
## <a name="prerequisites"></a>Előfeltételek

* Azure Language Understanding – Authoring resource 32 karakterkulcs és szerzői végpont URL-címe. Hozzon létre az [Azure Portalon](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) vagy az [Azure CLI-n](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli)keresztül.
* Importálja a [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) alkalmazást a cognitive-services-language-understanding GitHub-tárházból.
* Az importált TravelAgent alkalmazás LUIS-alkalmazásazonosítója. Az alkalmazásazonosító az alkalmazás irányítópultján látható.
* A kimondott szövegeket fogadó alkalmazáson belüli verzióazonosító. Az alapértelmezett azonosító a „0.1”.
* [Node.js](https://nodejs.org/) programozási nyelv
* [Visual Studio kód](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>Modell módosítása programozott módon

1. Hozzon létre egy új fájlt `model.js` néven. Adja hozzá a következő kódot:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');

    // 32 character key value
    const LUIS_authoringKey = "YOUR-KEY";

    // endpoint example: your-resource-name.api.cognitive.microsoft.com
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;

    const utterances = [
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
          ];

    const main = async() =>{


        await addUtterance();
        await train("POST");
        await trainStatus("GET");

    }
    const addUtterance = async () => {

        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };

        const response = await requestpromise(options)
        console.log(response.body);
    }
    const train = async (verb) => {

        const options = {
            uri: addTrainURI,
            method: verb,
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };

        const response = await requestpromise(options)
        console.log(response.body);
    }

    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```

1. Cserélje le a `YOUR-` saját értékeivel kezdődő értékeket.

    |Információ|Cél|
    |--|--|
    |`YOUR-KEY`|A 32 karakteres szerzői kulcs.|
    |`YOUR-ENDPOINT`| A szerzői URL-végpont. Például: `replace-with-your-resource-name.api.cognitive.microsoft.com`. Az erőforrás nevét az erőforrás létrehozásakor állította be.|
    |`YOUR-APP-ID`| A LUIS-alkalmazásazonosító. |

    A hozzárendelt kulcsok és erőforrások a LUIS-portálon, a Kezelés szakaszban, az **Azure-erőforrások** lapon láthatók. Az alkalmazásazonosító ugyanabban a Kezelés szakaszban, az **Alkalmazás beállítások** lapján érhető el.

1. Ha a parancssorugyanabban a könyvtárban található, ahol a fájlt létrehozta, írja be a következő parancsot a fájl futtatásához:

    ```console
    node model.js
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte ezt a rövid útmutatót, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyakorlati tanácsok egy alkalmazáshoz](../luis-concept-best-practices.md)