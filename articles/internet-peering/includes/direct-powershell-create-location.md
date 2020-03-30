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
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774288"
---
A **Get-AzPeeringLocation PowerShell-parancsmag** a kötelező paraméterrel `Kind`rendelkező társviszony-létesítési helyek listáját adja vissza, amelyet a későbbi lépésekben fog használni:

```powershell
Get-AzPeeringLocation -Kind Direct
```

A közvetlen társviszony-létesítési helyek a következő mezőket tartalmazzák:
* Társviszony-létesítési hely 
* Ország
* PeeringDBFacilityId azonosító
* PeeringDBFacilityLink
* Sávszélességajánlatok

Ellenőrizze, hogy jelen van-e a kívánt társviszony-létesítési lehetőségben a [Társviszony-létesítési lehetőségre](https://wwww.peeringdb.com)hivatkozva.

Az alábbi példa bemutatja, hogyan használhatja Seattle-t társviszony-létesítési helyként közvetlen társviszony-létesítés létrehozásához:

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