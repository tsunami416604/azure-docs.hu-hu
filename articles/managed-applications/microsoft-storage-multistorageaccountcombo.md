---
title: Azure MultiStorageAccountCombo UI element | Microsoft Docs
description: Az Azure portal a Microsoft.Storage.MultiStorageAccountCombo felhasználói felületi elem ismerteti.
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
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 08b65770414e9ee1cb5e478427fe7654b2bb9a78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252457"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI element
Egy csoport vezérlők különböző storage-fiókok létrehozásához és a egy közös előtaggal kezdődik.

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
- Az érték `defaultValue.prefix` van kibővítve a tárfiókok nevének feladatütemezés létrehozásához legalább egy egész számok. Például ha `defaultValue.prefix` van **sa** és `count` van **2**, majd tárfiókneveket **sa1** és **sa2** jönnek létre. A létrehozott tárfiókok nevének automatikusan érvényesíti egyedi-e.
- A tárfiókok nevének lexicographically alapján jönnek létre `count`. Például ha `count` 10, akkor a tárfiókok nevének végén kétjegyű egész számok (01, 02, 03).
- Az alapértelmezett érték a `defaultValue.prefix` van **null**, és a `defaultValue.type` van **Premium_LRS**.
- Nincs megadva a bármilyen `constraints.allowedTypes` rejtett, és nincs megadva a bármilyen `constraints.excludedTypes` jelenik meg. `constraints.allowedTypes` és `constraints.excludedTypes` mindkettő nem kötelező, de nem használható egyszerre.
- Tárfiókok nevének, létrehozása mellett `count` elemének megfelelő szorzó beállítására szolgál. Támogatja az állandó érték, például **2**, vagy egy másik elem dinamikus értéket, például `[steps('step1').storageAccountCount]`. Az alapértelmezett érték **1**.

## <a name="sample-output"></a>Példa kimenet

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
