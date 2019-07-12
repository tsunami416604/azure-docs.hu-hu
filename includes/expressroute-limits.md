---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622102"
---
| Resource | Alapértelmezett/maximális korlát |
| --- | --- |
| Előfizetésenként az ExpressRoute-Kapcsolatcsoportok |10 |
| Az ExpressRoute-Kapcsolatcsoportok régiónként és előfizetésenként, az Azure Resource Managerrel |10 |
| Az Azure privát társviszony-létesítés ExpressRoute standard meghirdetett útvonalak maximális száma |4,000 |
| Azure privát társviszony-létesítést az ExpressRoute prémium bővítmény hirdetett útvonalakat maximális száma |10,000 |
| Az Azure privát társviszony-létesítés egy ExpressRoute-kapcsolat a virtuális hálózat címtere a meghirdetett útvonalak maximális száma |200 |
| A ExpressRoute Standard-társviszony Microsoft számára hirdetett útvonalakat maximális száma |200 |
| Microsoft társviszony-létesítést az ExpressRoute prémium bővítmény hirdetett útvonalakat maximális száma |200 |
| Az ExpressRoute-Kapcsolatcsoportok társviszony-létesítési ugyanazon a helyen azonos virtuális hálózathoz társított maximális száma |4 |
| Az azonos virtuális hálózatban lévő másik társviszony-létesítési helyszínek társított ExpressRoute-Kapcsolatcsoportok maximális száma |4 |
| Virtuális hálózati kapcsolat ExpressRoute-kapcsolatcsoportonként megengedett száma |Tekintse meg a [ExpressRoute-kapcsolatcsoportonként virtuális hálózatok száma](#vnetpercircuit) tábla.  |

#### <a name="vnetpercircuit"></a> ExpressRoute-kapcsolatcsoportonként virtuális hálózatok száma
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
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**Csak a 100 GB/s az ExpressRoute közvetlen*
