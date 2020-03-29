---
title: Tanulási szabályzat - Személyre szabó
description: A tanulási beállítások határozzák meg a *modellbetanítás hiperparamétereit.* Két modell az azonos adatok, amelyek bevannak tanítva a különböző tanulási beállítások a végén különböző.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219345"
---
# <a name="learning-policy-and-settings"></a>Tanulási szabályzat és beállítások

A tanulási beállítások határozzák meg a *modellbetanítás hiperparamétereit.* Két modell az azonos adatok, amelyek bevannak tanítva a különböző tanulási beállítások a végén különböző.

[A tanulási szabályzat és a beállítások](how-to-settings.md#configure-rewards-for-the-feedback-loop) az Azure Portalon a Personalizer erőforráson vannak beállítva.

## <a name="import-and-export-learning-policies"></a>Tanulási irányelvek importálása és exportálása

Az Azure Portalról importálhat és exportálhat tanulási szabályzatfájlokat. Ezzel a módszerrel mentheti a meglévő házirendeket, tesztelheti őket, lecserélheti őket, és archiválhatja őket a forráskód-vezérlőben, mint összetevőket későbbi hivatkozásés naplózás.

Ismerje [meg, hogyan importálhat](how-to-manage-model.md#import-a-new-learning-policy) és exportálhat tanulási szabályzatot az Azure Portalon a Personalizer-erőforráshoz.

## <a name="understand-learning-policy-settings"></a>A tanulási házirend beállításainak ismertetése

A tanulási szabályzat beállításai nem módosíthatók. Csak akkor módosítsa a beállításokat, ha tisztában van azzal, hogy ezek hogyan befolyásolják a Personalizert. E nélkül a tudás nélkül problémákat okozhat, beleértve a Personalizer modellek érvénytelenítését.

A Personalizer [vowpalwabbit](https://github.com/VowpalWabbit) segítségével edzi és pontzi az eseményeket. Tekintse meg a [vowpalwabbit dokumentációt](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) arról, hogyan lehet a tanulási beállításokat a vowpalwabbit használatával szerkeszteni. Miután rendelkezik a megfelelő parancssori argumentumokkal, mentse a parancsot egy fájlba a következő formátumban (cserélje le az argumentumok tulajdonság értékét a kívánt paranccsal), és töltse fel a fájlt a **tanulási** beállítások importálásához az Azure Portalon a Personalizer erőforráshoz.

Az `.json` alábbiakban egy tanulási szabályzat ot mutatunk be.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Tanulási irányelvek összehasonlítása

Offline [értékelésekkel](concepts-offline-evaluation.md)összehasonlíthatja, hogy a különböző tanulási irányelvek hogyan viszonyulnak a korábbi adatokhoz a Personalizer naplókban.

[Töltse fel saját tanulási szabályzatait,](how-to-manage-model.md) hogy összehasonlíthassa őket a jelenlegi tanulási szabályzattal.

## <a name="optimize-learning-policies"></a>Tanulási irányelvek optimalizálása

A Personalizer offline kiértékelésben hozhat létre optimalizált tanulási [szabályzatot.](how-to-offline-evaluation.md) Az optimalizált tanulási szabályzat, amely jobb jutalmakat egy offline értékelés jobb eredményeket, ha használják online Personalizer.

Miután optimalizálta a tanulási szabályzatot, közvetlenül alkalmazhatja azt a Personalizer-ra, így azonnal lecseréli az aktuális szabályzatot. Vagy mentheti az optimalizált szabályzatot további kiértékelésre, és később eldöntheti, hogy elveti, menti vagy alkalmazza azt.

## <a name="next-steps"></a>További lépések

* Ismerje meg [az aktív és inaktív eseményeket.](concept-active-inactive-events.md)
