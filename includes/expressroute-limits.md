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
ms.openlocfilehash: d067f1af3d5479aef28ddf2290cebe3fe45726b0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733776"
---
| Erőforrás | Alapértelmezett/maximális korlát |
| --- | --- |
| Előfizetésenként az ExpressRoute-Kapcsolatcsoportok |10 |
| Az ExpressRoute-Kapcsolatcsoportok régiónként és előfizetésenként, az Azure Resource Managerrel |10 |
| Az Azure privát társviszony-létesítés ExpressRoute standard útvonalak maximális száma |4,000 |
| Az Azure privát társviszony-létesítéshez az ExpressRoute prémium bővítmény útvonalak maximális száma |10,000 |
| Az Azure privát társviszony-létesítés egy ExpressRoute-kapcsolat a virtuális hálózat címtere útvonalak maximális száma |200 | 
| A Microsoft Azure standard ExpressRoute-társviszony-létesítéshez útvonalak maximális száma |200 |
| Az ExpressRoute prémium bővítmény a Microsoft Azure-társviszony-létesítéshez útvonalak maximális száma |200 |
| Az ExpressRoute-Kapcsolatcsoportok társviszony-létesítési ugyanazon a helyen azonos virtuális hálózathoz társított maximális száma |4 |
| Az azonos virtuális hálózatban lévő másik társviszony-létesítési helyszínek társított ExpressRoute-Kapcsolatcsoportok maximális száma |> 4, függ az átjáró-alhálózat mérete|
| Virtuális hálózati kapcsolat ExpressRoute-kapcsolatcsoportonként megengedett száma |A következő táblázatban találja. |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>ExpressRoute-kapcsolatcsoportonként virtuális hálózatok száma
| **Kapcsolatcsoport méretétől** | **A Standard virtuális hálózati kapcsolatok száma** | **A Premium bővítmény virtuális hálózati kapcsolatok száma** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |

