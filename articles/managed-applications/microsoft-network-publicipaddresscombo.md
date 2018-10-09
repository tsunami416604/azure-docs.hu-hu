---
title: Az Azure PublicIpAddressCombo felhasználói felületi elemben |} A Microsoft Docs
description: Az Azure portal a Microsoft.Network.PublicIpAddressCombo felhasználói felületi elem ismerteti.
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
ms.openlocfilehash: c3e8c99f6648f0f4927140f3215978566afb9eb8
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868904"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI element
Funkciók kiválasztása egy új vagy meglévő nyilvános IP-cím csoportja.

## <a name="ui-sample"></a>Felhasználói felület minta
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Ha a felhasználó kiválasztja a "Nincs" nyilvános IP-cím, a tartomány neve felirat szövegmező rejtve marad.
- Ha a felhasználó kiválaszt egy meglévő nyilvános IP-címet, a tartomány neve felirat szövegmező le van tiltva. A kiválasztott IP-cím tartománynévcímkéje értéke.
- A tartomány neve (például westus.cloudapp.azure.com) utótag frissítések automatikusan a kiválasztott hely alapján.

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
- Ha `constraints.required.domainNameLabel` értékre van állítva **igaz**, a felhasználónak kell megadnia egy tartománynévcímkét, amikor új nyilvános IP-címet hoz létre. Meglévő nyilvános IP-címek nélkül a címke nem érhetők el lehet kiválasztani.
- Ha `options.hideNone` értékre van állítva **igaz**, majd válassza ki a lehetőséget **None** a nyilvános IP-cím van-e rejtve. Az alapértelmezett érték **hamis**.
- Ha `options.hideDomainNameLabel` értékre van állítva **igaz**, majd szolgáló tartománynévcímke van-e rejtve. Az alapértelmezett érték **hamis**.
- Ha `options.hideExisting` értéke igaz, a felhasználó nem tud egy meglévő nyilvános IP-cím kiválasztása. Az alapértelmezett érték **hamis**.
- A `zone`, csak nyilvános IP-cím a megadott zóna vagy zóna rugalmas nyilvános IP-címek érhetők el.

## <a name="sample-output"></a>Példa kimenet
Ha a felhasználó nincs nyilvános IP-cím, a vezérlő a következő kimenetet ad vissza:

```json
{
  "newOrExistingOrNone": "none"
}
```

Ha a felhasználó egy új vagy meglévő IP-címet, a vezérlő a következő kimenetet ad vissza:

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

- Amikor `options.hideNone` megadott **igaz**, `newOrExistingOrNone` érték csak van **új** vagy **meglévő**.
- Amikor `options.hideDomainNameLabel` megadott **igaz**, `domainNameLabel` nincs deklarálva.

## <a name="next-steps"></a>További lépések
* Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
* Egy felhasználói felületi elemeket általános tulajdonságok leírásáért lásd: [CreateUiDefinition elemek](create-uidefinition-elements.md).
