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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "67622102"
---
| Resource | Alapértelmezett/maximális korlát |
| --- | --- |
| ExpressRoute áramkörök/előfizetés |10 |
| ExpressRoute áramkörök régiónként/előfizetéssel, Azure Resource Manager |10 |
| Az Azure Private-partnereknek a ExpressRoute standard használatával hirdetett útvonalak maximális száma |4,000 |
| Az Azure Private peering szolgáltatásban hirdetett útvonalak maximális száma az ExpressRoute Premium bővítménnyel |10,000 |
| Az Azure-beli privát társak által a VNet ExpressRoute-kapcsolatok esetében hirdetett útvonalak maximális száma |200 |
| A Microsoft-partnerek számára a ExpressRoute standard használatával hirdetett útvonalak maximális száma |200 |
| A Microsoft-partnerek számára a ExpressRoute Premium bővítménysel hirdetett útvonalak maximális száma |200 |
| Ugyanahhoz a virtuális hálózathoz ugyanahhoz a társi helyhez kapcsolódó ExpressRoute-áramkörök maximális száma |4 |
| Ugyanahhoz a virtuális hálózathoz kapcsolódó ExpressRoute-áramkörök maximális száma különböző egymásra vonatkozó helyen |4 |
| A ExpressRoute áramkörben engedélyezett virtuális hálózati kapcsolatok száma |Tekintse meg a [virtuális hálózatok száma ExpressRoute áramköri](#vnetpercircuit) táblában.  |

#### <a name="vnetpercircuit"></a>Virtuális hálózatok száma ExpressRoute áramkörben
| **Áramkör mérete** | **A standard szintű virtuális hálózati kapcsolatok száma** | **A prémium szintű bővítménysel rendelkező virtuális hálózati kapcsolatok száma** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 GB/s * |10 |100 |
| 100 GB/s * |10 |100 |

**100 GB/s ExpressRoute – csak közvetlen*
