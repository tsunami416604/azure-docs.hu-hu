---
title: A LUIS előre összeállított entitások |} A Microsoft Docs
description: Ez a cikk az előre összeállított entitások, a Language Understanding Intelligent Services (LUIS) részét képező tartalmaz.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/15/2018
ms.author: cahann
ms.reviewer: diberry
ms.openlocfilehash: 0ef91019e713a708d02edd2e9b09450d64a7ef6b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222922"
---
# <a name="prebuilt-entities"></a>Előre összeállított entitások

LUIS az általános típusú adatok, például a dátumok, időpontok, számok, mértékek és pénznem FELISMERVE előre összeállított entitások egy készletét tartalmazza. Előre összeállított entitások támogatása a LUIS-alkalmazás kulturális környezete eltérő. A LUIS támogatja, beleértve támogatási kulturális környezet, előre összeállított entitások teljes listáját lásd: a [előre összeállított entitások bemutatása](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> **Builtin.DateTime** elavult. A rendszer felülírja [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), dátum és idő címtartományok recognition, valamint továbbfejlesztett elismerését nem egyértelmű dátumok és időpontok biztosítja.

## <a name="add-a-prebuilt-entity"></a>Egy előre létrehozott entitás hozzáadása

1. Nyissa meg az alkalmazás nevére kattintva **saját alkalmazások** lapon, és kattintson a **entitások** a bal oldalon. 
2. Az a **entitások** kattintson **előre összeállított entitások kezelése**.

    ![Entitások lap – előre összeállított entitások kezelése](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. A **előre összeállított entitások hozzáadása** párbeszédpanelen kattintson az előre létrehozott entitást szeretne hozzáadni (például "datetimeV2"). Ezután kattintson a **Save** (Mentés) gombra.

    ![Előre összeállított entitások párbeszédpanel hozzáadása](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>Egy előre elkészített számú entitást használja
Ha az alkalmazás tartalmaz egy előre összeállított entitások, az előrejelzéseket a közzétett alkalmazás szerepelnek. Előre összeállított entitások viselkedését előre betanított és **nem** módosítható. Kövesse az alábbi lépéseket, hogy egy előre összeállított entitások működését:

1. Adjon hozzá egy **szám** entitást az alkalmazáshoz, majd [Train](luis-interactive-test.md) és [közzététele](luis-how-to-publish-app.md) az alkalmazást.
2. Kattintson a végpont URL-cím a **App közzététele** megnyitásához a LUIS-végpont egy webböngészőben. 
3. Az URL-cím, amely tartalmaz egy numerikus kifejezés hozzáfűzése egy utterance (kifejezés). Például beírhatja a `buy two plane ticktets`, és láthatja, hogy a LUIS azonosítja `two` , egy `builtin.number` entitás, és azonosítja az `2` alelem értékként a a `resolution` mező. A `resolution` mező segít a számok és dátumok feloldani a kanonikus formájában, amely egyszerűbb használata az ügyfélalkalmazás számára. 

    ![egy szám entitást tartalmazó böngészőben a utterance (kifejezés)](./media/luis-use-prebuilt-entity/browser-query.png)

A LUIS intelligensen képes felismerni a számokat, amelyek nem szerepelnek a nem szabványos űrlap. Próbálja ki a kimondott szöveg különböző numerikus kifejezést, és tekintse meg a LUIS adja vissza.

Az alábbi példa bemutatja, amely tartalmazza a felbontást az utterance (kifejezés) "két tucat" értékének 24, LUIS, a JSON-választ.

```json
{
  "query": "order two dozen tickets for group travel",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.905443209
  },
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```
## <a name="use-a-prebuilt-datetimev2-entity"></a>Egy előre elkészített datetimeV2 entitás használja
A **datetimeV2** előre összeállított entitások felismeri a dátumok, időpontok, dátumtartományokat és idő időtartamának összegénél. Kövesse az alábbi lépéseket, hogy hogyan a `datetimeV2` előre összeállított entitások működik:

1. Adjon hozzá egy **datetimeV2** entitást az alkalmazáshoz, majd [Train](luis-interactive-test.md) és [közzététele](luis-how-to-publish-app.md) az alkalmazást.
2. Kattintson a végpont URL-cím a **App közzététele** megnyitásához a LUIS-végpont egy webböngészőben. 
3. Az utterance (kifejezés) hozzáfűzni a dátumtartományt tartalmazó URL-cím. Például beírhatja a `book a flight tomorrow`, és láthatja, hogy a LUIS azonosítja `tomorrow` , egy `builtin.datetimeV2.date` entitás, és azonosítja az alelem értékként a holnapi dátumot a `resolution` mező. 

Az alábbi példa bemutatja a JSON-válasz, a LUIS néz Ha a mai dátum 2017 október 31-én.

```json
{
  "query": "book a flight tomorrow",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9063408
  },
  "entities": [
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 14,
      "endIndex": 21,
      "resolution": {
        "values": [
          {
            "timex": "2017-11-01",
            "type": "date",
            "value": "2017-11-01"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Előre összeállított entitások bemutatása](./luis-reference-prebuilt-entities.md)
