---
title: Azure PowerShell-minták – hálózatkezelés
description: Azure PowerShell-minták
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: d1e6027a0dc71f0962293143ca9bd3de67a659e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844904"
---
# <a name="azure-powershell-samples-for-networking"></a>Azure PowerShell-minták hálózatépítéshez

Az alábbi táblázat az Azure PowerShell használatával készített parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Kapcsolat az Azure-erőforrások között**||
| [Virtuális hálózat létrehozása többrétegű alkalmazásokhoz](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat felé irányuló forgalom HTTP-re, a háttérbeli alhálózat felé irányuló forgalom pedig az SQL-re, az 1433-as portra korlátozódik. |
| [Társviszony létesítése két virtuális hálózat között](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban. |
| [Forgalom irányítása hálózati virtuális készüléken keresztül](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal, valamint egy virtuális gépet, amely képes a két alhálózat közötti forgalom irányítására. |
| [Virtuális gép kimenő és bejövő hálózati forgalmának szűrése](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtér-alhálózatba irányuló bejövő hálózati forgalom HTTP-re és HTTPS-re korlátozódik. A háttér-alhálózatból az internetre irányuló kimenő forgalom nem engedélyezett. |
|**Terheléselosztás és forgalom iránya**||
| [Terheléselosztási forgalom a virtuális gépekre a magas rendelkezésre állás érdekében](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Több virtuális gépet hoz létre magas rendelkezésre állású és terheléselosztásos konfigurációban. |
| [Közvetlen forgalom több régióban a magas rendelkezésre állás érdekében](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Két alkalmazásszolgáltatási csomag, két webalkalmazás, egy forgalomkezelő profil és két forgalomkezelő végpont létrehozása. |
| | |
