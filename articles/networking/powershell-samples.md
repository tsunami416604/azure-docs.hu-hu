---
title: Azure PowerShell minták – hálózatkezelés
description: Azure PowerShell-minták
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: 98b72dbb9f3b62fd5985f6245e52293ec31fc682
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232542"
---
# <a name="azure-powershell-samples-for-networking"></a>Azure PowerShell minták a hálózatkezeléshez

Az alábbi táblázat a Azure PowerShell használatával létrehozott parancsfájlokra mutató hivatkozásokat tartalmaz.

| Script | Leírás |
|-|-|
|**Az Azure-erőforrások közötti kapcsolat**||
| [Virtuális hálózat létrehozása többrétegű alkalmazásokhoz](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat felé irányuló forgalom HTTP-re, a háttérbeli alhálózat felé irányuló forgalom pedig az SQL-re, az 1433-as portra korlátozódik. |
| [Társviszony létesítése két virtuális hálózat között](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban. |
| [Forgalom irányítása hálózati virtuális készüléken keresztül](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal, valamint egy virtuális gépet, amely képes a két alhálózat közötti forgalom irányítására. |
| [Virtuális gép kimenő és bejövő hálózati forgalmának szűrése](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtér-alhálózatra irányuló bejövő hálózati forgalom a HTTP és a HTTPS értékre van korlátozva. A háttérbeli alhálózatról az internetre irányuló kimenő forgalom nem engedélyezett. |
|**Terheléselosztás és forgalom iránya**||
| [A virtuális gépek forgalmának elosztása magas rendelkezésre állás érdekében](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Több virtuális gépet hoz létre egy magasan elérhető és terheléselosztásos konfigurációban. |
| [Közvetlen forgalom több régióban az alkalmazások magas rendelkezésre állása érdekében](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Két app Service-csomagot, két webalkalmazást, egy Traffic Manager-profilt és két Traffic Manager-végpontot hoz létre. |
| | |
