---
title: Tárolótámogatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ellenőrizheti a vélemények elemzése tárolópéldányt.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f68d9c7098f2b1ca782e2522c632c2e267b35336
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455136"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Ellenőrizze a Hangulatelemzés tárolópéldány

1. Válassza ki a **áttekintése** lapra, és másolja az IP-címet.
1. Nyisson meg egy új böngészőlapot, és az IP-címet, például használja `http://<IP-address>:5000 (http://55.55.55.55:5000`). A tároló kezdőlapja jelenik meg, amely közli, a tároló fut-e.

    ![A tároló kezdőlapra, és ellenőrizze, hogy fut-e megtekintése](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Válassza ki a **szolgáltatás API-t leírása** navigáljon a tárolók swagger lapra mutató hivatkozást.

1. Válassza ki bármelyik a **POST** API-kat, és válassza ki **kipróbálás**.  A paraméterek jelennek meg többek között a példabemenet:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Cserélje le a bemeneti adatok az alábbi JSON-ra:

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

1. Állítsa be **showStats** igaz értékre.

1. Válassza ki **Execute** meghatározni a szöveg a róluk szóló véleményeket.

    A modell a tárolót csomagolt egy 0 és 1, ahol 0 a negatív pedig 1 pozitív közötti pontszámot állít elő.

    A visszaadott JSON-válasz tartalmazza a frissített szövegbevitel vélemények:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
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

Most már tudjuk kapcsolhatja össze a dokumentum `id` az eredeti kérelem hasznos dokumentumot a válasz hasznos JSON- `id`, és tekintse meg, hogy hiba történt a pontszámot keresztül `.98` nagyon pozitív vélemények jelző.