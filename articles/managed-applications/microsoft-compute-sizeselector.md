---
title: Az Azure SizeSelector felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Compute.SizeSelector felhasználói felületi elem ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: a38a5461e96d741b3a0d556990418e022afdb305
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI element
Egy vagy több virtuálisgép-példányok méretét kiválasztására szolgáló vezérlő.

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

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
- `recommendedSizes` tartalmaznia kell legalább egy méretét. Az első ajánlott mérete alapértelmezés szerint használt.
- Ha az ajánlott méretet a kiválasztott helyen nem érhető el, a mérete automatikusan a rendszer kihagyja. Ehelyett a következő ajánlott méret szolgál.
- Nincs megadva a tetszőleges méretű a `constraints.allowedSizes` rejtett, és nincs megadva a tetszőleges méretű `constraints.excludedSizes` látható.
`constraints.allowedSizes` és `constraints.excludedSizes` mindkettő nem kötelező, de nem használható egyszerre. Az elérhető méretek listáját meghívásával lehet meghatározni [érhető el virtuális gépek méretét az előfizetéshez listában](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region).
- `osPlatform` meg kell adni, és lehet **Windows** vagy **Linux**. Határozza meg a virtuális gépek hardverköltségek szolgál.
- `imageReference` Nincs megadva a belső lemezképek, de a megadott külső képek. A virtuális gépek szoftverek költségeit meghatározására szolgál.
- `count` az elem a megfelelő szorzószáma beállítására használatos. Egy állandó érték, például támogatja **2**, vagy egy másik elem dinamikus értéket, például `[steps('step1').vmCount]`. Az alapértelmezett érték **1**.
- A `numAvailabilityZonesRequired` 1, 2 vagy 3 lehet.

## <a name="sample-output"></a>Példa kimenet
```json
"Standard_D1"
```

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
