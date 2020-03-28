---
title: 'Minta: Paraméterek egy házirend-definícióban'
description: Ez az Azure Policy minta egy példa a paraméterek használatára egy szabályzatdefinícióban.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77172806"
---
# <a name="azure-policy-pattern-parameters"></a>Azure-szabályzat minta: paraméterek

A házirend-definíció dinamikussá tehető a paraméterek használatával szükséges házirend-definíciók számának csökkentése [érdekében.](../concepts/definition-structure.md#parameters) A paraméter a házirend-hozzárendelés során van definiálva. A paraméterek előre definiált tulajdonságokkal rendelkeznek, amelyek leírják a paramétert és annak módját.

## <a name="sample-1-string-parameters"></a>1. minta: Karakterlánc-paraméterek

Ez a házirend-definíció két paramétert használ, **a tagName-t** és a **tagValue-t** annak beállításához, hogy mit keres a házirend-hozzárendelés az erőforrásokon. Ez a formátum lehetővé teszi, hogy a házirend tetszőleges számú címkenév- és címkeérték-kombinációhoz használható legyen, de csak egyetlen házirend-definíciót tart fenn.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>1. minta: Magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

A házirend-definíció ezen részében a **tagName** paraméter _karakterláncként_ van definiálva, és a használathoz leírás van megadva.

A paraméter ezután a **policyRule.if** blokkban használatos a házirend dinamikussá hozásához. Itt a kiértékelt mező meghatározására szolgál, amely egy **címkeNév**.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>2. minta: Tömbparaméterek

Ez a házirend-definíció egyetlen paramétert, **a listOfBandwidthinMbps**paramétert használja annak ellenőrzésére, hogy az Express Route Circuit erőforrás konfigurálta-e a sávszélesség-beállítást a jóváhagyott értékek egyikére. Ha nem egyezik, az erőforrás létrehozása vagy frissítése [megtagadva.](../concepts/effects.md#deny)

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>2. minta: Magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

A házirend-definíció ezen részében a **listOfBandwidthinMbps** paraméter _tömbként_ van definiálva, és a használathoz leírás van megadva. Tömbként _array_több értékkel rendelkezik.

A paraméter ezután a **policyRule.if** blokkban használatos. _Tömbparaméterként_ _a_
[tömbfeltételt](../concepts/definition-structure.md#conditions)'s in or **notIn-t** kell használni. **in**
Itt a **serviceProvider.bandwidthInMbps** alias ellen használják, mint a definiált értékek egyikét.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>További lépések

- Tekintse át az egyéb [mintákat és a beépített definíciókat.](./index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).