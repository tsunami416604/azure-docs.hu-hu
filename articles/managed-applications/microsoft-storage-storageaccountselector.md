---
title: Az Azure StorageAccountSelector felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Storage.StorageAccountSelector felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: c6d4ef50645902aecd57ceb9fc48b7d99bf22d53
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104862"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI element
Vezérlő kiválasztásakor egy új vagy meglévő tárfiókot.

## <a name="ui-sample"></a>Felhasználói felület minta

A vezérlőelem megjeleníti az alapértelmezett érték.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

A vezérlő lehetővé teszi, hogy a felhasználó hozzon létre egy új tárfiókot, vagy válasszon ki egy meglévő tárfiókot.

![Microsoft.Storage.StorageAccountSelector new](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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
- Ha meg van adva, `defaultValue.name` egyedi-e automatikusan érvényesítve. Ha a tárfiók neve nem egyedi, a felhasználó kell adjon meg egy másik nevet vagy válasszon egy meglévő tárfiókot.
- Az alapértelmezett érték a `defaultValue.type` van **Premium_LRS**.
- Nincs megadva a bármilyen `constraints.allowedTypes` rejtett, és nincs megadva a bármilyen `constraints.excludedTypes` jelenik meg. `constraints.allowedTypes` és `constraints.excludedTypes` mindkettő nem kötelező, de nem használható egyszerre.
- Ha `options.hideExisting` van **igaz**, a felhasználó nem választhatja ki egy meglévő tárfiókot. Az alapértelmezett érték **hamis**.

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
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
