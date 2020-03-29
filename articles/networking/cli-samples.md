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
ms.openlocfilehash: 21d8896e4f87b432908a2ccf6a994a5c29d4b293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840519"
---
# <a name="azure-cli-samples-for-networking"></a>Azure CLI-minták hálózatépítéshez

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Kapcsolat az Azure-erőforrások között**||
| [Virtuális hálózat létrehozása többrétegű alkalmazásokhoz](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat felé irányuló forgalom a HTTP- és az SSH-protokollon, a háttérbeli alhálózat felé irányuló forgalom pedig a MySQL-en, a 3306-os porton keresztül engedélyezett. |
| [Társviszony létesítése két virtuális hálózat között](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban. |
| [Forgalom irányítása hálózati virtuális készüléken keresztül](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal, valamint egy virtuális gépet, amely képes a két alhálózat közötti forgalom irányítására. |
| [Virtuális gép kimenő és bejövő hálózati forgalmának szűrése](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtér-alhálózatba irányuló bejövő hálózati forgalom http, HTTPS és SSH protokollra korlátozódik. A háttér-alhálózatból az internetre irányuló kimenő forgalom nem engedélyezett. |
|**Terheléselosztás és forgalom iránya**||
| [Több webhely terheléselosztása a virtuális gépeken](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Két virtuális gépet hoz létre több IP-konfigurációval, amelyek egy Azure-os terheléselosztón keresztül elérhető Azure rendelkezésre állási csoporthoz csatlakoznak. |
| [Közvetlen forgalom több régióban a magas rendelkezésre állás érdekében](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Két alkalmazásszolgáltatási csomag, két webalkalmazás, egy forgalomkezelő profil és két forgalomkezelő végpont létrehozása. |
| | |
