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
ms.openlocfilehash: 2ebf5d574b4d185953ab0f7984648c440d6f107e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678623"
---
A **Get-AzPeeringLocation PowerShell-parancsmag** a kötelező paraméterrel `Kind`rendelkező társviszony-létesítési helyek listáját adja vissza, amelyet a későbbi lépésekben fog használni.

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Az Exchange társviszony-létesítési helyei a következő mezőket tartalmazzák:
* ExchangeName (ExchangeName)
* Társviszony-létesítési hely
* Ország
* PeeringDBFacilityId azonosító
* PeeringDBFacilityLink
* MicrosoftIPv4Cím
* MicrosoftIPv6Cím

Ellenőrizze, hogy jelen van-e a kívánt társviszony-létesítési lehetőségben a [Társviszony-létesítési lehetőségre](https://wwww.peeringdb.com)hivatkozva.

Ez a példa azt mutatja be, hogyan használható Seattle társviszony-létesítési helytársviszony-létesítési helyként.

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