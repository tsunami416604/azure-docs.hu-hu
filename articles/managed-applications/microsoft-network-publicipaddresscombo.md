---
title: Az Azure PublicIpAddressCombo felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Network.PublicIpAddressCombo felhasználói felületi elem ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: bced85abaf24beb0997ec906a68e5947f0e67cca
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI element
Egy új vagy meglévő nyilvános IP-cím kiválasztásához vezérlők egy csoportja.

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Ha a felhasználó kiválasztja a "Nincs" nyilvános IP-cím, a tartomány címke szövegmezőt rejtett.
- Ha a felhasználó kiválaszt egy meglévő nyilvános IP-cím, a tartomány címke szövegmezőt le van tiltva. A kijelölt IP-cím tartománynévcímkéje érvénytelen.
- A tartomány neve utótagot (például westus.cloudapp.azure.com) frissítéseket automatikusan a kijelölt hely alapján.

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
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések
- Ha `constraints.required.domainNameLabel` értéke **igaz**, a felhasználónak a tartománynév-címke meg kell adnia egy új nyilvános IP-cím létrehozása során. Meglévő nyilvános IP-címek, egy címke nélküli nem lehet választani.
- Ha `options.hideNone` értéke **igaz**, majd jelölje be a beállítás **nincs** a nyilvános IP-cím van-e rejtve. Az alapértelmezett érték **hamis**.
- Ha `options.hideDomainNameLabel` értéke **igaz**, akkor a tartománynév-címke a szövegmezőbe írja be van-e rejtve. Az alapértelmezett érték **hamis**.
- Ha `options.hideExisting` értéke igaz, akkor a felhasználó nem választhat egy meglévő nyilvános IP-cím. Az alapértelmezett érték **hamis**.

## <a name="sample-output"></a>Példa kimenet
Ha a felhasználó kijelöli a nyilvános IP-cím, a következő kimeneti várt:
```json
{
  "newOrExistingOrNone": "none"
}
```

Ha a felhasználó kiválaszt egy új vagy meglévő IP-címet, a következő kimeneti várt:
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```
- Ha `options.hideNone` megadott **igaz**, `newOrExistingOrNone` érték csak van **új** vagy **meglévő**.
- Ha `options.hideDomainNameLabel` megadott **igaz**, `domainNameLabel` nincs deklarálva.

## <a name="next-steps"></a>További lépések
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
