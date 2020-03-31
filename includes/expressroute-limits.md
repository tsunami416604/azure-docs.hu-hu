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
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334784"
---
| Erőforrás | Korlát |
| --- | --- |
| ExpressRoute-áramkörök előfizetésenként |10 |
| ExpressRoute-áramkörök régiónként és előfizetésenként, az Azure Resource Manager rel |10 |
| Az ExpressRoute Standard szolgáltatással az Azure privát társviszony-létesítési útvonalai nak maximális száma |4,000 |
| Az ExpressRoute Premium bővítménysel az Azure privát társviszony-létesítési útvonalai maximális száma |10,000 |
| Az Azure-beli privát társviszony-létesítésből meghirdetett útvonalak maximális száma az ExpressRoute-kapcsolat virtuális hálózati címterületéről |200 |
| A Microsoft ExpressRoute Standard szolgáltatással történő társviszony-létesítésre meghirdetett útvonalak maximális száma |200 |
| A Microsoft-társviszony-létesítésre az ExpressRoute Premium bővítménysel meghirdetett útvonalak maximális száma |200 |
| Ugyanazon a virtuális hálózathoz ugyanahhoz a társviszony-létesítési helyen lévő ExpressRoute-áramkörök maximális száma |4 |
| Az azonos virtuális hálózathoz különböző társviszony-létesítési helyeken lévő ExpressRoute-áramkörök maximális száma |4 |
| Engedélyezett virtuális hálózati kapcsolatok száma ExpressRoute-kapcsolatonként |Tekintse meg a [virtuális hálózatok száma ExpressRoute-áramköri táblánként.](#vnetpercircuit)  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Virtuális hálózatok száma ExpressRoute-kapcsolatonként
| **Áramkör mérete** | **A standard hoz való virtuális hálózati kapcsolatok száma** | **Virtuális hálózati kapcsolatok száma prémium bővítőhe-val** |
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

**Csak 100 Gb/s sebességű ExpressRoute közvetlen*

> [!NOTE]
> A globális elérési kapcsolatok az ExpressRoute-kapcsolatonkénti virtuális hálózati kapcsolatok korláthoz számítanak. Egy 10 Gb/s-os prémium verziópéldául 5 globális elérési kapcsolatot és 95 kapcsolatot engedélyezne az ExpressRoute átjárókhoz vagy 95 globális elérési kapcsolathoz, valamint 5 kapcsolatot az ExpressRoute-átjárókhoz vagy bármely más kombinációhoz a 100-as határig. az áramkörhöz.
