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
ms.openlocfilehash: 1fc63dc53d61a8b2e26f97cc09a359b3f2c7665c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901861"
---
| Erőforrás | Alapértelmezett/maximális korlát |
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
| 50 Mb/s |10 |20 |
| 100 Mb/s |10 |25 |
| 200 Mb/s |10 |25 |
| 500 Mb/s |10 |40 |
| 1 Gb/s |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gb/s |10 |100 |
| 40 GB/s * |10 |100 |
| 100 GB/s * |10 |100 |

**100 GB/s ExpressRoute csak közvetlen*

> [!NOTE]
> Global Reach kapcsolatok száma a ExpressRoute áramkör virtuális hálózati kapcsolatainak korlátja. A 10 GB/s-os prémium szintű áramkör például 5 Global Reach kapcsolatot és 95 kapcsolatot tesz lehetővé a ExpressRoute-átjárók vagy a 95-es Global Reach-kapcsolatok, valamint az ExpressRoute-átjárók és a 100-as kapcsolatok korlátja között. az áramkörhöz.
