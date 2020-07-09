---
title: Azure CLI-minták virtuális hálózathoz
description: Azure CLI-minták virtuális hálózathoz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
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
ms.openlocfilehash: 7193a186359de2e19f1e1c56a7c2ee3ac2695e1d
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84707565"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Azure CLI-minták virtuális hálózathoz

A következő táblázat az Azure CLI-parancsok használatával létrehozott Bash-szkriptekre mutató hivatkozásokat tartalmaz:

| | |
|----|----|
| [Virtuális hálózat létrehozása többrétegű alkalmazásokhoz](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat felé irányuló forgalom a HTTP- és az SSH-protokollon, a háttérbeli alhálózat felé irányuló forgalom pedig a MySQL-en, a 3306-os porton keresztül engedélyezett. |
| [Társviszony létesítése két virtuális hálózat között](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban. |
| [Forgalom irányítása hálózati virtuális készüléken keresztül](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal, valamint egy virtuális gépet, amely képes a két alhálózat közötti forgalom irányítására. |
| [Virtuális gép kimenő és bejövő hálózati forgalmának szűrése](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat bejövő hálózati forgalma HTTP-re, HTTPS-re és SSH-ra korlátozódik. A háttérbeli alhálózat felől az internetre irányuló kimenő forgalom nem engedélyezett. |
|[Az IPv4 + IPv6 kettős verem virtuális hálózatának beállítása alapszintű Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Két virtuális géppel, valamint egy IPv4-és IPv6-alapú nyilvános IP-címmel rendelkező Azure alapszintű Load Balancer üzembe helyezi a kettős verem (IPv4 + IPv6) virtuális hálózatot. |
|[IPv4 + IPv6 kettős verem virtuális hálózatának konfigurálása standard Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Két virtuális gépre, valamint egy IPv4-és IPv6-alapú nyilvános IP-címmel rendelkező Azure-standard Load Balancer üzembe helyezi a kettős verem (IPv4 + IPv6) virtuális hálózatot. |
|[Oktatóanyag: NAT-átjáró létrehozása és tesztelése – Azure CLI](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|NAT-átjáró létrehozása és ellenőrzése forrás és cél virtuális gép használatával. |