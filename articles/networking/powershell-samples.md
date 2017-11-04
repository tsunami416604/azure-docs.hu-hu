---
title: "Az Azure PowerShell-példák |} Microsoft Docs"
description: "Az Azure PowerShell-példák"
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: georgewallace
ms.openlocfilehash: 0bca4fb6874bd265f0ae9faeb4219abeb4ffb6d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-powershell-samples-for-networking"></a>Hálózat az Azure PowerShell-példák

A következő táblázat az Azure PowerShell használatával készített parancsfájlok mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Azure-erőforrások közötti kapcsolat**||
| [A többrétegű alkalmazások virtuális hálózat létrehozása](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Előtér- és alhálózatok hoz létre a virtuális hálózat. Az előtér-alhálózat felé irányuló forgalom korlátozódik HTTP, közben az adatforgalom a háttér-alhálózathoz korlátozott, az SQL-1433-as port. |
| [A két partner virtuális hálózatok](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hoz létre, és két virtuális hálózat ugyanabban a régióban kapcsolódik. |
| [A hálózati virtuális készülék-útvonal forgalmát](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hoz létre egy virtuális hálózati előtér- és alhálózatok és virtuális gép, amely képes irányíthatja a forgalmat a két alhálózat között. |
| [Bejövő és kimenő virtuális gép hálózati forgalom szűrésére](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Előtér- és alhálózatok hoz létre a virtuális hálózat. Az előtér-alhálózat bejövő hálózati forgalmának HTTP és HTTPS korlátozódik. Kimenő forgalom az internethez, a háttér-alhálózatból nem engedélyezett. |
|**Terheléselosztás és a forgalom iránya betöltése**||
| [Betöltési oszthatja el a forgalmat a virtuális gépek magas rendelkezésre álláshoz](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre. |
| [Terhelésének elosztása több webhely virtuális gépeken](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hoz létre két virtuális gépek több IP-konfigurációk, csatlakozik Azure rendelkezésre állási csoport, az Azure Load Balancer keresztül érhető el. |
| [A magas rendelkezésre állásának különféle régiókban közvetlen forgalom](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Két app service-csomagokról, a két web apps, a traffic manager-profil és a két traffic manager-végpont létrehozása |
| | |
