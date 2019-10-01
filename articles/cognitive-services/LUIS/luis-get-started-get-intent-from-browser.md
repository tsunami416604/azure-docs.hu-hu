---
title: 'Gyors útmutató: Szándék beszerzése a böngészőben – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy elérhető nyilvános LUIS-alkalmazás használatával határozhatja meg a felhasználó szándékát a böngészőben a társalgási szövegben.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703130"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Gyors útmutató: Szándék beszerzése böngészővel

Ha szeretné látni, mit ad vissza egy LUIS előrejelzési végpont, tekintsen meg egy előrejelzési eredményt egy webböngészőben. 

## <a name="prerequisites"></a>Előfeltételek

Egy nyilvános alkalmazás lekérdezéséhez a következőkre lesz szüksége:

* Saját Language Understanding (LUIS) kulcsa. Ha még nem rendelkezik előfizetéssel a kulcs létrehozásához, regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* A nyilvános alkalmazás azonosítója: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>A böngésző használata az előrejelzések megjelenítéséhez

1. Nyisson meg egy webböngészőt. 
1. Használja az alábbi teljes URL-címeket, és cserélje le a `{your-key}` értéket a saját LUIS-kulcsára. A kérések MEGKAPják a kéréseket, és tartalmazzák az engedélyt a LUIS-kulccsal, lekérdezési karakterlánc paraméterként.

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[V2 előrejelzési végpont kérése](#tab/V2)
    
    A **Get** Endpoint kérelem v2 URL-címének formátuma a következő:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[V3 előrejelzési végpont kérése](#tab/V3)
    
    
    A **Get** -végpont (bővítőhely) kérelmének v3 URL-címének formátuma a következő:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. Illessze be az URL-címet egy böngészőablakba, és nyomja le az Enter billentyűt. A böngészőben megjelenik egy JSON-eredmény, amely jelzi, hogy a LUIS a `HomeAutomation.TurnOn` szándékot észleli első szándékként és a `on` értékű `HomeAutomation.Operation` entitást.

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * *

1. Az összes cél megjelenítéséhez adja hozzá a megfelelő lekérdezési karakterlánc paramétert. 

    #### <a name="v2-prediction-endpointtabv2"></a>[V2 előrejelzési végpont](#tab/V2)

    Adja hozzá a `verbose=true` értéket a querystring végéhez az **összes cél megjelenítéséhez**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    #### <a name="v3-prediction-endpointtabv3"></a>[V3 előrejelzési végpont](#tab/V3)

    Adja hozzá a `show-all-intents=true` értéket a querystring végéhez az **összes cél megjelenítéséhez**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    * * * 

## <a name="next-steps"></a>További lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Alkalmazás létrehozása a LUIS-portálon](get-started-portal-build-app.md)