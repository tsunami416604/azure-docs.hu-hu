---
title: Az Azure VirtualNetworkCombo felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Network.VirtualNetworkCombo felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: b0437338b403ff19761173d08be3938d07f13f55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708349"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI element
Vezérlők egy új vagy meglévő virtuális hálózat kiválasztása csoportja.

## <a name="ui-sample"></a>Felhasználói felület minta
Amikor a felhasználó kiválasztja az új virtuális hálózat, a felhasználó szabhatja testre, minden egyes alhálózat nevét és a cím előtagja. Alhálózatok konfigurálása nem kötelező.

![Microsoft.Network.VirtualNetworkCombo new](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

Amikor a felhasználó kiválasztja a meglévő virtuális hálózattal, a felhasználó egy létező alhálózathoz képezze le a központi telepítési sablont igényel minden alhálózathoz. Alhálózatok konfigurálása ebben az esetben szükség.

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

## <a name="remarks"></a>Megjegyzések
- Ha meg van adva, az első egymást nem átfedő cím előtagja méretű `defaultValue.addressPrefixSize` automatikusan a meglévő virtuális hálózatok, a felhasználói előfizetés alapján határozza meg.
- Az alapértelmezett érték a `defaultValue.name` és `defaultValue.addressPrefixSize` van **null**.
- `constraints.minAddressPrefixSize` meg kell adni. Az egy kisebb, mint a megadott érték címtér bármely meglévő virtuális hálózatok nem lehet kiválasztani.
- `subnets` meg kell adni, és `constraints.minAddressPrefixSize` minden alhálózat számára meg kell adni.
- Új virtuális hálózat létrehozásakor minden egyes alhálózati cím előtag alapján lesz kiszámítva automatikusan a virtuális hálózat címelőtagjához és a megfelelő `addressPrefixSize`.
- Ha egy meglévő virtuális hálózat, esetleges olyan alhálózatokra, amelyek kisebb, mint a megfelelő `constraints.minAddressPrefixSize` kijelölés nem érhető el. Emellett ha meg van adva, alhálózatok, amelyek nem rendelkeznek legalább `minAddressCount` elérhető címek nem lehet kiválasztani. Az alapértelmezett érték **0**. Adja meg annak érdekében, hogy az elérhető címek összefüggő, **igaz** a `requireContiguousAddresses`. Az alapértelmezett érték **igaz**.
- Alhálózatok létrehozása meglévő virtuális hálózatban nem támogatott.
- Ha `options.hideExisting` van **igaz**, a felhasználó egy meglévő virtuális hálózat nem választható. Az alapértelmezett érték **hamis**.

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

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
