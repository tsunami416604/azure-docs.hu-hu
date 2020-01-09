---
title: StorageAccountSelector FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Storage. StorageAccountSelector felhasználói felületi elemét ismerteti.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 4fcaf0e5842ce8a65175d2fc1dfa2483a1203b2f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651890"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft. Storage. StorageAccountSelector FELHASZNÁLÓIFELÜLET-elem

Egy új vagy meglévő Storage-fiók kiválasztására szolgáló vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája

A vezérlő megjeleníti az alapértelmezett értéket.

![Microsoft. Storage. StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

A vezérlő lehetővé teszi, hogy a felhasználó új Storage-fiókot hozzon létre, vagy válasszon ki egy meglévő Storage-fiókot.

![Microsoft. Storage. StorageAccountSelector új](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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

- Ha meg van adva, `defaultValue.name` automatikusan érvényesíti az egyediséget. Ha a Storage-fiók neve nem egyedi, a felhasználónak másik nevet kell megadnia, vagy egy meglévő Storage-fiókot kell választania.
- Az `defaultValue.type` alapértelmezett értéke **Premium_LRS**.
- A `constraints.allowedTypes`ban nem megadott típusok rejtettek, és a `constraints.excludedTypes`ban nem megadott típusok jelennek meg. `constraints.allowedTypes` és `constraints.excludedTypes` egyaránt választható, de nem használható egyszerre.
- Ha `options.hideExisting` **igaz**, a felhasználó nem választhat meglévő Storage-fiókot. Az alapértelmezett érték **false (hamis**).

## <a name="next-steps"></a>Következő lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
