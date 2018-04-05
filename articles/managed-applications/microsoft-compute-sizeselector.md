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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 3966de95233f32a09d4799630632c2bb6a490d78
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
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
    "excludedSizes": []
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

## <a name="sample-output"></a>Példa kimenet
```json
"Standard_D1"
```

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
