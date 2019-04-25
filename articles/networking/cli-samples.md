---
title: Azure CLI-minták | Microsoft Docs
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
ms.openlocfilehash: c612cdefa3f915212c43d7bdc4d48b10a593150b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60426072"
---
# <a name="azure-cli-samples-for-networking"></a>Hálózatkezelés az Azure CLI-minták

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Azure-erőforrások közötti kapcsolat**||
| [Virtuális hálózat létrehozása többrétegű alkalmazásokhoz](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat felé irányuló forgalom a HTTP- és az SSH-protokollon, a háttérbeli alhálózat felé irányuló forgalom pedig a MySQL-en, a 3306-os porton keresztül engedélyezett. |
| [Társviszony létesítése két virtuális hálózat között](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban. |
| [Forgalom irányítása hálózati virtuális készüléken keresztül](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal, valamint egy virtuális gépet, amely képes a két alhálózat közötti forgalom irányítására. |
| [Virtuális gép kimenő és bejövő hálózati forgalmának szűrése](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat bejövő hálózati forgalma HTTP, HTTPS és az SSH korlátozódik. A háttérbeli alhálózat felől az internetre irányuló kimenő forgalom nem engedélyezett. |
|**Terheléselosztás és az adatforgalom irányának betöltése**||
| [Bejövő forgalmának terheléselosztása virtuális gépek magas rendelkezésre állás érdekében](./scripts/load-balancer-linux-cli-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre. |
| [A virtuális gépek több webhely terheléselosztását kezelheti](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Két virtuális gépet hoz létre több IP-konfiguráció, csatlakozik az Azure rendelkezésre állási csoport, egy Azure Load Balanceren keresztül érhető el. |
| [Közvetlen forgalom több régióban a magas rendelkezésre állása](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Két app service-csomagot, két webalkalmazást, egy traffic manager-profilt és két traffic manager-végpontot hoz létre. |
| | |
