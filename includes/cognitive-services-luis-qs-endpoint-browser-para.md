---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: dae56e05f01e83f05e75fdf378c0c50679d18728
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819994"
---
Szeretné megtudni, a LUIS-előrejelzési végpont adja vissza, böngészőben a egy előrejelzési eredmény megtekintéséhez. Annak érdekében, hogy a lekérdezés egy nyilvános alkalmazást, szüksége saját kulcs és az alkalmazás azonosítóját. A nyilvános IoT Alkalmazásazonosító `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, amelyiket az első lépésben az URL-cím része.

URL-Címének formátuma a **első** végpont kérelem:

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. A végpont a nyilvános IoT-alkalmazás a következő formátumban van: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    Másolja az URL-címet, és cserélje le a kulcs értékét a `<YOUR_KEY>`.

2. Illessze be az URL-címet egy böngészőablakba, és nyomja le az Enter billentyűt. A böngészőben megjelenik egy JSON-eredményt, amely azt jelzi, hogy a LUIS észleli a `HomeAutomation.TurnOn` szándék, mint a felső célt és a `HomeAutomation.Room` értékű entitást `bedroom`.

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

3. Módosítsa az URL-címben található `q=` paraméter értékét `turn off the living room light` értékre, majd nyomja le az Enter billentyűt. Most, az eredmény, hogy a LUIS észlelt a `HomeAutomation.TurnOff` szándék, mint a felső célt és a `HomeAutomation.Room` értékkel rendelkező entitás `living room`. 

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
