---
title: Az Azure SizeSelector felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Compute.SizeSelector felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: e5be5635964ebeedc7be4d1d1f5403e4d281b55c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64722347"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI element
Vezérlő kiválasztásakor egy-egy vagy több virtuálisgép-példányok méretei.

## <a name="ui-sample"></a>Felhasználói felület minta

A felhasználónál az alapértelmezett értékekkel, az elem definícióból választókat.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Miután kiválasztotta a vezérlőt, a felhasználó látja az elérhető méretek a kibontott nézetének.

![Microsoft.Compute.SizeSelector expanded](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

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
- `recommendedSizes` rendelkeznie kell legalább egy méretét. Az első javasolt mérete az alapértelmezett szolgál. Az elérhető méretek listáját a nem az ajánlott állapot szerint rendezve. A felhasználó kiválaszthatja az adott oszlop ajánlott állapotuk szerint rendezheti.
- Ha egy ajánlott mérete a kiválasztott helyen nem érhető el, a mérete automatikusan ki lesz hagyva. Ehelyett a következő ajánlott mérete szolgál.
- `constraints.allowedSizes` és `constraints.excludedSizes` mindkettő nem kötelező, de nem használható egyszerre. Az elérhető méretek listáját lehet meghatározni meghívásával [listában elérhető virtuális gépek méretei-előfizetéshez tartozó](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Nincs megadva a méretű a `constraints.allowedSizes` rejtett, és nincs megadva a méretű `constraints.excludedSizes` jelenik meg.
- `osPlatform` meg kell adni, és lehet **Windows** vagy **Linux**. A hardvereszközökre fordított költségek a virtuális gépek meghatározására szolgál.
- `imageReference` belső rendszerképeket, de a megadott külső képek van hagyva. A szoftverek díjait a virtuális gépek meghatározására szolgál.
- `count` Állítsa be a megfelelő szorzó elemének szolgál. Támogatja az állandó érték, például **2**, vagy egy másik elem dinamikus értéket, például `[steps('step1').vmCount]`. Az alapértelmezett érték **1**.
- A `numAvailabilityZonesRequired` 1, 2 vagy 3 lehet.
- Alapértelmezés szerint `hideDiskTypeFilter` van **hamis**. A lemez-szűrő lehetővé teszi, hogy a felhasználó összes lemeztípusok vagy csak SSD megtekintéséhez.

## <a name="sample-output"></a>Példa kimenet
```json
"Standard_D1"
```

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
