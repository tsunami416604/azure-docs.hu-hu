---
title: MultiStorageAccountCombo FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Storage. MultiStorageAccountCombo felhasználói felületi elemét ismerteti.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651877"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft. Storage. MultiStorageAccountCombo FELHASZNÁLÓIFELÜLET-elem

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

- A értéke egy `defaultValue.prefix` vagy több egész számmal van összefűzve a Storage-fiókok neveinek létrehozásához. Ha `defaultValue.prefix` például a **sa** és `count` a **2**, akkor a Storage-fiókok nevei **SA1** és **SA2** jönnek létre. A létrehozott Storage-fiókok neve automatikusan az egyediségre van érvényesítve.
- A Storage-fiókok nevei a lexicographically alapján jönnek `count`létre. Ha `count` például a 10, akkor a Storage-fiókok nevei két számjegyből állnak (01, 02, 03).
- Az `defaultValue.prefix` alapértelmezett értéke **Null**, és a `defaultValue.type` **Premium_LRS**.
- A nem meghatározott `constraints.allowedTypes` típusok rejtettek, és az itt `constraints.excludedTypes` megadott típusok láthatók. `constraints.allowedTypes`és `constraints.excludedTypes` mindkettő nem kötelező, de nem használható egyszerre.
- A Storage-fiókok nevének létrehozása mellett az `count` elemhez tartozó megfelelő szorzót is megadhatja. Egy statikus értéket, például **2**vagy egy másik elemből származó dinamikus értéket támogat, például: `[steps('step1').storageAccountCount]`. Az alapértelmezett érték **1**.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
