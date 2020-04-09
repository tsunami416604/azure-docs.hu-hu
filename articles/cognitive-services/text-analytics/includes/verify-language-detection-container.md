---
title: A Nyelvfelismerés tárolópéldány ellenőrzése
titleSuffix: Azure Cognitive Services
description: További információ a Nyelvi észlelési tárolópéldány ellenőrzéséről.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc52586550f89ddae147d79458584331ed984eea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876426"
---
### <a name="verify-the-language-detection-container-instance"></a>A Nyelvfelismerés tárolópéldány ellenőrzése

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

1. Állítsa a `true` **showStats-ot** a számára.

1. **A szöveg** hangulatának meghatározásához válassza a Végrehajtás lehetőséget.

    A tárolóba csomagolt modell 0 és 1 közötti pontszámot hoz létre, ahol a 0 negatív, az 1 pedig pozitív vélemény.

    A visszaadott JSON-válasz a frissített szövegbevitel hangulatát tartalmazza:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Most már korrelálhatunk a válasz hasznos teherjtaadatainak dokumentumait az `id`eredeti kérelem hasznos dokumentumokkal a megfelelő . Minden dokumentumot egymástól függetlenül kezelnek, amely különböző statisztikákat tartalmaz, például `characterCount` és `transactionCount`. Ezenkívül minden eredményül `detectedLanguages` kapott dokumentumban `iso6391Name`a `score` `name`, és minden egyes észlelt nyelvhez a tömb ötvan. Ha több nyelvet észlel, a `score` rendszer a legvalószínűbb nyelv meghatározására használja.