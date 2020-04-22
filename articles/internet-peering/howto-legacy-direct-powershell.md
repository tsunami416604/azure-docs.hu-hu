---
title: Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával
titleSuffix: Azure
description: Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 5d2a8c910c9e384e137785bc1cd491bc85c7e7a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678497"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-powershell"></a>Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával

Ez a cikk ismerteti, hogyan konvertálhatja a meglévő örökölt közvetlen társviszony-létesítés egy Azure-erőforrás powershell-parancsmagok használatával.

Ha szeretné, ezt az útmutatót az Azure Portal használatával is [elvégezheti.](howto-legacy-direct-portal.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és a [közvetlen társviszony-létesítési forgatókönyvet.](walkthrough-direct-all.md)

### <a name="work-with-azure-powershell"></a>Az Azure PowerShell munkája
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be Azure-fiókjába, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-a-legacy-direct-peering-for-conversion"></a><a name= get></a>Örökölt közvetlen társviszony-létesítés a konvertáláshoz
Ez a példa bemutatja, hogyan lehet egy örökölt közvetlen társviszony-létesítésa a Seattle-i társviszony-létesítési helyen.

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Íme egy példa egy válaszra:
```powershell
Name                       :
Sku                        : Basic_Direct_Free
Kind                       : Direct
PeeringLocation            : Seattle
UseForPeeringService       : False
PeerAsn.Id                 :
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.71.156.72/30
PeerSessionIPv4Address     : 4.71.156.73
MicrosoftIPv4Address       : 4.71.156.74
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:2100::1e10/126
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.68.70.140/30
PeerSessionIPv4Address     : 4.68.70.141
MicrosoftIPv4Address       : 4.68.70.142
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:4:3::cc/126
PeerSessionIPv6Address     : 2001:1900:4:3::cd
MicrosoftIPv6Address       : 2001:1900:4:3::ce
SessionStateV6             : Established
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
ProvisioningState          : Succeeded
```

### <a name="convert-a-legacy-direct-peering"></a>Örökölt közvetlen társviszony-létesítés konvertálása

&nbsp;
> [!IMPORTANT]
> Ha egy örökölt társviszony-létesítést Azure-erőforrássá alakít át, a módosítások nem támogatottak. &nbsp;

Ezzel a paranccsal konvertálhatja az örökölt közvetlen társviszony-létesítést egy Azure-erőforrássá:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Íme egy példa egy válaszra:

```powershell
Name                 : SeattleDirectPeering
Sku.Name             : Basic_Direct_Free
Kind                 : Direct
Connections          : {11, 11}
PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{asnNumber}
UseForPeeringService : False
PeeringLocation      : Seattle
ProvisioningState    : Succeeded
Location             : centralus
Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
Type                 : Microsoft.Peering/peerings
Tags                 : {}
```

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával kaphatja meg:

```powershell
Get-Help Get-AzPeering -detailed
```

További információt az [Internetes társviszony-létesítés – gyakori kérdések című témakörben talál.](faqs.md)

## <a name="next-steps"></a>További lépések

* [Közvetlen társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával](howto-direct-powershell.md)
