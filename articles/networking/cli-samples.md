---
title: Azure CLI-minták – hálózatkezelés
description: Azure CLI-minták
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: ae8ea3db10f1a40fe4d3bbca94db61d425b6092d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091466"
---
# <a name="azure-cli-samples-for-networking"></a>Azure CLI-minták hálózatkezeléshez

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Az Azure-erőforrások közötti kapcsolat**||
| [Virtuális hálózat létrehozása többrétegű alkalmazásokhoz](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat felé irányuló forgalom a HTTP- és az SSH-protokollon, a háttérbeli alhálózat felé irányuló forgalom pedig a MySQL-en, a 3306-os porton keresztül engedélyezett. |
| [Társviszony létesítése két virtuális hálózat között](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban. |
| [Forgalom irányítása hálózati virtuális készüléken keresztül](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal, valamint egy virtuális gépet, amely képes a két alhálózat közötti forgalom irányítására. |
| [Virtuális gép kimenő és bejövő hálózati forgalmának szűrése](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtér-alhálózatra irányuló bejövő hálózati forgalom HTTP, HTTPS és SSH rendszerre korlátozódik. A háttérbeli alhálózatról az internetre irányuló kimenő forgalom nem engedélyezett. |
|**Terheléselosztás és forgalom iránya**||
| [Bejövő forgalmának terheléselosztása virtuális gépek magas rendelkezésre állás érdekében](./scripts/load-balancer-linux-cli-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre. |
| [A virtuális gépek több webhely terheléselosztását kezelheti](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Két virtuális gépet hoz létre több IP-konfiguráció, csatlakozik az Azure rendelkezésre állási csoport, egy Azure Load Balanceren keresztül érhető el. |
| [Közvetlen forgalom több régióban az alkalmazások magas rendelkezésre állása érdekében](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Két app Service-csomagot, két webalkalmazást, egy Traffic Manager-profilt és két Traffic Manager-végpontot hoz létre. |
| | |
