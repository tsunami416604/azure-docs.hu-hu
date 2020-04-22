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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680776"
---
A **Get-AzPeeringLocation PowerShell-parancsmag** a kötelező paraméterrel `Kind`rendelkező társviszony-létesítési helyek listáját adja vissza, amelyet a későbbi lépésekben fog használni.

```powershell
Get-AzPeeringLocation -Kind Direct
```

A közvetlen társviszony-létesítési helyek a következő mezőket tartalmazzák:
* Társviszony-létesítési hely 
* Ország
* PeeringDBFacilityId azonosító
* PeeringDBFacilityLink
* Sávszélességajánlatok

Ellenőrizze, hogy jelen van-e a kívánt társviszony-létesítési lehetőségben a [PeeringDB](https://wwww.peeringdb.com)elemre hivatkozva.

Ez a példa azt mutatja be, hogyan használható Seattle társviszony-létesítési helyként közvetlen társviszony-létesítési létrehozásához.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```