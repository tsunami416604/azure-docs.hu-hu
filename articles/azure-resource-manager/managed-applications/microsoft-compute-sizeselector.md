---
title: SizeSelector felhasználói felület eleme
description: A cikk a Microsoft.Compute.SizeSelector UI elemet ismerteti az Azure Portalon. A virtuális gép méretének kiválasztására használható.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d98ddbb09ac4f1f933237b3288db7a0cb78380b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652046"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector felhasználói felületi elem

Egy vagy több virtuálisgép-példány méretének kiválasztására szolgáló vezérlő.

## <a name="ui-sample"></a>Felhasználói felület minta

A felhasználó egy alapértelmezett értékekkel rendelkező választót lát az elemdefinícióból.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

A vezérlő kiválasztása után a felhasználó a rendelkezésre álló méretek kibontott nézetét látja.

![Microsoft.Compute.SizeSelector kibontva](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

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

## <a name="sample-output"></a>Példa kimenet

```json
"Standard_D1"
```

## <a name="remarks"></a>Megjegyzések

- `recommendedSizes`legalább egy mérettel kell rendelkeznie. Az első ajánlott méret az alapértelmezett. Az elérhető méretek listája nem az ajánlott állapot szerint van rendezve. A felhasználó kiválaszthatja az oszlopot az ajánlott állapot szerint rendezve.
- Ha a kijelölt helyen nem érhető el az ajánlott méret, a rendszer automatikusan kihagyja a méretet. Ehelyett a következő ajánlott méretet használja a következő.
- `constraints.allowedSizes`és `constraints.excludedSizes` mindkettő választható, de nem használható egyszerre. Az elérhető méretek listája az [előfizetéshez elérhető virtuális gépméretek listájának](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)hívásával határozható meg. A nem megadott `constraints.allowedSizes` méret rejtett, és a nem `constraints.excludedSizes` megadott méret is megjelenik.
- `osPlatform`meg kell adni, és lehet **Windows** vagy **Linux**. A virtuális gépek hardverköltségeinek meghatározására szolgál.
- `imageReference`a külső képek esetében kimarad, de a külső felek től származó képekhez meg van adva. A virtuális gépek szoftverköltségeinek meghatározására szolgál.
- `count`az elem megfelelő szorzójának beállítására szolgál. Támogatja a statikus értéket, például **a 2**vagy a `[steps('step1').vmCount]`dinamikus értéket egy másik elemből, például . Az alapértelmezett érték **1**.
- A `numAvailabilityZonesRequired` lehet 1, 2 vagy 3.
- Alapértelmezés szerint `hideDiskTypeFilter` **hamis**. A lemeztípus-szűrő lehetővé teszi, hogy a felhasználó az összes lemeztípust vagy csak SSD-t lásson.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
