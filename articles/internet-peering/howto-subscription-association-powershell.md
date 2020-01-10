---
title: Társközi ASN társítása az Azure-előfizetéshez a PowerShell használatával
titleSuffix: Azure
description: Társközi ASN társítása az Azure-előfizetéshez a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e7239fdedafedc96a382de6c3c2f90b5da4df00c
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774249"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Társközi ASN társítása az Azure-előfizetéshez a PowerShell használatával

A kérések elküldése előtt először társítsa az ASN-t az Azure-előfizetéshez az alábbi lépések segítségével.

Ha szeretné, ezt az útmutatót a [portálon](howto-subscription-association-portal.md)végezheti el.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerASN létrehozása az ASN-nek az Azure-előfizetéshez való hozzárendeléséhez

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Az előfizetéshez társított társ-információ frissítése

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -A név az erőforrás neve, és tetszőlegesen választhatja ki. A-peerName azonban megfelel a vállalat nevének, és a lehető legpontosabban kell megfelelnie a PeeringDB-profilnak. Vegye figyelembe, hogy a-peerName értéke csak a-z, A-Z és A szóköz karaktereket támogatja.

Egy előfizetés több ASN is rendelkezhet. Frissítse az összes ASN-hez tartozó egyenrangú információt. Győződjön meg arról, hogy a "Name" egyedi minden ASN-hez.

A társak számára a [PeeringDB](https://www.peeringdb.com)teljes és naprakész profilja szükséges. Ezeket az információkat a regisztráció során használjuk, hogy érvényesítse a partner adatait, például a NOC információit, a technikai kapcsolattartási adatokat és azok jelenlétét a társas létesítményekben stb.

Vegye figyelembe, hogy a fenti kimenetben lévő **{subscriptionId}** helyett a tényleges előfizetés-azonosító fog megjelenni.

## <a name="view-status-of-a-peerasn"></a>PeerASN állapotának megtekintése

Az alábbi parancs használatával keresse meg az ASN-érvényesítési állapotot:

```powershell
Get-AzPeerAsn
```

Az alábbiakban egy példa erre a válaszra:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Várjon, amíg a ValidationState bekapcsolja a "jóváhagyva" beállítást, mielőtt elküldené a kérést. Ez a jóváhagyás akár 12 órát is igénybe vehet.

## <a name="modify-peerasn"></a>PeerAsn módosítása
Bármikor módosíthatja a NOC kapcsolattartási adatait.

Az alábbiakban egy példa látható:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>PeerAsn törlése
A PeerASN törlése jelenleg nem támogatott. Ha törölnie kell a PeerASN, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz.

## <a name="next-steps"></a>Következő lépések

* [Közvetlen társ létrehozása vagy módosítása](howto-direct-powershell.md)
* [Örökölt közvetlen társítás átalakítása Azure-erőforrásra](howto-legacy-direct-powershell.md)
* [Exchange-társ létrehozása vagy módosítása](howto-exchange-powershell.md)
* [Örökölt Exchange-társ átalakítása az Azure-erőforrásra](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>További források

További információért látogasson el az internetes kereséssel kapcsolatos [Gyakori kérdések](faqs.md) oldalra.
