---
title: MultiStorageAccountCombo felhasználói felületi elem
description: A Microsoft.Storage.MultiStorageAccountCombo UI elem ismertetése az Azure Portalon.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651877"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo felhasználói felület

Több tárfiók létrehozására szolgáló vezérlőelemek csoportja, amelyek neve közös előtaggal kezdődik.

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

- A rendszer `defaultValue.prefix` egy vagy több egész számmal összefűzi az értéket a tárfióknevek sorrendjének létrehozásához. Ha például `defaultValue.prefix` **sa** `count` és **2,** akkor a tárfiók nevek **sa1** és **sa2** jönnek létre. A létrehozott tárfióknevek automatikusan frissülnek az egyediség érdekében.
- A tárfiók nevek jönnek lexikografikusan alapján `count`. Ha például `count` 10, akkor a tárfiók nevek kétjegyű egész számokkal végződnek (01, 02, 03).
- Az alapértelmezett `defaultValue.prefix` érték **null,** `defaultValue.type` a Premium_LRS érték pedig **a**.
- Minden olyan típus, `constraints.allowedTypes` amely nincs megadva, rejtett, és minden olyan típus, amely nincs `constraints.excludedTypes` megadva, megjelenik. `constraints.allowedTypes`és `constraints.excludedTypes` mindkettő választható, de nem használható egyszerre.
- A tárfióknevek `count` létrehozása mellett a megfelelő szorzó beállítása az elem. Támogatja a statikus értéket, például **a 2**vagy a `[steps('step1').storageAccountCount]`dinamikus értéket egy másik elemből, például . Az alapértelmezett érték **1**.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
