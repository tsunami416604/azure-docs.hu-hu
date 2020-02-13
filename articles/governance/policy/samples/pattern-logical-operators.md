---
title: 'Minta: logikai operátorok egy házirend-definícióban'
description: Ez a Azure Policy minta bemutatja, hogyan használhatók a logikai operátorok egy házirend-definícióban.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 8e57efaea81848c6b2d0188dbf3f91e06ed74c67
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172848"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure Policy minta: logikai operátorok

Egy házirend-definíció több feltételes utasítást is tartalmazhat. Előfordulhat, hogy minden utasításnak igaz értékűnek kell lennie, vagy csak néhánynak igaznak kell lennie. Ezeknek az igényeknek a támogatásához a nyelvnek [logikai operátorai](../concepts/definition-structure.md#logical-operators) vannak a **nem**, a **allOf**és a **anyOf**. Ezeket nem kötelező megadni, és összetett forgatókönyvek létrehozásához is beágyazható.

## <a name="sample-1-one-logical-operator"></a>1\. példa: egy logikai operátor

Ez a szabályzat-definíció kiértékeli a CosmosDB-fiókokat, hogy ellenőrizze, hogy az automatikus feladatátvétel és több írási hely van-e konfigurálva. Ha nem, a [naplózás](../concepts/effects.md#audit) elindítja és létrehoz egy naplóbejegyzést a nem megfelelő erőforrás létrehozásakor vagy frissítésekor.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>1\. példa: magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

A **' policyrule osztály. if** blokk egyetlen **allOf** használ annak biztosítására, hogy mindhárom feltétel igaz legyen.
Csak akkor, ha az összes ilyen feltétel igaz értékre van állítva, a **naplózási** effektus aktiválva van.

## <a name="sample-2-multiple-logical-operators"></a>2\. minta: több logikai operátor

Ez a házirend-definíció kiértékeli az erőforrásokat egy elnevezési mintához. Ha egy erőforrás nem egyezik, a rendszer [megtagadja](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>2\. minta: magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Ez a **' policyrule osztály. Ha** a Letiltás egyetlen **allOf**is tartalmaz, az egyes feltételek a **nem** logikai operátorral vannak becsomagolva. A **nem** logikai operátoron belüli feltételesség először kiértékeli, majd kiértékeli **, hogy a** teljes záradék igaz vagy hamis. Ha a logikai operátorok egyike **sem** igaz, a rendszer elindítja a házirend hatását.

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).