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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774262"
---
Ez a szakasz azt ismerteti, hogyan hajtható végre a következő módosítási műveletek a közvetlen társításhoz:

* Közvetlen társas kapcsolatok hozzáadása
* Közvetlen társas kapcsolatok eltávolítása
* A sávszélesség frissítése vagy visszalépése az aktív kapcsolatokon.
* IPv4/IPv6-munkamenet hozzáadása az aktív kapcsolatokhoz.
* IPv4/IPv6-munkamenet eltávolítása az aktív kapcsolatokon.

### <a name="add-direct-peering-connections"></a>Közvetlen társas kapcsolatok hozzáadása

Az alábbi példa azt ismerteti, hogyan lehet kapcsolatokat hozzáadni a meglévő közvetlen társításhoz

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

### <a name="remove-direct-peering-connections"></a>Közvetlen társas kapcsolatok eltávolítása

A kapcsolatok eltávolítása jelenleg nem támogatott a PowerShellben. Forduljon a [Microsoft-partnerekhez](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>A sávszélesség frissítése vagy visszalépése aktív kapcsolatokon

Az alábbi példa azt ismerteti, hogyan adhat hozzá 10Gbps a meglévő közvetlen kapcsolódáshoz.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet hozzáadása az aktív kapcsolatokhoz.

Az alábbi példa azt ismerteti, hogyan adható hozzá IPv6-munkamenet egy meglévő közvetlen kapcsolathoz csak IPv4-munkamenettel. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet eltávolítása az aktív kapcsolatokon.

Egy IPv4/IPv6-munkamenet meglévő kapcsolatból való eltávolítása jelenleg nem támogatott a PowerShellben. Forduljon a [Microsoft-partnerekhez](mailto:peeringexperience@microsoft.com).