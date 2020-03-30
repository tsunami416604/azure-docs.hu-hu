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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774418"
---
A módosítási művelet et követően az Exchange társviszony-létesítése támogatott
1. Exchange-társviszony-létesítési kapcsolatok hozzáadása
1. Exchange-társviszony-létesítési kapcsolatok eltávolítása
1. IPv4/IPv6-munkamenet hozzáadása aktív kapcsolatokhoz.
1. Távolítsa el az IPv4/IPv6 munkamenetet az aktív kapcsolatokból.


### <a name="add-exchange-peering-connections"></a>Exchange-társviszony-létesítési kapcsolatok hozzáadása

Az alábbi példa bemutatja, hogyan lehet kapcsolatokat hozzáadni a meglévő Exchange-társviszony-létesítéshez

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

### <a name="remove-exchange-peering-connections"></a>Exchange-társviszony-létesítési kapcsolatok eltávolítása

Az alábbi példa bemutatja, hogyan lehet eltávolítani a meglévő Exchange-társviszony-létesítési kapcsolatokat

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

Az alábbi parancsban 0 helyett írja be az eltávolítani kívánt kapcsolat indexszámát.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet hozzáadása aktív kapcsolatokhoz

Az alábbi példa bemutatja, hogyan lehet IPv6-munkamenetet hozzáadni a meglévő cserekapcsolathoz.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4-munkamenet eltávolítása aktív kapcsolatokon

A PowerShell jelenleg nem támogatja az IPv4/IPv6-munkamenet meglévő kapcsolatból való eltávolítását. Lépjen kapcsolatba [a Microsoft társviszony-létesítési](mailto:peeringexperience@microsoft.com).