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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774288"
---
A **Get-AzPeeringLocation PowerShell-** parancsmag a (z) `Kind`kötelező paraméterrel visszaadja a társítási helyszínek listáját, amelyet a későbbi lépésekben fog használni:

```powershell
Get-AzPeeringLocation -Kind Direct
```

A közvetlen társítási helyszínek a következő mezőket tartalmazzák:
* PeeringLocation 
* Ország/régió
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Győződjön meg arról, hogy a kívánt társi létesítményben jelen van a [PeeringDB](https://w www.peeringdb.com).

Az alábbi példa bemutatja, hogyan használhatja a Seattle-t a közvetlen társítás létrehozásához a következő módon:

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