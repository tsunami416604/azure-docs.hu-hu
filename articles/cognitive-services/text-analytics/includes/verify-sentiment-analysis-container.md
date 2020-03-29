---
title: A Hangulatelemzés tárolópéldány ellenőrzése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ellenőrizheti a hangulatelemzés tárolópéldányát.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 2e201b4ec0d1364ea99b376171efabad65af0a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70968580"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>A Hangulatelemzés tárolópéldány ellenőrzése

1. Válassza az **Áttekintés** lapot, és másolja az IP-címet.
1. Nyisson meg egy új böngészőlapot, és írja be az IP-címet. Írja be `http://<IP-address>:5000 (http://55.55.55.55:5000`például a ). Megjelenik a tároló kezdőlapja, amely tudatja önnel, hogy a tároló fut.

    ![A tároló kezdőlapjának megtekintése annak ellenőrzéséhez, hogy fut-e](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Válassza ki a **Service API Description** hivatkozást a tároló Swagger lapjára való ugráshoz.

1. Válassza ki bármelyik **POST** API-t, és válassza **a Kipróbálás**lehetőséget . Megjelennek a paraméterek, amelyek a következő példabevitelt tartalmazzák:

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

1. Állítsa a `true` **showStats-ot** a számára.

1. **A szöveg** hangulatának meghatározásához válassza a Végrehajtás lehetőséget.

    A tárolóba csomagolt modell 0 és 1 közötti pontszámot hoz létre, ahol a 0 negatív, az 1 pedig pozitív vélemény.

    A visszaadott JSON-válasz a frissített szövegbevitel hangulatát tartalmazza:

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

Most már korrelálhatjuk a válasz hasznos adatainak dokumentumát `id` az eredeti `id`kérelem hasznos dokumentumával. A pontszám több, mint `0.98` azt jelzi, egy nagyon pozitív hangulat.