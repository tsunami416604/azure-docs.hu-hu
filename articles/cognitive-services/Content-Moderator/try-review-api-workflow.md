---
title: Moderálási munkafolyamatok definiálása a REST API konzollal – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Az Azure Content Moderator Review API-k segítségével egyéni munkafolyamatokat és küszöbértékeket határozhat meg a tartalomszabályzatai alapján.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754183"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Moderálási munkafolyamatok definiálása és használata (REST)

A munkafolyamatok felhőalapú, testre szabott szűrők, amelyek segítségével hatékonyabban kezelheti a tartalmat. A munkafolyamatok különböző szolgáltatásokhoz kapcsolódhatnak, hogy különböző módokon szűrjék a tartalmat, majd megtegyékeljék a megfelelő lépéseket. Ez az útmutató bemutatja, hogyan használhatja a REST munkafolyamat API-kat az API-konzolon keresztül munkafolyamatok létrehozásához és használatához. Miután megértette az API-k szerkezetét, könnyedén átviheti ezeket a hívásokat bármely REST-kompatibilis platformra.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be vagy hozzon létre fiókot a [Tartalommoderátor-ellenőrzési eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.

## <a name="create-a-workflow"></a>Munkafolyamat létrehozása

Munkafolyamat létrehozásához vagy frissítéséhez nyissa meg a **[Munkafolyamat – API létrehozása vagy frissítése](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** referencialapot, és válassza ki a kulcsrégió gombját (ezt a Végpont URL-címében, a Véleményezés [eszköz](https://contentmoderator.cognitive.microsoft.com/) **Hitelesítő adatok** lapján találja). Ez elindítja az API-konzolt, ahol könnyedén hozhat létre és futtathat REST API-hívásokat.

![Munkafolyamat – Lapterület kijelölésének létrehozása vagy frissítése](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST-hívás paramétereinek megadása

Adja meg a **csapat,** **a munkafolyamatnév**és az **Ocp-Apim-Subscription-Key értékeit:**

- **csapat**: Az a csapatazonosító, amelyet a [Véleményezés eszközfiók](https://contentmoderator.cognitive.microsoft.com/) beállításakor hozott létre (amely a Véleményezés eszköz Hitelesítő adatai képernyőjén az **Azonosító** mezőben található).
- **munkafolyamatneve**: A hozzáadni kívánt új munkafolyamat (vagy meglévő név neve, ha meglévő munkafolyamatot szeretne frissíteni) neve.
- **Ocp-Apim-Subscription-Key**: A tartalommoderátor kulcs. Ezt a [Véleményezés eszköz](https://contentmoderator.cognitive.microsoft.com) **Beállítások** lapján találja.

![Munkafolyamat – Konzollekérdezési paraméterek és -fejlécek létrehozása vagy frissítése](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Munkafolyamat-definíció megadása

1. A **Kérelem törzse** mező szerkesztésével adja meg a JSON-kérelmet a **Leírás** és **a Típus** (vagy `Image` vagy) `Text`részleteivel.
2. Kifejezés **esetén**másolja az alapértelmezett munkafolyamat JSON-kifejezést. Az utolsó JSON-húrnak így kell kinéznie:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Ezzel az API-val egyszerű, összetett és egyenletes beágyazott kifejezéseket definiálhat a munkafolyamatokhoz. A [munkafolyamat – Létrehozása vagy frissítése](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) dokumentáció példákat összetettebb logika.

### <a name="submit-your-request"></a>Küldje el kérését
  
Válassza a **Küldés**lehetőséget. Ha a művelet sikeres, a `200 OK`Válasz **állapota** a `true`, és a Válasz **tartalom** mezője megjelenik.

### <a name="examine-the-new-workflow"></a>Az új munkafolyamat vizsgálata

A [Véleményezés eszközben](https://contentmoderator.cognitive.microsoft.com/)válassza a **Beállítások** > **munkafolyamatok**lehetőséget. Az új munkafolyamatnak meg kell jelennie a listában.

![Munkafolyamatok eszközlistájának áttekintése](images/workflow-console-new-workflow.PNG)

Válassza a munkafolyamat **Szerkesztés beállítását,** és lépjen a **Tervező** lapra. Itt láthatja a JSON-logika intuitív ábrázolását.

![Tervező lap a kijelölt munkafolyamathoz](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Munkafolyamat-részletek beszerezni

Egy meglévő munkafolyamat részleteinek lekéréséhez nyissa meg a **[Munkafolyamat – API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** referencialap át, és válassza ki a régió (a kulcs felügyeletének régiójában) gombját.

![Munkafolyamat – Régiókijelölés beszereznie](images/test-drive-region.png)

Adja meg a REST hívás paramétereit a fenti szakaszban. Győződjön meg arról, hogy ez alkalommal a **munkafolyamatneve** egy meglévő munkafolyamat neve.

![Lekérdezési paraméterek és fejlécek beolvasása](images/workflow-get-default.PNG)

Válassza a **Küldés**lehetőséget. Ha a művelet sikeres, a `200 OK`Válasz **állapota** a , és a **Válasz tartalom** mezője a munkafolyamatot JSON formátumban jeleníti meg, a következőkhöz hasonlóan:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használhatók a munkafolyamatok [tartalommoderálási feladatokkal.](try-review-api-job.md)