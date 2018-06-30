---
title: Az Azure StorageAccountSelector felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Storage.StorageAccountSelector felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: 5de536a562d234a4c463c862aedffc7c7ca5228d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112286"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI element
Válasszon egy új vagy meglévő tárfiókot tartozó vezérlőelem.

## <a name="ui-sample"></a>Felhasználói felület minta

A vezérlő látható az alapértelmezett érték.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

A vezérlő lehetővé teszi, hogy a felhasználó számára hozzon létre egy új tárfiókot, vagy válasszon egy meglévő tárfiókot.

![Új Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések
- Ha meg van adva, `defaultValue.name` egyedi-e automatikusan érvényesítése. Ha a tárfiók neve nem egyedi, a felhasználó adjon meg egy másik nevet, vagy egy meglévő tárfiókot.
- Az alapértelmezett érték `defaultValue.type` van **Premium_LRS**.
- Nincs megadva a bármilyen `constraints.allowedTypes` rejtett, és nincs megadva a bármilyen `constraints.excludedTypes` jelenik meg. `constraints.allowedTypes` és `constraints.excludedTypes` mindkettő nem kötelező, de nem használható egyszerre.
- Ha `options.hideExisting` van **igaz**, a felhasználó egy meglévő tárfiók nem választható. Az alapértelmezett érték **hamis**.

## <a name="sample-output"></a>Példa kimenet

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
