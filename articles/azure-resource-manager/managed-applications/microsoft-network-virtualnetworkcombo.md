---
title: VirtualNetworkCombo UI elem
description: A cikk a Microsoft.Network.VirtualNetworkCombo UI elem ét ismerteti az Azure Portalon.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 53c9653b44a6c9d26d49d37b351cf6000676e2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651968"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo Felhasználói felület

Vezérlők csoportja új vagy meglévő virtuális hálózat kiválasztásához.

## <a name="ui-sample"></a>Felhasználói felület minta

Amikor a felhasználó új virtuális hálózatot választ, a felhasználó testreszabhatja az egyes alhálózatok nevét és címelőnevét. Az alhálózatok konfigurálása nem kötelező.

![Microsoft.Network.VirtualNetworkCombo új](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Amikor a felhasználó egy meglévő virtuális hálózatot választ, a felhasználónak le kell képeznie minden egyes alhálózatot, amelyhez a központi telepítési sablonszükséges egy meglévő alhálózathoz. Ebben az esetben az alhálózatok konfigurálása szükséges.

![Microsoft.Network.VirtualNetworkCombo meglévő](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

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

- Ha meg van adva, az első át nem `defaultValue.addressPrefixSize` fedő méretű címelőtag automatikusan meghatározásra kerül a felhasználó előfizetésében lévő meglévő virtuális hálózatok alapján.
- A(z) `defaultValue.name` `defaultValue.addressPrefixSize` alapértelmezett értéke **null**.
- `constraints.minAddressPrefixSize`meg kell adni. A megadott értéknél kisebb címtérrel rendelkező meglévő virtuális hálózatok nem választhatók ki.
- `subnets`meg kell adni, `constraints.minAddressPrefixSize` és minden alhálózathoz meg kell adni.
- Új virtuális hálózat létrehozásakor az egyes alhálózatok címelőtagja automatikusan kiszámításra kerül a `addressPrefixSize`virtuális hálózat címelőtagja és a megfelelő címelőtag alapján.
- Meglévő virtuális hálózat használata esetén a megfelelőnél `constraints.minAddressPrefixSize` kisebb alhálózatok nem választhatók ki. Továbbá, ha meg van adva, alhálózatok, amelyek `minAddressCount` nem rendelkeznek legalább elérhető címek nem érhetők el a kiválasztáshoz. Az alapértelmezett érték **0**. Annak érdekében, hogy a rendelkezésre **true** álló `requireContiguousAddresses`címek összefüggőek legyenek, adja meg a true értéket a számára. Az alapértelmezett érték **igaz**.
- Alhálózatok létrehozása egy meglévő virtuális hálózatban nem támogatott.
- Ha `options.hideExisting` **ez igaz,** a felhasználó nem választhat meglévő virtuális hálózatot. Az alapértelmezett érték: **hamis**.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
