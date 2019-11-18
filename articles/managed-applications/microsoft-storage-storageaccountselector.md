---
title: Az Azure StorageAccountSelector felhasználói felületének eleme | Microsoft Docs
description: A Azure Portal Microsoft. Storage. StorageAccountSelector felhasználói felületi elemét ismerteti.
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
ms.openlocfilehash: e1f96b42e58bcb09cfc2836c993626a889669fc0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151460"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI element

Egy új vagy meglévő Storage-fiók kiválasztására szolgáló vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája

A vezérlő megjeleníti az alapértelmezett értéket.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

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
- Ha `options.hideExisting` **igaz**, a felhasználó nem választhat meglévő Storage-fiókot. Az alapértelmezett érték **hamis**.

## <a name="next-steps"></a>További lépések
* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
