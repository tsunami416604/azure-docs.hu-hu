---
title: Azure PublicIpAddressCombo UI element | Microsoft Docs
description: A Azure Portal Microsoft. Network. PublicIpAddressCombo felhasználói felületi elemének ismertetése.
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
ms.openlocfilehash: 069a8ee1f019d1b21be996084e4902f94076fbf7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151491"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI element

Új vagy meglévő nyilvános IP-cím kiválasztására szolgáló vezérlők csoportja.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Ha a felhasználó a "nincs" lehetőséget választja a nyilvános IP-címhez, a tartománynév feliratának szövegmezője rejtett.
- Ha a felhasználó kiválaszt egy meglévő nyilvános IP-címet, a tartománynév felirat szövegmező le lesz tiltva. Értéke a kijelölt IP-cím tartománynév-címkéje.
- A tartománynév utótagja (például westus.cloudapp.azure.com) automatikusan frissül a kiválasztott hely alapján.

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

- Ha `options.hideNone` értéke TRUE ( **igaz**), `newOrExistingOrNone` csak az **új** vagy a **meglévő**értéket fogja tartalmazni.
- Ha `options.hideDomainNameLabel` **igaz**értékre van beállítva, `domainNameLabel` nem deklarált.

## <a name="remarks"></a>Megjegyzések

- Ha a `constraints.required.domainNameLabel` értéke **true (igaz**), a felhasználónak meg kell adnia egy tartománynév címkét új nyilvános IP-cím létrehozásakor. Egy címke nélküli meglévő nyilvános IP-címek nem választhatók ki a kijelöléshez.
- Ha `options.hideNone` értéke TRUE ( **igaz**), akkor a nyilvános IP-cím **egyikének** kiválasztására szolgáló beállítás rejtett. Az alapértelmezett érték **hamis**.
- Ha `options.hideDomainNameLabel` értéke TRUE ( **igaz**), akkor a tartománynév feliratának szövegmezője rejtett. Az alapértelmezett érték **hamis**.
- Ha a `options.hideExisting` értéke igaz, a felhasználó nem választhat meglévő nyilvános IP-címet. Az alapértelmezett érték **hamis**.
- `zone`esetében csak a megadott zónához vagy zónához tartozó nyilvános IP-címek érhetők el.

## <a name="next-steps"></a>További lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
