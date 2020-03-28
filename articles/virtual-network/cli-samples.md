---
title: Azure CLI-minták virtuális hálózathoz
description: Azure CLI-minták virtuális hálózathoz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5ce10cf37b61b0269e6c8f2279b8814d9dc4a4f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78271206"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Azure CLI-minták virtuális hálózathoz

A következő táblázat az Azure CLI-parancsok használatával létrehozott Bash-szkriptekre mutató hivatkozásokat tartalmaz:

| | |
|----|----|
| [Virtuális hálózat létrehozása többrétegű alkalmazásokhoz](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat felé irányuló forgalom a HTTP- és az SSH-protokollon, a háttérbeli alhálózat felé irányuló forgalom pedig a MySQL-en, a 3306-os porton keresztül engedélyezett. |
| [Társviszony létesítése két virtuális hálózat között](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban. |
| [Forgalom irányítása hálózati virtuális készüléken keresztül](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal, valamint egy virtuális gépet, amely képes a két alhálózat közötti forgalom irányítására. |
| [Virtuális gép kimenő és bejövő hálózati forgalmának szűrése](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat bejövő hálózati forgalma HTTP-re, HTTPS-re és SSH-ra korlátozódik. A háttérbeli alhálózat felől az internetre irányuló kimenő forgalom nem engedélyezett. |
|[IPv4 + IPv6 kettős veremes virtuális hálózat konfigurálása egyszerű terheléselosztóval](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Kétverkező (IPv4+IPv6) virtuális hálózatot telepít két virtuális géptel és egy Azure Basic Load Balancer iPv4- és IPv6-alapú nyilvános IP-címekkel. |
|[IPv4 + IPv6 kettős veremes virtuális hálózat konfigurálása standard terheléselosztóval](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Kétverkező (IPv4+IPv6) virtuális hálózatot telepít két virtuális géptel és egy Azure Standard Load Balancer iPv4- és IPv6-alapú nyilvános IP-címekkel. |
|[Oktatóanyag: NAT-átjáró létrehozása és tesztelése – Azure CLI](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|NAT-átjáró létrehozása és érvényesítése forrás- és célvirtuális gép használatával. |