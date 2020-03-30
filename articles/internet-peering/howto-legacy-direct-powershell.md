---
title: Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrásba a PowerShell használatával
titleSuffix: Azure
description: Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrásba a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ba41f4ad8014ba3e85174b7c32e11394f0068643
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775016"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-powershell"></a>Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrásba a PowerShell használatával

Ez a cikk ismerteti, hogyan konvertálhatja a meglévő örökölt közvetlen társviszony-létesítés az Azure-erőforrás PowerShell-parancsmagok használatával.

Ha szeretné, ezt az útmutatót a portál segítségével is [elvégezheti.](howto-legacy-direct-portal.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át [az előfeltételeket](prerequisites.md) és [a közvetlen társviszony-létesítési forgatókönyvet.](walkthrough-direct-all.md)

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be Azure-fiókjába, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-direct-peering-for-conversion"></a><a name= get></a>Örökölt közvetlen társviszony-létesítés a konvertáláshoz
Az alábbiakban egy példa, hogy örökölt Közvetlen társviszony-létesítés a Seattle peering helyen

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Az alábbiakban egy példa válasz:
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

### <a name="convert-legacy-direct-peering"></a>Örökölt közvetlen társviszony-létesítés konvertálása

&nbsp;
> [!IMPORTANT]
> Vegye figyelembe, hogy az örökölt társviszony-létesítés azure-erőforrássá konvertálásakor a módosítások nem támogatottak. &nbsp;

Az alábbi parancs használatával konvertálhatja az örökölt közvetlen társviszony-létesítést az Azure-erőforrássá:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Az alábbiakban egy példa válasz:

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

További információkért látogasson el [az internetes társviszony-létesítési GYIK-be](faqs.md)

## <a name="next-steps"></a>További lépések

* [Közvetlen társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával.](howto-direct-powershell.md)
