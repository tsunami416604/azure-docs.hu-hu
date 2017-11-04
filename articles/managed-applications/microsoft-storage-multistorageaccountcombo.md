---
title: "Az Azure által felügyelt alkalmazás MultiStorageAccountCombo felhasználói felületi elem |} Microsoft Docs"
description: "A témakör ismerteti a Microsoft.Storage.MultiStorageAccountCombo felhasználói felületi elem Azure által felügyelt alkalmazások"
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
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: e1dda7917988146807ca6cfab10a3a4eac0b7bb2
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo felhasználói felületi elem
Több tárfiókot, a közös előtaggal kezdődő neveket való létrehozásának vezérlők egy csoportja. Ez az elem használata során [Azure által felügyelt alkalmazások létrehozására](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Séma
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések
- A következő `defaultValue.prefix` tárfiókneveket sorozatát létrehozásához legalább egy egész számokat van kibővítve. Például ha `defaultValue.prefix` van **foobar** és `count` van **2**, majd tárfiókok neve **foobar1** és **foobar2** jönnek létre. Létrehozott tárfiókok neve automatikusan érvényesíti egyedi-e.
- A tárfiókok neve lexicographically alapján generált `count`. Például ha `count` 10, akkor a tárfiókok neve végződhet 2 számjegyből álló egész számok (01, 02, 03, stb.).
- Az alapértelmezett érték `defaultValue.prefix` van **null**, és a `defaultValue.type` van **Premium_LRS**.
- Nincs megadva a bármilyen `constraints.allowedTypes` rejtett, és nincs megadva a bármilyen `constraints.excludedTypes` jelenik meg.
`constraints.allowedTypes`és `constraints.excludedTypes` mindkettő nem kötelező, de nem használható egyszerre.
- Tárfiókok neve, generálása mellett `count` elemhez a megfelelő többszöröző beállítására szolgál. Egy állandó érték, például támogatja **2**, vagy egy másik elem dinamikus értéket, például `[steps('step1').storageAccountCount]`. Az alapértelmezett érték **1**.

## <a name="sample-output"></a>Példa kimenet
```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>Következő lépések
* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](overview.md).
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
