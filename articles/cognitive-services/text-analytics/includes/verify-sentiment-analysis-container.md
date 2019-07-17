---
title: Ellenőrizze a Hangulatelemzés tárolópéldány
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ellenőrizheti a vélemények elemzése tárolópéldányt.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f69d573e9e70a505018e94cca354f363097cc1b8
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229272"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Ellenőrizze a Hangulatelemzés tárolópéldány

1. Válassza ki a **áttekintése** lapra, és másolja az IP-címet.
1. Nyisson meg egy új böngészőlapot, és adja meg az IP-címet. For example, adja meg `http://<IP-address>:5000 (http://55.55.55.55:5000`). A tároló kezdőlapja jelenik meg, amely közli, a tároló fut-e.

    ![Ellenőrizze, hogy fut-e a tároló kezdőlap megtekintése](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. Válassza ki a **szolgáltatás API-t leírása** lépjen az container swagger lapra mutató hivatkozást.

1. Válassza ki bármelyik a **POST** API-kat, és válassza ki **kipróbálás**.  Jelennek meg a paraméterek, többek között a példabemenet:

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

1. Cserélje le a bemenetben a következő JSON-tartalmak:

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

    A modell, amely a tárolót csomagolt egy 0 és 1, ahol 0 a negatív pedig 1 pozitív közötti pontszámot állít elő.

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

Most már tudjuk kapcsolhatja össze a dokumentum `id` , a válasz-adattartalomra JSON-adatok az eredeti kérelem hasznos dokumentumot `id`. Láthatjuk, hogy a pontszám, több mint `.98`, erősen pozitív vélemények jelzi.