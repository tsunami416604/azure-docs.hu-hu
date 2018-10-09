---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: e507a7c45e286473abe9b9e4365e80fb29eba2a4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044020"
---
Ha szeretné látni, mit ad vissza egy LUIS előrejelzési végpont, tekintsen meg egy előrejelzési eredményt egy webböngészőben. Nyilvános alkalmazás lekérdezéséhez a saját kulcsára és az alkalmazásazonosítóra is szükség van. A nyilvános IoT-alkalmazás azonosítója, a `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, az első lépésben az URL-cím részeként van megadva.

Egy **GET** végpontkérelem URL-címének formátuma a következő:

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. A nyilvános IoT-alkalmazás végpontja a következő formátumú: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    Másolja az URL-címet, és a `<YOUR_KEY>` helyére írja be a saját kulcsát.

2. Illessze be az URL-címet egy böngészőablakba, és nyomja le az Enter billentyűt. A böngészőben megjelenik egy JSON-eredmény, amely jelzi, hogy a LUIS a `HomeAutomation.TurnOn` szándékot észleli első szándékként és a `bedroom` értékű `HomeAutomation.Room` entitást.

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. Módosítsa az URL-címben található `q=` paraméter értékét `turn off the living room light` értékre, majd nyomja le az Enter billentyűt. Az eredmény most azt jelzi, hogy a LUIS a `HomeAutomation.TurnOff` szándékot észleli első szándékként és a `living room` értékű `HomeAutomation.Room` entitást. 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
