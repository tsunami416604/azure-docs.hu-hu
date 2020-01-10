---
title: Örökölt közvetlen társítás átalakítása Azure-erőforrásra a PowerShell használatával
titleSuffix: Azure
description: Örökölt közvetlen társítás átalakítása Azure-erőforrásra a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ba41f4ad8014ba3e85174b7c32e11394f0068643
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775016"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-powershell"></a>Örökölt közvetlen társítás átalakítása Azure-erőforrásra a PowerShell használatával

Ez a cikk azt ismerteti, hogyan alakíthatja át a meglévő örökölt közvetlen társítást az Azure-erőforrásra PowerShell-parancsmagok használatával.

Ha szeretné, ezt az útmutatót a [portálon](howto-legacy-direct-portal.md)végezheti el.

## <a name="before-you-begin"></a>Előzetes teendők
* A konfigurálás megkezdése előtt tekintse át az [Előfeltételek](prerequisites.md) és a közvetlen társítási [útmutató](walkthrough-direct-all.md) témakört.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Örökölt közvetlen társítás átalakítása Azure-erőforrásra

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Örökölt közvetlen társítások beolvasása átalakításhoz
Alább látható egy példa arra, hogy örökölt közvetlen társat szerezzen a Seattle-alapú peering Location

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Az alábbiakban egy példa erre a válaszra:
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

### <a name="convert-legacy-direct-peering"></a>Örökölt közvetlen társak konvertálása

&nbsp;
> [!IMPORTANT]
> Vegye figyelembe, hogy az örökölt peering Azure-erőforrásra konvertálásakor a módosítások nem támogatottak. &nbsp;

Az alábbi parancs használatával alakítsa át az örökölt közvetlen társítást az Azure-erőforrásra:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Az alábbiakban egy példa erre a válaszra:

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
Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```powershell
Get-Help Get-AzPeering -detailed
```

További információért látogasson el az internetes kereséssel kapcsolatos [Gyakori kérdések](faqs.md) oldalra.

## <a name="next-steps"></a>Következő lépések

* [Hozzon létre vagy módosítson egy közvetlen társat a PowerShell használatával](howto-direct-powershell.md).
