---
title: TársASN-társ-ASN-társítása az Azure-előfizetéshez a PowerShell használatával
titleSuffix: Azure
description: TársASN-társ-ASN-társítása az Azure-előfizetéshez a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908992"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>TársASN-társ-ASN-társítása az Azure-előfizetéshez a PowerShell használatával

Mielőtt társviszony-létesítési kérelmet küld, először társítsa az ASN-t az Azure-előfizetéssel az alábbi lépések végrehajtásával.

Ha szeretné, ezt az útmutatót a portál segítségével is [elvégezheti.](howto-subscription-association-portal.md)

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>PeerASN létrehozása az ASN azure-előfizetéssel való társításához

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be Azure-fiókjába, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Regisztráció társviszony-létesítési erőforrás-szolgáltatóhoz
Regisztráljon társviszony-létesítési erőforrás-szolgáltatóra az előfizetésében az alábbi paranccsal. Ha ezt nem hajtja végre, akkor a társviszony-létesítés beállításához szükséges Azure-erőforrások nem érhetők el.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

A regisztrációs állapotot az alábbi parancsokkal ellenőrizheti:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Várjon, amíg *a RegistrationState* bekapcsolja a "Regisztrált" bekapcsolást a folytatás előtt. A parancs végrehajtása után 5–30 percig is eltarthat.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Az előfizetéshez társított társadatok frissítése

Az alábbi példa a társadatok frissítésére szolgál.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -A név megfelel az erőforrás nevének, és bármi lehet, amit csak választhat. A -peerName azonban megfelel a vállalat nevének, és a lehető legközelebb kell lennie a PeeringDB-profilhoz. Ne feledje, hogy a -peerName érték csak az a-z, az A-Z és a szóköz karaktereket támogatja.

Egy előfizetés több ASN-t is rendelkezhet. Frissítse az egyes ASN-ek társviszony-létesítési adatait. Győződjön meg arról, hogy a "név" egyedi minden ASN.Ensure that "name" is unique for each ASN.

A peeringdb-n a társaknak teljes [PeeringDB](https://www.peeringdb.com)és naprakész profillal kell rendelkezniük. Ezeket az információkat a regisztráció során arra használjuk, hogy érvényesítsük a partner adatait, például a NOC adatait, a műszaki elérhetőségi adatokat és jelenlétüket a társviszony-létesítő létesítményekben stb.

Vegye figyelembe, hogy a fenti kimenetben a **{subscriptionId}** helyett a tényleges előfizetés-azonosító jelenik meg.

## <a name="view-status-of-a-peerasn"></a>Társtársiasn állapotának megtekintése

Ellenőrizze az ASN-érvényesítés állapotát az alábbi paranccsal:

```powershell
Get-AzPeerAsn
```

Az alábbiakban egy példa válasz:
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
> Várakozás, amíg a ValidationState bekapcsolja a "Jóváhagyva" a társviszony-létesítési kérelem elküldése előtt. A jóváhagyás akár 12 órát is igénybe vehet.

## <a name="modify-peerasn"></a>Társtárs-asszn módosítása
A NOC kapcsolattartási adatait bármikor módosíthatja.

Az alábbiakban egy példa:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Társtárs-társítás törlése
A Társtársiasn törlése jelenleg nem támogatott. Ha törölnie kell a Társtársi kapcsolatot, forduljon a [Microsoft társviszony-létesítési](mailto:peering@microsoft.com).

## <a name="next-steps"></a>További lépések

* [Közvetlen társviszony létesítése vagy módosítása](howto-direct-powershell.md)
* [Örökölt közvetlen társviszony konvertálása Azure-erőforrássá](howto-legacy-direct-powershell.md)
* [Exchange-társviszony-létesítés létrehozása vagy módosítása](howto-exchange-powershell.md)
* [Örökölt Exchange-társviszony konvertálása Azure-erőforrássá](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>További források

További információkért látogasson el [az internetes társviszony-létesítési GYIK-be](faqs.md)
