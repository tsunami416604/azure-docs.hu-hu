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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774262"
---
Ez a szakasz azt ismerteti, hogy miként hajthatja végre a következő módosítási műveleteket a közvetlen társviszony-létesítéshez:

* Közvetlen társviszony-létesítési kapcsolatok hozzáadása
* Közvetlen társviszony-létesítési kapcsolatok eltávolítása
* Az aktív kapcsolatok sávszélességének frissítése vagy visszaminősítése.
* IPv4/IPv6-munkamenet hozzáadása aktív kapcsolatokhoz.
* Távolítsa el az IPv4/IPv6 munkamenetet az aktív kapcsolatokból.

### <a name="add-direct-peering-connections"></a>Közvetlen társviszony-létesítési kapcsolatok hozzáadása

Az alábbi példa bemutatja, hogyan adhat hozzá kapcsolatokat a meglévő közvetlen társviszony-létesítéshez

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Közvetlen társviszony-létesítési kapcsolatok eltávolítása

A kapcsolat eltávolítása jelenleg nem támogatott a PowerShell. Lépjen kapcsolatba [a Microsoft társviszony-létesítési](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>A sávszélesség frissítése vagy visszaminősítése aktív kapcsolatokon

Az alábbi példa bemutatja, hogyan adhat hozzá 10 Gbps-t a meglévő közvetlen kapcsolathoz.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet hozzáadása aktív kapcsolatokhoz.

Az alábbi példa bemutatja, hogyan lehet IPv6-munkamenetet hozzáadni egy meglévő közvetlen kapcsolathoz csak az IPv4-munkamenettel. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Távolítsa el az IPv4/IPv6 munkamenetet az aktív kapcsolatokból.

A PowerShell jelenleg nem támogatja az IPv4/IPv6-munkamenet meglévő kapcsolatból való eltávolítását. Lépjen kapcsolatba [a Microsoft társviszony-létesítési](mailto:peeringexperience@microsoft.com).