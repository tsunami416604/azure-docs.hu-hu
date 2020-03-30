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
ms.openlocfilehash: 6f5d2dc30ac0f6316587fa0836b87cbd4efc0a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774392"
---
A **Get-AzPeeringLocation PowerShell-parancsmag** a kötelező paraméterrel `Kind`rendelkező társviszony-létesítési helyek listáját adja vissza, amelyet a későbbi lépésekben fog használni:

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Az Exchange társviszony-létesítési helyek a következő mezőket tartalmazzák:
* ExchangeName (ExchangeName)
* Társviszony-létesítési hely
* Ország
* PeeringDBFacilityId azonosító
* PeeringDBFacilityLink
* MicrosoftIPv4Cím
* MicrosoftIPv6Cím

Ellenőrizze, hogy jelen van-e a kívánt társviszony-létesítési lehetőségben a [Társviszony-létesítési lehetőségre](https://wwww.peeringdb.com)hivatkozva.

Az alábbi példa bemutatja, hogyan használhatja Seattle-t társviszony-létesítési helyként társviszony-létesítési helyként:

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```