---
title: Előre elkészített entitásának LUIS |} Microsoft Docs
description: Ez a cikk a nyelvi ismertetése intelligens szolgáltatások (LUIS) szereplő előre elkészített entitások tartalmaz.
services: cognitive-services
author: cahann
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/15/2018
ms.author: cahann
ms.reviewer: v-geberr
ms.openlocfilehash: 4858de8b8517016ca385e9105ca8948b66aa683b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36322050"
---
# <a name="prebuilt-entities"></a>Előre elkészített entitások

LUIS olyan adatok, például a dátumok, idők, számok, mérések és pénznem általános típusú ismer fel az előre elkészített entitásokat tartalmaz. A kulturális környezet az LUIS alkalmazás függ a előre elkészített entitás támogatása. Az előre elkészített entitások támogató LUIS, kulturális környezet, beleértve a támogatási teljes listájáért lásd: a [előre elkészített entitáshivatkozás](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> **Builtin.DateTime** elavult. A rendszer felülírja [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), pedig a dátum és idő tartományok, valamint továbbfejlesztett felismerését a nem egyértelmű dátumok és időpontok biztosítja.

## <a name="add-a-prebuilt-entity"></a>Egy előre elkészített entitás hozzáadása

1. Nyissa meg az alkalmazás nevére kattintva a **saját alkalmazások** lapon, majd **entitások** a bal oldalon található. 
2. Az a **entitások** kattintson **előre elkészített entitások kezelése**.

    ![Entitások lap – az előre elkészített entitások kezelése](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. A **adja hozzá az előre elkészített entitások** párbeszédpanelen nyomja meg az előre elkészített entitás hozzá szeretne adni (például "datetimeV2"). Ezután kattintson a **Save** (Mentés) gombra.

    ![Adja hozzá az előre elkészített entitás párbeszédpanel](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## <a name="use-a-prebuilt-number-entity"></a>Egy előre elkészített számú entitás
Ha az alkalmazás egy előre elkészített entitást tartalmaz, az előrejelzés szerepelnek a közzétett alkalmazáshoz. Az előre elkészített entitások viselkedésének előre betanított és **nem** módosítható. Kövesse az alábbi lépéseket egy előre elkészített entitás működése megjelenítéséhez:

1. Adja hozzá a **száma** entitás az alkalmazáshoz, majd [vonat](interactive-test.md) és [közzététele](PublishApp.md) az alkalmazást.
2. Kattintson a végpont URL-cím a **alkalmazás közzététele** lapot a böngészőben nyissa meg a LUIS végpont. 
3. Egy utterance hozzáfűzése egy numerikus kifejezés tartalmazó URL-CÍMÉT. Például beírhatja a `buy two plane ticktets`, és ellenőrizze, hogy LUIS azonosító `two` , egy `builtin.number` entitást, és azonosítja `2` a értéke a `resolution` mező. A `resolution` mező segít a számok és dátumok feloldani egy kanonikus alakja, amely megkönnyíti a használandó ügyfélalkalmazást. 

    ![egy szám entitást tartalmazó böngészőben utterance](./media/luis-use-prebuilt-entity/browser-query.png)

Intelligens módon LUIS fel tudja ismerni a nem szabványos formában nem szám. Próbálja ki a utterances különböző numerikus kifejezést, és megtekintheti a LUIS adja vissza.

A következő példa bemutatja, amely tartalmazza a utterance "két tucat" értéke 24, felbontása LUIS, a JSON-választ.

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
## <a name="use-a-prebuilt-datetimev2-entity"></a>Egy előre elkészített datetimeV2 entitás
A **datetimeV2** előre elkészített entitás felismeri a dátumok, idők, dátumtartományok és idő időtartamok. Kövesse az alábbi lépéseket, hogy hogyan a `datetimeV2` előre elkészített entitás működik:

1. Adja hozzá a **datetimeV2** entitás az alkalmazáshoz, majd [vonat](interactive-test.md) és [közzététele](PublishApp.md) az alkalmazást.
2. Kattintson a végpont URL-cím a **alkalmazás közzététele** lapot a böngészőben nyissa meg a LUIS végpont. 
3. Egy utterance hozzáfűzése dátumtartomány tartalmazó URL-CÍMÉT. Például beírhatja a `book a flight tomorrow`, és ellenőrizze, hogy LUIS azonosító `tomorrow` , egy `builtin.datetimeV2.date` entitást, és az értéke jövő dátum azonosítja a `resolution` mező. 

A következő példa bemutatja, hogyan LUIS JSON válaszát nézhet Ha az aktuális dátum október 31-edik 2017.

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
> [Előre elkészített entitáshivatkozás](./luis-reference-prebuilt-entities.md)
