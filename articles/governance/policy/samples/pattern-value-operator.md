---
title: 'Minta: Az értékoperátor egy házirend-definícióban'
description: Ez az Azure Policy minta egy példa arra, hogyan használhatja az értékoperátor t egy szabályzatdefinícióban.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: ace7b7cd4a765cdb8c7aa764b52b180c60508ab2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172785"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Policy minta: az értékoperátor

Az [értékoperátor](../concepts/definition-structure.md#value) [kiértékeli a paramétereket](../concepts/definition-structure.md#parameters), [a támogatott sablonfüggvényeket](../concepts/definition-structure.md#policy-functions)vagy a konstansokat egy adott [feltétel](../concepts/definition-structure.md#conditions)hez megadott értékre .

> [!WARNING]
> Ha egy _sablonfüggvény_ eredménye hiba, a házirend kiértékelése sikertelen lesz. A sikertelen kiértékelés implicit **megtagadás.** További információt a [sablonhibák elkerülése](../concepts/definition-structure.md#avoiding-template-failures)című témakörben talál.

## <a name="sample-policy-definition"></a>Mintaházirend-definíciója

Ez a házirend-definíció hozzáadja vagy lecseréli az erőforrások név (_karakterlánc_) paraméterében megadott **címkét,** és örökli a **tagName** értékét attól az erőforráscsoporttól, amelyben az erőforrás található. Ez a kiértékelés az erőforrás létrehozásakor vagy frissítésekor történik. [Módosítási](../concepts/effects.md#modify) hatásként a szervizelés a meglévő erőforrásokon egy [szervizelési feladaton](../how-to/remediate-resources.md)keresztül futtatható.

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>Magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

Az **értékoperátor** a **policyRule.if** blokkon belül használatos. **properties** Ebben a példában az **allOf** [logikai operátor](../concepts/definition-structure.md#logical-operators) azt adja ki, hogy mindkét feltételes utasításnak igaznak kell lennie a hatás, **a módosítás**, a hatás hoz létre.

**az érték** kiértékeli az [erőforráscsoport()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) sablonfüggvény eredményét egy üres érték **nemegyenlő** feltételére. Ha a tagName címkében megadott **címkenév** a szülő erőforráscsoporton létezik, a feltételes kiértékelés igaz.

## <a name="next-steps"></a>További lépések

- Tekintse át az egyéb [mintákat és a beépített definíciókat.](./index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).