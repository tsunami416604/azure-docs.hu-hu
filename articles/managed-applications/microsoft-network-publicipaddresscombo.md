---
title: "Az Azure által felügyelt alkalmazás PublicIpAddressCombo felhasználói felületi elem |} Microsoft Docs"
description: "A témakör ismerteti a Microsoft.Network.PublicIpAddressCombo felhasználói felületi elem Azure által felügyelt alkalmazások"
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
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 94f1f64a57784254912cb6cf568fafc15c8e74ec
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo felhasználói felületi elem
Egy új vagy meglévő nyilvános IP-cím kiválasztásához vezérlők egy csoportja. Ez az elem használata során [Azure által felügyelt alkalmazások létrehozására](publish-service-catalog-app.md).

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
    "domainNameLabel": "foobar"
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
  "domainNameLabel": "foobar",
  "newOrExistingOrNone": "new"
}
```
- Ha `options.hideNone` megadott **igaz**, `newOrExistingOrNone` érték csak van **új** vagy **meglévő**.
- Ha `options.hideDomainNameLabel` megadott **igaz**, `domainNameLabel` nincs deklarálva.

## <a name="next-steps"></a>Következő lépések
* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](overview.md).
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
* Általános tulajdonságok felhasználói felületi elemei ismertetését lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
