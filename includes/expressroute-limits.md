---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 54306b8bd8718a0a69f5b04ceaab15dff46d21a8
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978827"
---
| Erőforrás | Korlát |
| --- | --- |
| ExpressRoute áramkörök/előfizetés |10 |
| ExpressRoute áramkörök régiónként/előfizetéssel, Azure Resource Manager |10 |
| Az Azure Private-partnereknek a ExpressRoute standard használatával hirdetett útvonalak maximális száma |4,000 |
| Az Azure Private peering szolgáltatásban hirdetett útvonalak maximális száma az ExpressRoute Premium bővítménnyel |10,000 |
| Az Azure-beli privát társak által a VNet ExpressRoute-kapcsolatok esetében hirdetett útvonalak maximális száma |1000 |
| A Microsoft-partnerek számára a ExpressRoute standard használatával hirdetett útvonalak maximális száma |200 |
| A Microsoft-partnerek számára a ExpressRoute Premium bővítménysel hirdetett útvonalak maximális száma |200 |
| Ugyanahhoz a virtuális hálózathoz ugyanahhoz a társi helyhez kapcsolódó ExpressRoute-áramkörök maximális száma |4 |
| Ugyanahhoz a virtuális hálózathoz kapcsolódó ExpressRoute-áramkörök maximális száma különböző egymásra vonatkozó helyen |4 |
| A ExpressRoute áramkörben engedélyezett virtuális hálózati kapcsolatok száma |Tekintse meg a [virtuális hálózatok száma ExpressRoute áramköri](#vnetpercircuit) táblában.  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a> Virtuális hálózatok száma ExpressRoute áramkörben
| **Áramkör mérete** | **A standard szintű virtuális hálózati kapcsolatok száma** | **A prémium szintű bővítménysel rendelkező virtuális hálózati kapcsolatok száma** |
| --- | --- | --- |
| 50 Mbit/s |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbit/s |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 GB/s * |10 |100 |
| 100 GB/s * |10 |100 |

**100 GB/s ExpressRoute – csak közvetlen*

> [!NOTE]
> Global Reach kapcsolatok száma a ExpressRoute áramkör virtuális hálózati kapcsolatainak korlátja. A 10 GB/s-os prémium szintű áramkör például 5 Global Reach kapcsolatot és 95 kapcsolatot tesz lehetővé a ExpressRoute-átjárók vagy a 95-es Global Reach-kapcsolatokhoz, valamint az ExpressRoute-átjárókkal való 5 kapcsolathoz, vagy bármely más, az áramkörhöz tartozó 100-kapcsolatok korlátozásával.
