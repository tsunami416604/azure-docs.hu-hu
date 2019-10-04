---
title: Azure CLI-minták virtuális hálózathoz | Microsoft Docs
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
ms.openlocfilehash: 4caa19ad22faa0a921fb6397e5b376b65a29e519
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249071"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Azure CLI-minták virtuális hálózathoz

A következő táblázat az Azure CLI-parancsok használatával létrehozott Bash-szkriptekre mutató hivatkozásokat tartalmaz:

| | |
|----|----|
| [Virtuális hálózat létrehozása többrétegű alkalmazásokhoz](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat felé irányuló forgalom a HTTP- és az SSH-protokollon, a háttérbeli alhálózat felé irányuló forgalom pedig a MySQL-en, a 3306-os porton keresztül engedélyezett. |
| [Társviszony létesítése két virtuális hálózat között](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban. |
| [Forgalom irányítása hálózati virtuális készüléken keresztül](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal, valamint egy virtuális gépet, amely képes a két alhálózat közötti forgalom irányítására. |
| [Virtuális gép kimenő és bejövő hálózati forgalmának szűrése](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat bejövő hálózati forgalma HTTP-re, HTTPS-re és SSH-ra korlátozódik. A háttérbeli alhálózat felől az internetre irányuló kimenő forgalom nem engedélyezett. |
|[Konfigurálhatja IPv4 + IPv6 kettős verem az alapszintű Load Balancer virtuális hálózat](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Kettős vermű (IPv4 + IPv6) két virtuális gépet, és az Azure alapszintű Load Balancerhez IPv4 és IPv6 típusú nyilvános IP-címekkel rendelkező virtuális hálózaton üzembe helyezi. |
|[Konfigurálhatja IPv4 + IPv6 kettős verem virtuális hálózatba a Standard Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Kettős vermű (IPv4 + IPv6) két virtuális gépet, és az Azure Standard Load Balancerhez IPv4 és IPv6 típusú nyilvános IP-címekkel rendelkező virtuális hálózaton üzembe helyezi. |