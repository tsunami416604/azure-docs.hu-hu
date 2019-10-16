---
title: Az Azure SizeSelector felhasználói felületének eleme | Microsoft Docs
description: Ismerteti a Microsoft. számítás. SizeSelector felhasználói felületi elemét Azure Portal. A virtuális gép méretének kiválasztásához használható.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 288ea7e887a170c8560b0126fa53c9132da35db6
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332671"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft. számítás. SizeSelector FELHASZNÁLÓIFELÜLET-elem
Egy vagy több virtuálisgép-példány méretének kiválasztására szolgáló vezérlőelem.

## <a name="ui-sample"></a>Felhasználói felület mintája

A felhasználó az elem definíciójában alapértelmezett értékekkel látja el a választót.

![Microsoft. számítás. SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

A vezérlő kiválasztása után a felhasználó az elérhető méretek kibővített nézetét láthatja.

![Microsoft. számítás. SizeSelector kibontva](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>Séma
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések
- a `recommendedSizes` értéknek legalább egy mérettel kell rendelkeznie. A rendszer az első ajánlott méretet használja alapértelmezettként. Az elérhető méretek listája nem a javasolt állapot szerint van rendezve. A felhasználó az ajánlott állapot alapján kiválaszthatja az adott oszlopot.
- Ha a kiválasztott helyen nem érhető el javasolt méret, a rendszer automatikusan kihagyja a méretet. Ehelyett a következő javasolt méretet használjuk.
- `constraints.allowedSizes` és `constraints.excludedSizes` egyaránt választható, de nem használható egyszerre. Az elérhető méretek listája az [előfizetéshez tartozó elérhető virtuálisgép-méretek](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)meghívásával határozható meg. A `constraints.allowedSizes` nem megadott méret rejtett, és a `constraints.excludedSizes` értékben nem megadott méret jelenik meg.
- meg kell adni a `osPlatform` értéket, és lehet **Windows** vagy **Linux**. A virtuális gépek hardveres költségeinek meghatározására szolgál.
- a `imageReference` kimaradt az első féltől származó rendszerképekhez, de a harmadik féltől származó rendszerképekhez biztosítva van. A virtuális gépek szoftveres költségeinek meghatározására szolgál.
- a `count` a elem megfelelő szorzójának megadására szolgál. Egy statikus értéket, például **2**vagy egy másik elemből származó dinamikus értéket támogat, például `[steps('step1').vmCount]`. Az alapértelmezett érték **1**.
- A `numAvailabilityZonesRequired` lehet 1, 2 vagy 3.
- Alapértelmezés szerint a `hideDiskTypeFilter` **hamis**. A lemez típusa szűrő lehetővé teszi a felhasználó számára az összes lemez típusának vagy csak SSD megjelenítését.

## <a name="sample-output"></a>Példa kimenet
```json
"Standard_D1"
```

## <a name="next-steps"></a>Következő lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
