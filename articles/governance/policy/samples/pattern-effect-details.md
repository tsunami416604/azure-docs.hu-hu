---
title: 'Minta: A házirend-definíció hatásai'
description: Ez az Azure-szabályzatminta példát mutat be a szabályzatdefiníciók különböző hatásainak használatára.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 1a9aec50bd328b76271d54f7830c75e0848d3cde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372634"
---
# <a name="azure-policy-pattern-effects"></a>Azure-szabályzat minta: hatások

Az Azure Policy számos [hatással](../concepts/effects.md) rendelkezik, amelyek meghatározzák, hogy a szolgáltatás hogyan reagál a nem megfelelő erőforrásokra. Egyes effektusok egyszerűek, és nem igényelnek további tulajdonságokat a házirend-definícióban, míg mások több tulajdonságot igényelnek.

## <a name="sample-1-simple-effect"></a>1. minta: Egyszerű hatás

Ez a házirend-definíció ellenőrzi, hogy a kiértékelt erőforráson létezik-e a **parameter tagName** paraméterben definiált címke. Ha a címke még nem létezik, a [módosítási](../concepts/effects.md#modify) hatás a címke hozzáadásához a parameter **tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>1. minta: Magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

A **módosítási** hatáshoz a **policyRule.then.details** blokkra van szükség, amely meghatározza **a roleDefinitionIds** és **a műveleteket.** Ezek a paraméterek tájékoztatják az Azure-szabályzatot arról, hogy milyen szerepkörök szükségesek a címke hozzáadásához és az erőforrás kiújításához, és melyik **módosítási** művelet végrehajtásához. Ebben a példában a **művelet** _hozzáadódik,_ és a paraméterek et a címke és az érték beállítására használják.

## <a name="sample-2-complex-effect"></a>2. minta: Komplex hatás

Ez a házirend-definíció minden virtuális gépet naplóz, ha egy bővítmény, amely a **közzétevő** és **a típus**paraméterben van definiálva, nem létezik. Az [auditIfNotExists](../concepts/effects.md#auditifnotexists) segítségével ellenőrzi a virtuális géphez kapcsolódó erőforrásokat, hogy egy példány létezik-e, amely megfelel a megadott paramétereknek. Ez a példa ellenőrzi a **bővítmények** típusát.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>2. minta: Magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Az **auditIfNotExists** hatás megköveteli, hogy a **policyRule.then.details** blokk definiáljon egy **típust** és a **existenceCondition-t** is. A **existenceCondition** házirendnyelvi elemeket, például [logikai operátorokat](../concepts/definition-structure.md#logical-operators)használ annak meghatározására, hogy létezik-e megfelelő kapcsolódó erőforrás. Ebben a példában az egyes [aliasokkal](../concepts/definition-structure.md#aliases) összevetett értékek paraméterekben vannak definiálva.

## <a name="next-steps"></a>További lépések

- Tekintse át az egyéb [mintákat és a beépített definíciókat.](./index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).