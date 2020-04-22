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
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678670"
---
Az Exchange társviszony-létesítése a következő módosítási műveleteket támogatja:
* Exchange-társviszony-létesítési kapcsolatok hozzáadása.
* Távolítsa el az Exchange-társviszony-létesítési kapcsolatokat.
* IPv4- vagy IPv6-munkamenet hozzáadása aktív kapcsolatokhoz.
* IPv4- vagy IPv6-munkamenet eltávolítása aktív kapcsolatokon.


### <a name="add-exchange-peering-connections"></a>Exchange-társviszony-létesítési kapcsolatok hozzáadása

Ez a példa azt ismerteti, hogy miként lehet kapcsolatokat hozzáadni egy meglévő Exchange-társviszony-létesítéshez.

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

Ez a példa azt ismerteti, hogy miként távolítható el a meglévő Exchange-társviszony-létesítési kapcsolatok.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Tekintse meg az összes kapcsolatot, és jelölje ki az eltávolítani kívánt kapcsolatot. 

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

A következő parancsban 0 helyett írja be az eltávolítani kívánt kapcsolat indexszámát.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>IPv4- vagy IPv6-munkamenet hozzáadása aktív kapcsolatokhoz

Ez a példa azt ismerteti, hogy miként lehet IPv6-munkamenetet hozzáadni egy meglévő Exchange-kapcsolathoz.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>IPv4- vagy IPv6-munkamenet eltávolítása aktív kapcsolatokon

Az IPv4- vagy IPv6-munkamenet ek eltávolítása egy meglévő kapcsolatból jelenleg nem támogatott a PowerShellben. További információért forduljon a [Microsoft társviszony-létesítési](mailto:peeringexperience@microsoft.com).