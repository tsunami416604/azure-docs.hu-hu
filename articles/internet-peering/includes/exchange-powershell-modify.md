---
title: fájl belefoglalása
titleSuffix: Azure
description: fájl belefoglalása
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 86e1a9cce1864ce259fe07b6949be2e32be242a8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774418"
---
A következő módosítási művelet támogatott az Exchange-társak számára
1. Exchange-partneri kapcsolatok hozzáadása
1. Exchange-társi kapcsolatok eltávolítása
1. IPv4/IPv6-munkamenet hozzáadása az aktív kapcsolatokhoz.
1. IPv4/IPv6-munkamenet eltávolítása az aktív kapcsolatokon.


### <a name="add-exchange-peering-connections"></a>Exchange-partneri kapcsolatok hozzáadása

Az alábbi példa azt ismerteti, hogyan lehet kapcsolatokat hozzáadni a meglévő Exchange-partnerekhez

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Exchange-társi kapcsolatok eltávolítása

Az alábbi példa azt ismerteti, Hogyan távolítható el a meglévő Exchange-társ kapcsolatai

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Tekintse meg az összes kapcsolatot, és válassza ki az eltávolítani kívánt kapcsolatot. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

Az alábbi parancsban a 0 helyett adja meg az eltávolítani kívánt kapcsolatok indexének számát.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet hozzáadása az aktív kapcsolatokhoz

Az alábbi példa azt ismerteti, hogyan adhat hozzá IPv6-munkamenetet meglévő Exchange-kapcsolathoz.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet eltávolítása az aktív kapcsolatokon

Egy IPv4/IPv6-munkamenet meglévő kapcsolatból való eltávolítása jelenleg nem támogatott a PowerShellben. Forduljon a [Microsoft-partnerekhez](mailto:peeringexperience@microsoft.com).