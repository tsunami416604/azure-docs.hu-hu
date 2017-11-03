---
title: "Az Azure CLI minták |} Microsoft Docs"
description: "Az Azure CLI-minták"
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: 7977460f61bfdabd399e45e86d9bbf2e5083992b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cli-samples-for-networking"></a>Hálózati Azure CLI-minták

A következő táblázat a bash parancsfájlok az Azure parancssori felület használatával készített mutató hivatkozásokat tartalmaz.

| | |
|-|-|
|**Azure-erőforrások közötti kapcsolat**||
| [A többrétegű alkalmazások virtuális hálózat létrehozása](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Előtér- és alhálózatok hoz létre a virtuális hálózat. Az előtér-alhálózat felé irányuló forgalom korlátozódik HTTP és az SSH-közben az adatforgalom a háttér-alhálózathoz MySQL, port 3306 korlátozódik. |
| [A két partner virtuális hálózatok](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hoz létre, és két virtuális hálózat ugyanabban a régióban kapcsolódik. |
| [A hálózati virtuális készülék-útvonal forgalmát](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hoz létre egy virtuális hálózati előtér- és alhálózatok és virtuális gép, amely képes irányíthatja a forgalmat a két alhálózat között. |
| [Bejövő és kimenő virtuális gép hálózati forgalom szűrésére](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Előtér- és alhálózatok hoz létre a virtuális hálózat. Az előtér-alhálózat bejövő hálózati forgalmának HTTP, HTTPS és SSH korlátozódik. Kimenő forgalom az internethez, a háttér-alhálózatból nem engedélyezett. |
|**Terheléselosztás és a forgalom iránya betöltése**||
| [Betöltési oszthatja el a forgalmat a virtuális gépek magas rendelkezésre álláshoz](./scripts/load-balancer-linux-cli-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Több magas rendelkezésre állású virtuális gépek és az elosztott terhelésű konfigurációs hoz létre. |
| [Terhelésének elosztása több webhely virtuális gépeken](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hoz létre két virtuális gépek több IP-konfigurációk, csatlakozik Azure rendelkezésre állási csoport, az Azure Load Balancer keresztül érhető el. |
| [A magas rendelkezésre állásának különféle régiókban közvetlen forgalom](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Két app service-csomagokról, a két web apps, a traffic manager-profil és a két traffic manager-végpont létrehozása |
| | |
