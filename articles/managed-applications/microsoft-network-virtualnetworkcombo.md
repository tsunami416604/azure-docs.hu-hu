---
title: Az Azure VirtualNetworkCombo felhasználói felületének eleme | Microsoft Docs
description: A Azure Portal Microsoft. Network. VirtualNetworkCombo felhasználói felületi elemének ismertetése.
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
ms.openlocfilehash: d325230f603be4ccfe4fe42f1b58c6ad892fdb2c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151479"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI element

Új vagy meglévő virtuális hálózat kiválasztására szolgáló vezérlők csoportja.

## <a name="ui-sample"></a>Felhasználói felület mintája

Ha a felhasználó új virtuális hálózatot választ, a felhasználó testreszabhatja az egyes alhálózatok nevét és a hozzá tartozó előtagot. Az alhálózatok konfigurálása nem kötelező.

![Microsoft.Network.VirtualNetworkCombo new](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Ha a felhasználó egy meglévő virtuális hálózatot vesz fel, a felhasználónak le kell képeznie az egyes alhálózatokat, amelyekre a központi telepítési sablonnak szüksége van egy meglévő alhálózathoz. Ebben az esetben szükség van az alhálózatok konfigurálására.

![Microsoft.Network.VirtualNetworkCombo existing](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példa kimenet

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>Megjegyzések

- Ha meg van adva, a rendszer a felhasználó előfizetésének meglévő virtuális hálózatai alapján automatikusan meghatározza a `defaultValue.addressPrefixSize` méretének első nem átfedésben lévő előtagját.
- A `defaultValue.name` és az `defaultValue.addressPrefixSize` alapértelmezett értéke **Null**.
- meg kell adni a `constraints.minAddressPrefixSize`. A megadott értéknél kisebb címtartományt tartalmazó meglévő virtuális hálózatok nem választhatók ki a kijelöléshez.
- `subnets` meg kell adni, és minden alhálózathoz meg kell adni `constraints.minAddressPrefixSize`.
- Új virtuális hálózat létrehozásakor a rendszer az egyes alhálózatok címének előtagját automatikusan kiszámítja a virtuális hálózat címének előtagja és a megfelelő `addressPrefixSize`alapján.
- Meglévő virtuális hálózat használata esetén a megfelelő `constraints.minAddressPrefixSize`nál kisebb alhálózatok nem érhetők el a kiválasztáshoz. Emellett, ha meg van adva, az olyan alhálózatok, amelyek nem rendelkeznek legalább `minAddressCount` rendelkezésre álló címmel, nem érhetők el a kijelöléshez. Az alapértelmezett érték a **0**. Ha biztosítani szeretné, hogy az elérhető címek folytonos legyenek, a `requireContiguousAddresses`**igaz** értéket kell megadnia. Az alapértelmezett érték **true (igaz**).
- Az alhálózatok meglévő virtuális hálózatban való létrehozása nem támogatott.
- Ha `options.hideExisting` **igaz**, a felhasználó nem választhat meglévő virtuális hálózatot. Az alapértelmezett érték **hamis**.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
