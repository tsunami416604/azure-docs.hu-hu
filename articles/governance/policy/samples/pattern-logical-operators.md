---
title: 'Minta: Logikai operátorok egy házirend-definícióban'
description: Ez az Azure-szabályzatminta példákat tartalmaz a logikai operátorok használatára egy szabályzatdefinícióban.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 8e57efaea81848c6b2d0188dbf3f91e06ed74c67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172848"
---
# <a name="azure-policy-pattern-logical-operators"></a>Azure Policy minta: logikai operátorok

A házirend-definíció több feltételes utasítást is tartalmazhat. Előfordulhat, hogy minden állításnak igaznak kell lennie, vagy csak néhányra van szüksége, hogy igaz legyen. Ezen igények kielégítésére a nyelv [logikai operátorokkal rendelkezik](../concepts/definition-structure.md#logical-operators) a **nem** **,**, és **anyOf .** Ezek nem kötelező, és lehet beágyazott összetett forgatókönyvek létrehozása.

## <a name="sample-1-one-logical-operator"></a>1. minta: Egy logikai operátor

Ez a házirend-definíció kiértékeli a CosmosDB-fiókokat, hogy lássa, konfigurálva vannak-e az automatikus feladatátvételek és a több írási helyek. Ha nem, a [naplózás](../concepts/effects.md#audit) eseményindítók, és létrehoz egy naplóbejegyzést, amikor a nem megfelelő erőforrás jön létre vagy frissíthető.

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json":::

### <a name="sample-1-explanation"></a>1. minta: Magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-1.json" range="6-22" highlight="3":::

A **policyRule.if** blokk egyetlen **allOf-t** használ annak biztosítására, hogy mindhárom feltétel teljesüljön.
Csak akkor, ha az összes ilyen feltételek kiértékelése igaz nem a **naplózási** hatás kiváltó.

## <a name="sample-2-multiple-logical-operators"></a>2. minta: Több logikai operátor

Ez a házirend-definíció kiértékeli az erőforrásokat egy elnevezési minta. Ha egy erőforrás nem egyezik, a [tag adott.](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json":::

### <a name="sample-2-explanation"></a>2. minta: Magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-logical-operators-2.json" range="7-21" highlight="2,3,9":::

Ez **a policyRule.if** blokk egyetlen **allOf**-ot is tartalmaz, de minden feltétel a **nem** logikai operátorral van csomagolva. A **nem** logikai operátoron belüli feltételes érték kiértékeli először, majd nem határozza **meg,** hogy a teljes záradék igaz vagy hamis. Ha mindkét **nem** logikai operátorok kiértékelése igaz, a házirend-hatás aktiválódik.

## <a name="next-steps"></a>További lépések

- Tekintse át az egyéb [mintákat és a beépített definíciókat.](./index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).