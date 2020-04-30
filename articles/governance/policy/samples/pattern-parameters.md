---
title: 'Minta: a házirend-definícióban szereplő paraméterek'
description: Ez a Azure Policy minta azt szemlélteti, hogyan használhatók paraméterek egy házirend-definícióban.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 4921bb216ef67b561bc8986cf48239e6448beafc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77172806"
---
# <a name="azure-policy-pattern-parameters"></a>Azure Policy minta: paraméterek

A házirend-definíciót dinamikus értékre lehet állítani, hogy csökkentse a [Paraméterek](../concepts/definition-structure.md#parameters)használatával szükséges házirend-definíciók számát. A paraméter a házirend-hozzárendelés során van meghatározva. A paraméterek előre definiált tulajdonságokkal rendelkeznek, amelyek leírják a paramétert és azt, hogy hogyan használják azt.

## <a name="sample-1-string-parameters"></a>1. példa: karakterlánc-paraméterek

Ez a házirend-definíció két paramétert használ: **TagName** és **tagValue** , amelyekkel beállíthatja, hogy a szabályzat-hozzárendelés milyen erőforrásokon keres. Ez a formátum lehetővé teszi, hogy a szabályzat tetszőleges számú címkét és címke értékű kombinációt adjon meg, de csak egyetlen házirend-definíciót tart fenn.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>1. példa: magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

A házirend-definíció ezen részében a **TagName** paraméter _karakterláncként_ van definiálva, és a használathoz Leírás van megadva.

Ezt a paramétert a ' Policyrule osztály használja a rendszer **. Ha** a Letiltás letiltja a szabályzatot. Itt megadhatja a kiértékelt mezőt, amely a **TagName**értékkel rendelkező címke.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>2. minta: tömb paramétereinek

Ez a házirend-definíció egyetlen paramétert használ a **listOfBandwidthinMbps**, hogy ellenőrizze, hogy az Express Route Circuit-erőforrás beállította-e a sávszélesség-beállítást a jóváhagyott értékek egyikére. Ha nem egyezik, az erőforrás létrehozása vagy frissítése [megtagadva](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>2. minta: magyarázat

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

A házirend-definíció ezen részében a **listOfBandwidthinMbps** paraméter _tömbként_ van definiálva, és a használathoz Leírás van megadva. _Tömbként_több értéket kell megegyeznie.

Ezután a paramétert a ' Policyrule osztály fogja használni **. Ha** a blokkot használja. _Tömb_ paraméterként **a** _tömb_
[feltételeit](../concepts/definition-structure.md#conditions)vagy **notIn** kell használni.
Itt a rendszer a **szolgáltató. bandwidthInMbps** aliast használja a definiált értékek egyikére.

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="next-steps"></a>További lépések

- Tekintse át [a többi mintázatot és a beépített definíciókat](./index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).