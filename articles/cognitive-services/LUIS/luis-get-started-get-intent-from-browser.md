---
title: 'Gyors útmutató: Ismerkedés a böngészővel – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy elérhető nyilvános LUIS-alkalmazás használatával határozhatja meg a felhasználó szándékát a böngészőben a társalgási szövegben.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: e06bb4c09b3ebab25c0c0ef8ac5c51f6842f34cd
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987954"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Rövid útmutató: a szándékok beszerzése böngészővel

Ha szeretné látni, mit ad vissza egy LUIS előrejelzési végpont, tekintsen meg egy előrejelzési eredményt egy webböngészőben.

## <a name="prerequisites"></a>Előfeltételek

Egy nyilvános alkalmazás lekérdezéséhez a következőkre lesz szüksége:

* Saját Language Understanding (LUIS) szerzői vagy előrejelző kulcsa, amely a [Luis Portalról (előzetes verzió)](https://preview.luis.ai/)szerezhető be. Ha még nem rendelkezik előfizetéssel a kulcs létrehozásához, regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* A nyilvános alkalmazás azonosítója: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.

## <a name="use-the-browser-to-see-predictions"></a>A böngésző használata az előrejelzések megjelenítéséhez

1. Nyisson meg egy webböngészőt.
1. Használja az alábbi teljes URL-címeket, és cserélje le a `YOUR-KEY`t a saját LUIS authoring vagy előrejelző kulcsával. A kérések MEGKAPják a kéréseket, és tartalmazzák az engedélyt, a LUIS authoring vagy az előrejelzési kulccsal, lekérdezési karakterlánc paraméterként.

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[V3 előrejelzési kérelem](#tab/V3-1-1)


    A **Get** -végpont (bővítőhely) kérelmének v3 URL-címének formátuma a következő:

    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[V2 előrejelzési kérelem](#tab/V2-1-2)

    A **Get** Endpoint kérelem v2 URL-címének formátuma a következő:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Illessze be az URL-címet egy böngészőablakba, és nyomja le az Enter billentyűt. A böngészőben megjelenik egy JSON-eredmény, amely jelzi, hogy a LUIS a `HomeAutomation.TurnOn` szándékot észleli első szándékként és a `on` értékű `HomeAutomation.Operation` entitást.

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[V3-előrejelzési válasz](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
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

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[V2 előrejelzési válasz](#tab/V2-2-2)

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

    * * *

1. Az összes cél megjelenítéséhez adja hozzá a megfelelő lekérdezési karakterlánc paramétert.

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[V3 előrejelzési végpont](#tab/V3-3-1)

    `show-all-intents=true` hozzáadása a querystring végéhez az **összes cél megjelenítéséhez**:

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
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
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpointtabv2"></a>[V2 előrejelzési végpont](#tab/V2)

    `verbose=true` hozzáadása a querystring végéhez az **összes cél megjelenítéséhez**:

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


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->

## <a name="next-steps"></a>Következő lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Alkalmazás létrehozása a LUIS-portálon](get-started-portal-build-app.md)
