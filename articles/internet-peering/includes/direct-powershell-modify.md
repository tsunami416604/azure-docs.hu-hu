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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680769"
---
Ez a szakasz azt ismerteti, hogy miként hajthatja végre a következő módosítási műveleteket a közvetlen társviszony-létesítéshez:

* Közvetlen társviszony-létesítési kapcsolatok hozzáadása.
* A közvetlen társviszony-létesítési kapcsolatok eltávolítása.
* Az aktív kapcsolatok sávszélességének frissítése vagy visszaminősítése.
* IPv4- vagy IPv6-munkamenetek hozzáadása aktív kapcsolatokhoz.
* Távolítsa el az IPv4- vagy IPv6-munkameneteket az aktív kapcsolatokon.

### <a name="add-direct-peering-connections"></a>Közvetlen társviszony-létesítési kapcsolatok hozzáadása

Ez a példa azt ismerteti, hogyan adhat hozzá kapcsolatokat a meglévő közvetlen társviszony-létesítéshez.

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

A kapcsolat eltávolítása jelenleg nem támogatott a PowerShell. További információért forduljon a [Microsoft társviszony-létesítési](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>A sávszélesség frissítése vagy visszaminősítése aktív kapcsolatokon

Ez a példa azt ismerteti, hogyan adhat hozzá 10 Gb/s-ot egy meglévő közvetlen kapcsolathoz.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>IPv4- vagy IPv6-munkamenetek hozzáadása aktív kapcsolatokhoz

Ez a példa azt ismerteti, hogy miként lehet IPv6-munkamenetet hozzáadni egy olyan meglévő közvetlen kapcsolathoz, amely csak IPv4-munkamenetet rendelkezik. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>IPv4- vagy IPv6-munkamenetek eltávolítása aktív kapcsolatokon

Az IPv4- vagy IPv6-munkamenet ek eltávolítása egy meglévő kapcsolatból jelenleg nem támogatott a PowerShellben. További információért forduljon a [Microsoft társviszony-létesítési](mailto:peeringexperience@microsoft.com).