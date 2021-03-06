---
title: A Kulcsszókeresés tároló példányának ellenőrzése
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan ellenőrizheti a Kulcsszókeresés tároló példányát.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5dca4828a5c1127133461ddf9fc06099fc176b68
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009913"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>A Kulcsszókeresés tároló példányának ellenőrzése

1. Válassza az **Áttekintés** lapot, és másolja ki az IP-címet.
1. Nyisson meg egy új böngésző fület, és adja meg az IP-címet. Írja be például a `http://<IP-address>:5000 (http://55.55.55.55:5000` következőt:). Megjelenik a tároló kezdőlapja, amelyből megtudhatja, hogy fut-e a tároló.

    ![A tároló kezdőlapjának megtekintése annak ellenőrzéséhez, hogy fut-e](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Válassza ki a **szolgáltatás API leírása** hivatkozást, hogy megnyissa a tároló hencegő lapját.

1. Válassza ki a **post** API-k bármelyikét, és válassza a **kipróbálás** lehetőséget. A paraméterek megjelennek, amely tartalmazza a következő bemeneti példát:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. Cserélje le a bemenetet a következő JSON-tartalomra:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. **ShowStats** beállítása a következőre: `true` .

1. A szöveg hangulatának meghatározásához válassza a **végrehajtás** lehetőséget.

    A tárolóban csomagolt modell egy 0 és 1 közötti értéket generál, ahol a 0 negatív, az 1 pedig pozitív.

    A visszaadott JSON-válasz magában foglalja a frissített szövegbeviteli adatok hangulatát:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Most már összekapcsolhatja a `id` Válasz adattartalom JSON-adatainak dokumentumát az eredeti kérelem adattartalma dokumentumával `id` . Az eredményül kapott dokumentum egy `keyPhrases` tömböt tartalmaz, amely a megfelelő bemeneti dokumentumból kinyert legfontosabb kifejezések listáját tartalmazza. Emellett számos statisztika létezik, például `characterCount` `transactionCount` a és az egyes létrejövő dokumentumokhoz.