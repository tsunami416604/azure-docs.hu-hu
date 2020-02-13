---
title: 'Minta: csoportházirend-definíciók kezdeményezésekkel'
description: Ez a Azure Policy minta egy példát mutat be a csoportházirend-definíciók kezdeményezésére
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 41c2b0cf3b8f677cdc408e85088c3ca6c2049d6b
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172855"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure Policy minta: csoportházirend-definíciók

A kezdeményezés szabályzat-definíciók csoportja. A kapcsolódó szabályzat-definíciók egyetlen objektumba csoportosításával egyetlen hozzárendelést hozhat létre, amely több hozzárendelés lenne.

## <a name="sample-initiative-definition"></a>Példa kezdeményezés definíciója

Ez a kezdeményezés két házirend-definíciót telepít, amelyek mindegyike a **TagName** és a **tagValue** paramétert veszi figyelembe. Maga a kezdeményezés két paraméterrel rendelkezik: **costCenterValue** és **productNameValue**.
Ezek a kezdeményezési paraméterek mindegyike a csoportosított házirend-definíciók mindegyikén elérhető. Ez a kialakítás maximalizálja a meglévő szabályzat-definíciók újbóli felhasználását, miközben korlátozza a létrehozott hozzárendelések számának igény szerinti megvalósítását.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>Magyarázat

#### <a name="initiative-parameters"></a>Kezdeményezési paraméterek

Egy kezdeményezés saját paramétereket adhat meg, amelyeket aztán a csoportosított házirend-definícióknak továbbítanak.
Ebben a példában a **costCenterValue** és a **productNameValue** is a kezdeményezési paraméterekként van definiálva. A rendszer a kezdeményezés hozzárendelésekor megadja az értékeket.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>Szabályzat-definíciókat tartalmaz

Minden belefoglalt szabályzat-definíciónak meg kell adnia a **policyDefinitionId** és a **Parameters** tömböt, ha a házirend-definíció paramétereket fogad el. Az alábbi kódrészletben a belefoglalt házirend definíciója két paramétert fogad: **TagName** és **tagValue**. a **TagName** literálal van definiálva, de a **tagValue** a **costCenterValue** által definiált paramétert használja. Az értékek áteresztése javítja az újrafelhasználást.

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>Következő lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).