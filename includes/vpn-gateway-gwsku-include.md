---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74085245"
---
Egy virtuális hálózati átjáró létrehozásakor meg kell adni a használni kívánt termékváltozatot. Válassza ki a számítási feladatok, az átviteli sebesség, a funkciók és a szolgáltatói szerződés igényeinek megfelelő termékváltozatot. Az Azure rendelkezésre állási zónáiban található virtuális hálózati átjáró termékfelhasználóiról az [Azure availability zones átjárótermék- és csoportkészletei.](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>Átjáró-termékváltozatok alagút, kapcsolat és átviteli sebesség szerint

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> A VpnGw SKUs (VpnGw1, VpnGw1AZ, VpnGw22, VpnGw2AZ, VpnGw3, VpnGw4, VpnGw4, VpnGw5 és VpnGw5AZ) csak az Erőforrás-kezelő telepítési modelljéhez támogatott. A klasszikus virtuális hálózatok továbbra is a régi (örökölt) skus-okat kell használniuk.
>  * Az örökölt átjáró termékkészletekkel (alapszintű, szabványos és nagy teljesítményű) való munkáról a [VPN-átjáró termékkészletei (örökölt termékkészletek) című](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)témakörben talál.
>  * ExpressRoute-átjáró-ska-król az [ExpressRoute virtuális hálózati átjárói.](../articles/expressroute/expressroute-about-virtual-network-gateways.md)
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>Átjáró-skin szolgáltatáskészlet szerint

Az új VPN-átjáró termékfelhasználói egyszerűsítik az átjárókon kínált szolgáltatáskészleteket:

| **Sku**| **Szolgáltatások**|
| ---    | ---         |
|**Alap** (**)   | **Útvonalalapú VPN:** 10 alagút S2S/kapcsolatokhoz; nincs RADIUS-hitelesítés a P2S-hez; nincs IKEv2 a P2S-hez<br>**Házirendalapú VPN**: (IKEv1): 1 S2S/kapcsolatalagút; nincs P2S|
| **Minden 1. generációs és 2.** | **Útvonalalapú VPN:** legfeljebb 30 alagút (*), P2S, BGP, aktív-aktív, egyéni IPsec/IKE házirend, ExpressRoute/VPN egymás mellett élés |
|        |             |

(*) Konfigurálhatja a "PolicyBasedTrafficSelectors" egy útvonal-alapú VPN-átjáró több helyszíni házirend-alapú tűzfaleszközök csatlakoztatásához. További részletekért tekintse meg a [VPN-átjárók több helyszíni házirendalapú VPN-eszközhöz való csatlakoztatása a PowerShellel](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) című cikket.

(\*\*) Az alapszintű termékváltozat örökölt termékváltozatnak számít. Az alapszintű termékváltozat bizonyos funkciókorlátozásokkal rendelkezik. Nem méretezhet át egy átjárót, amely egy egyszerű termékváltozatot használ az új átjáró termékváltozatok egyikéhez, ehelyett át kell váltania egy új termékváltozatra, amely magában foglalja a VPN-átjáró törlését és újbóli létrehozását.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>Átjáró-ska -termelés és a fejlesztési-tesztelési számítási feladatok

A szolgáltatói szerződések és a szolgáltatáskészletek eltérései miatt az alábbi termékváltozatokat javasoljuk termelés vs. dev-test környezetekhez:

| **Munkateher**                       | **Termékváltozatok**               |
| ---                                | ---                    |
| **Termelés, kritikus fontosságú számítási feladatok** | Minden 1. generációs és 2. |
| **Dev-test vagy a koncepció igazolása**   | Alap (**)                 |
|                                    |                        |

(\*\*) Az alapszintű termékváltozat örökölt termékváltozatnak minősül, és szolgáltatáskorlátozásokkal rendelkezik. Az alaptermékváltozat használata előtt ellenőrizze, hogy a szükséges szolgáltatás támogatott-e.

Ha a régi termékváltozatokat (örökölt) használja, az éles termékváltozat-javaslatok standard és nagy teljesítményűek. A régi sk-okról és utasításokról az [Átjáró-ski-k (örökölt)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)című témakörben talál.
