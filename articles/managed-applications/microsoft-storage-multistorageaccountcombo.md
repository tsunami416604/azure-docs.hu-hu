---
title: Az Azure MultiStorageAccountCombo felhasználói felületének eleme | Microsoft Docs
description: A Azure Portal Microsoft. Storage. MultiStorageAccountCombo felhasználói felületi elemét ismerteti.
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
ms.openlocfilehash: e3fb6f474bfe56f54e6dc621a3893e184ebc71d9
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151437"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI element

Olyan vezérlők csoportja, amelyekben számos olyan Storage-fiók hozható létre, amelynek neve közös előtaggal kezdődik.

## <a name="ui-sample"></a>Felhasználói felület mintája

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

## <a name="sample-output"></a>Példa kimenet

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Megjegyzések

- `defaultValue.prefix` értékének összefűzése egy vagy több egész számmal történik a Storage-fiókok nevének létrehozásához. Ha például `defaultValue.prefix` **sa** és **`count`, akkor a Storage**-fiókok nevei **SA1** és **SA2** jönnek létre. A létrehozott Storage-fiókok neve automatikusan az egyediségre van érvényesítve.
- A Storage-fiókok nevei `count`alapján jönnek létre a lexicographically. Ha például `count` 10, akkor a Storage-fiókok nevei két számjegyből állnak (01, 02, 03).
- A `defaultValue.prefix` alapértelmezett értéke **Null**, és a `defaultValue.type` **Premium_LRS**.
- A `constraints.allowedTypes`ban nem megadott típusok rejtettek, és a `constraints.excludedTypes`ban nem megadott típusok jelennek meg. `constraints.allowedTypes` és `constraints.excludedTypes` egyaránt választható, de nem használható egyszerre.
- A Storage-fiókok nevének létrehozása mellett `count` az elem megfelelő szorzójának megadására szolgál. Egy statikus értéket, például **2**vagy egy másik elemből származó dinamikus értéket támogat, például `[steps('step1').storageAccountCount]`. Az alapértelmezett érték **1**.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
