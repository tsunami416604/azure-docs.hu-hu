---
title: StorageAccountSelector felhasználói felületi elem
description: A Microsoft.Storage.StorageAccountSelector UI elem ismertetése az Azure Portalon.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 4fcaf0e5842ce8a65175d2fc1dfa2483a1203b2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651890"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector felhasználói felületi elem

Új vagy meglévő tárfiók kiválasztásának vezérlőja.

## <a name="ui-sample"></a>Felhasználói felület minta

A vezérlő az alapértelmezett értéket mutatja.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

A vezérlő lehetővé teszi, hogy a felhasználó új tárfiókot hozzon létre, vagy válasszon ki egy meglévő tárfiókot.

![Microsoft.Storage.StorageAccountSelector új](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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

## <a name="sample-output"></a>Példa kimenet

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Megjegyzések

- Ha meg `defaultValue.name` van adva, a rendszer automatikusan ellenőrzi az egyediséget. Ha a tárfiók neve nem egyedi, a felhasználónak meg kell adnia egy másik nevet, vagy válasszon egy meglévő tárfiókot.
- Az alapértelmezett `defaultValue.type` érték **a Premium_LRS.**
- Minden olyan típus, `constraints.allowedTypes` amely nincs megadva, rejtett, és minden olyan típus, amely nincs `constraints.excludedTypes` megadva, megjelenik. `constraints.allowedTypes`és `constraints.excludedTypes` mindkettő választható, de nem használható egyszerre.
- Ha `options.hideExisting` **igaz,** a felhasználó nem választhat meglévő tárfiókot. Az alapértelmezett érték: **hamis**.

## <a name="next-steps"></a>További lépések
* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
