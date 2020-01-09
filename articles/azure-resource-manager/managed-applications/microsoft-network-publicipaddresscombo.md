---
title: PublicIpAddressCombo FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. Network. PublicIpAddressCombo felhasználói felületi elemének ismertetése.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 0393673663df8f3ca580ff34e16bee910b955f8d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651916"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft. Network. PublicIpAddressCombo FELHASZNÁLÓIFELÜLET-elem

Új vagy meglévő nyilvános IP-cím kiválasztására szolgáló vezérlők csoportja.

## <a name="ui-sample"></a>Felhasználói felület mintája

![Microsoft. Network. PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

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
- Ha `options.hideNone` értéke TRUE ( **igaz**), akkor a nyilvános IP-cím **egyikének** kiválasztására szolgáló beállítás rejtett. Az alapértelmezett érték **false (hamis**).
- Ha `options.hideDomainNameLabel` értéke TRUE ( **igaz**), akkor a tartománynév feliratának szövegmezője rejtett. Az alapértelmezett érték **false (hamis**).
- Ha a `options.hideExisting` értéke igaz, a felhasználó nem választhat meglévő nyilvános IP-címet. Az alapértelmezett érték **false (hamis**).
- `zone`esetében csak a megadott zónához vagy zónához tartozó nyilvános IP-címek érhetők el.

## <a name="next-steps"></a>Következő lépések

* A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
* A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).
