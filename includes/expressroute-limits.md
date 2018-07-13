---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6e0d2b881e00acc6b1fccd3196187309fc36cba2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755714"
---
#### <a name="expressroute-limits"></a>Az ExpressRoute-korlátok
Az alábbi korlátok vonatkoznak, az ExpressRoute-erőforrásokra előfizetésenként.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Előfizetésenként az ExpressRoute-Kapcsolatcsoportok |10 |
| Az ExpressRoute-Kapcsolatcsoportok régiónként és előfizetésenként az ARM |10 |
| Az Azure privát társviszony-létesítés ExpressRoute standard útvonalak maximális száma |4,000 |
| Az Azure privát társviszony-létesítéshez az ExpressRoute prémium bővítmény útvonalak maximális száma |10,000 |
| Az Azure nyilvános társviszony-létesítési ExpressRoute standard útvonalak maximális száma |200 |
| Az Azure nyilvános társviszony-létesítéshez az ExpressRoute prémium bővítmény útvonalak maximális száma |200 |
| Az Azure a Microsoft társviszony-létesítési ExpressRoute standard útvonalak maximális száma |200 |
| Az Azure a Microsoft társviszony-létesítést az ExpressRoute prémium bővítmény útvonalak maximális száma |200 |
| Az azonos virtuális hálózatban lévő másik társviszony-létesítési helyszínek társított ExpressRoute-Kapcsolatcsoportok maximális száma |4 |
| Virtuális hálózati kapcsolat ExpressRoute-kapcsolatcsoportonként megengedett száma |Lásd az alábbi táblázatban |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>ExpressRoute-kapcsolatcsoportonként virtuális hálózatok száma
| **Kapcsolatcsoport méretétől** | **A standard szintű VNet-hivatkozások száma** | **A VNet-hivatkozások száma Premium bővítménnyel** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |

