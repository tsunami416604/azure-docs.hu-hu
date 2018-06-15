---
title: Az Azure PublicIpAddressCombo felhasználói felületi elem |} Microsoft Docs
description: Azure-portálon a Microsoft.Network.PublicIpAddressCombo felhasználói felületi elem ismerteti.
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
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: bf0ef5be609fba14ab12e1e6f9f97bc63f032aae
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260560"
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
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="remarks"></a>Megjegyzések
- Ha `constraints.required.domainNameLabel` értéke **igaz**, a felhasználónak a tartománynév-címke meg kell adnia egy új nyilvános IP-cím létrehozása során. Meglévő nyilvános IP-címek, egy címke nélküli nem lehet választani.
- Ha `options.hideNone` értéke **igaz**, majd jelölje be a beállítás **nincs** a nyilvános IP-cím van-e rejtve. Az alapértelmezett érték **hamis**.
- Ha `options.hideDomainNameLabel` értéke **igaz**, akkor a tartománynév-címke a szövegmezőbe írja be van-e rejtve. Az alapértelmezett érték **hamis**.
- Ha `options.hideExisting` értéke igaz, akkor a felhasználó nem választhat egy meglévő nyilvános IP-cím. Az alapértelmezett érték **hamis**.
- A `zone`, egyetlen nyilvános IP-cím a megadott zóna vagy zóna rugalmas nyilvános IP-címekhez érhetők el.

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
