---
title: Azure PowerShell-minták virtuális hálózathoz | Microsoft Docs
description: Azure PowerShell-minták virtuális hálózathoz.
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
ms.date: 04/17/2019
ms.author: kumud
ms.openlocfilehash: e10f7ad4ae8995d9efffec97a37dc64b28fa37b0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688021"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Azure PowerShell-minták virtuális hálózathoz

A következő táblázat Azure PowerShell-szkriptekre mutató hivatkozásokat tartalmaz:

| | |
|----|----|
| [Virtuális hálózat létrehozása többrétegű alkalmazásokhoz](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat felé irányuló forgalom HTTP-re, a háttérbeli alhálózat felé irányuló forgalom pedig az SQL-re, az 1433-as portra korlátozódik. |
| [Társviszony létesítése két virtuális hálózat között](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban. |
| [Forgalom irányítása hálózati virtuális készüléken keresztül](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal, valamint egy virtuális gépet, amely képes a két alhálózat közötti forgalom irányítására. |
| [Virtuális gép kimenő és bejövő hálózati forgalmának szűrése](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Egy virtuális hálózatot hoz létre előtérbeli és háttérbeli alhálózatokkal. Az előtérbeli alhálózat bejövő hálózati forgalma HTTP-re és HTTPS-re korlátozódik. A háttérbeli alhálózat felől az internetre irányuló kimenő forgalom nem engedélyezett. |
|[Konfigurálhatja IPv4 + IPv6 kettős verem virtuális hálózat](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Kettős vermű (IPv4 + IPv6) két virtuális gépet, és az Azure alapszintű Load Balancerhez IPv4 és IPv6 típusú nyilvános IP-címekkel rendelkező virtuális hálózaton üzembe helyezi. |
