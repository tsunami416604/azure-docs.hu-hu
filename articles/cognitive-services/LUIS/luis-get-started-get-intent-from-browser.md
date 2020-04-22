---
title: 'Rövid útmutató: Előrejelzés lekérdezése böngészővel – LUIS'
description: Ebben a rövid útmutatóban egy elérhető nyilvános LUIS-alkalmazás segítségével határozza meg a felhasználó szándékát a böngészőben lévő társalgási szövegből.
ms.topic: quickstart
ms.date: 04/21/2020
ms.openlocfilehash: 5ba86882ebf3cb538ad6b865382342fcbd43d27c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769968"
---
# <a name="quickstart-query-prediction-runtime-with-user-text"></a>Rövid útmutató: Lekérdezés-előrejelzési futásidő felhasználói szöveggel

Ha szeretné látni, mit ad vissza egy LUIS előrejelzési végpont, tekintsen meg egy előrejelzési eredményt egy webböngészőben.

## <a name="prerequisites"></a>Előfeltételek

Egy nyilvános alkalmazás lekérdezéséhez a következőkre van szükség:

* Az Ön nyelvi ismertetési (LUIS) erőforrásadatai:
    * **Előrejelzési kulcs** - amely a LUIS Portal-ból szerezhető [be.](https://www.luis.ai/) Ha még nem rendelkezik előfizetéssel a kulcs létrehozásához, regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/)
    * **Előrejelzés végpont altartomány** - az altartomány is a **LUIS-erőforrás neve.**
* Luis-alkalmazásazonosító – használja a nyilvános IoT-alkalmazásazonosítóját. `df67dcdb-c37d-46af-88e1-8b97951ca1c2` A gyorsútmutató kódban használt felhasználói lekérdezés az adott alkalmazásra jellemző.

## <a name="use-the-browser-to-see-predictions"></a>Az előrejelzések megtekintéséhez használja a böngészőt

1. Nyisson meg egy webböngészőt.
1. Használja az alábbi teljes URL-címeket, és cserélje le `YOUR-KEY` a saját LUIS-előrejelzési kulcsát. A kérelmek GET-kérelmek, és tartalmazza az engedélyezést, a LUIS előrejelzési kulcs, a lekérdezési karakterlánc paraméterként.

    #### <a name="v3-prediction-request"></a>[V3 előrejelzési kérelem](#tab/V3-1-1)


    A **Get** végpont (résidők szerint) kérelem V3-as URL-címének formátuma a következő:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2 előrejelzési kérelem](#tab/V2-1-2)

    A **Get** végpontkérés V2 URL-címének formátuma a következő:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. Illessze be az URL-címet egy böngészőablakba, és nyomja le az Enter billentyűt. A böngészőben megjelenik egy JSON-eredmény, amely jelzi, hogy a LUIS a `HomeAutomation.TurnOn` szándékot észleli első szándékként és a `on` értékű `HomeAutomation.Operation` entitást.

    #### <a name="v3-prediction-response"></a>[V3 előrejelzési válasz](#tab/V3-2-1)

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

    #### <a name="v2-prediction-response"></a>[V2 előrejelzési válasz](#tab/V2-2-2)

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

1. Az összes leképezés megtekintéséhez adja hozzá a megfelelő lekérdezési karakterlánc-paramétert.

    #### <a name="v3-prediction-endpoint"></a>[V3 előrejelzési végpont](#tab/V3-3-1)

    A `show-all-intents=true` lekérdezési karakterlánc végének hozzáadása az **összes leképezés megjelenítéséhez:**

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
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

    #### <a name="v2-prediction-endpoint"></a>[V2 előrejelzési végpont](#tab/V2)

    A `verbose=true` lekérdezési karakterlánc végének hozzáadása az **összes leképezés megjelenítéséhez:**

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
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

## <a name="next-steps"></a>További lépések

További információk:
* [V3 előrejelzési végpont](luis-migration-api-v3.md)
* [Egyéni altartományok](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Alkalmazás létrehozása a LUIS portálon](get-started-portal-build-app.md)
