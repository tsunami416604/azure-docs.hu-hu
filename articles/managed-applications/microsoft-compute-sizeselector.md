---
title: Az Azure SizeSelector felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Compute.SizeSelector felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: 9009d29e281ace179ad1dd2021c7cf35e3dc611a
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084807"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI element
Egy vagy több virtuálisgép-példányok méretét kiválasztására szolgáló vezérlő.

## <a name="ui-sample"></a>Felhasználói felület minta

A felhasználó megkeresheti a választó, az elem definíciója alapértelmezett értékeivel.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

A vezérlőelem kijelölése után a felhasználó megkeresheti a rendelkezésre álló méretű kibontott nézetének.

![Bővített Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

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
- `recommendedSizes` rendelkeznie kell legalább egy méretét. Az első ajánlott mérete alapértelmezés szerint használt. Elérhető méretek listáját a nem az ajánlott állapot szerint rendezve. A felhasználó választhat az adott oszlop ajánlott állapot szerint rendezheti.
- A kijelölt helyre ajánlott méret nem érhető el, ha a mérete automatikusan a rendszer kihagyja. Ehelyett a következő ajánlott méret szolgál.
- `constraints.allowedSizes` és `constraints.excludedSizes` mindkettő nem kötelező, de nem használható egyszerre. Az elérhető méretek listáját meghívásával lehet meghatározni [érhető el virtuális gépek méretét az előfizetéshez listában](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Nincs megadva a tetszőleges méretű a `constraints.allowedSizes` rejtett, és nincs megadva a tetszőleges méretű `constraints.excludedSizes` látható.
- `osPlatform` meg kell adni, és lehet **Windows** vagy **Linux**. Határozza meg a virtuális gépek hardverköltségek szolgál.
- `imageReference` Nincs megadva a belső lemezképek, de a megadott külső képek. A virtuális gépek szoftverek költségeit meghatározására szolgál.
- `count` az elem a megfelelő szorzószáma beállítására használatos. Egy állandó érték, például támogatja **2**, vagy egy másik elem dinamikus értéket, például `[steps('step1').vmCount]`. Az alapértelmezett érték **1**.
- A `numAvailabilityZonesRequired` 1, 2 vagy 3 lehet.
- Alapértelmezés szerint `hideDiskTypeFilter` van **hamis**. A lemez szűrő lehetővé teszi, hogy a felhasználó számára a lemez legyen kijelölve, vagy csak SSD.

## <a name="sample-output"></a>Példa kimenet
```json
"Standard_D1"
```

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
