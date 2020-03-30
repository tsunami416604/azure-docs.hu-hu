---
title: PublicIpAddressCombo felhasználói felületi elem
description: A cikk a Microsoft.Network.PublicIpAddressCombo UI összetevőt ismerteti az Azure Portalon.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 0393673663df8f3ca580ff34e16bee910b955f8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651916"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo felhasználói felület

Vezérlők csoportja új vagy meglévő nyilvános IP-cím kiválasztásához.

## <a name="ui-sample"></a>Felhasználói felület minta

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Ha a felhasználó a "Nincs" lehetőséget választja a nyilvános IP-címhez, a tartománynév feliratának szövegmezője rejtett.
- Ha a felhasználó egy meglévő nyilvános IP-címet választ, a tartománynév feliratának szövegmezője le van tiltva. Értéke a kijelölt IP-cím tartománynév-címkéje.
- A tartománynév-utótag (például westus.cloudapp.azure.com) automatikusan frissül a kiválasztott hely alapján.

## <a name="schema"></a>Séma

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példa kimenet

Ha a felhasználó nem választ nyilvános IP-címet, a vezérlő a következő kimenetet adja vissza:

```json
{
  "newOrExistingOrNone": "none"
}
```

Ha a felhasználó új vagy meglévő IP-címet választ, a vezérlő a következő kimenetet adja vissza:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- Ha `options.hideNone` **true**értékként `newOrExistingOrNone` van megadva , annak értéke csak **új** vagy **meglévő**lesz .
- Ha `options.hideDomainNameLabel` **igazként**van `domainNameLabel` megadva, nincs deklarálva.

## <a name="remarks"></a>Megjegyzések

- Ha `constraints.required.domainNameLabel` értéke **igaz,** a felhasználónak meg kell adnia egy tartománynév-címkét új nyilvános IP-cím létrehozásakor. A meglévő nyilvános IP-címek címke nélkül nem választhatók ki.
- Ha `options.hideNone` értéke **igaz,** akkor a nyilvános IP-cím **nincs** elemének kiválasztására szolgáló lehetőség rejtett. Az alapértelmezett érték: **hamis**.
- Ha `options.hideDomainNameLabel` értéke **igaz,** akkor a tartománynév-címke szövegdoboza rejtett. Az alapértelmezett érték: **hamis**.
- Ha `options.hideExisting` igaz, akkor a felhasználó nem tud meglévő nyilvános IP-címet választani. Az alapértelmezett érték: **hamis**.
- A `zone`esetén csak nyilvános IP-címek érhetők el a megadott zónához vagy zónarugalmas nyilvános IP-címekhez.

## <a name="next-steps"></a>További lépések

* A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
* A felhasználói felület elemeinek gyakori tulajdonságainak leírását a [CreateUiDefinition elements](create-uidefinition-elements.md)című témakörben található.
