---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 05dc8ae48a9164e4f7118d378ab0eb7c30a4249e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
Egy virtuális hálózati átjáró létrehozásakor meg kell adni a használni kívánt termékváltozatot. Válassza ki a számítási feladatok, az átviteli sebesség, a funkciók és a szolgáltatói szerződés igényeinek megfelelő termékváltozatot.

###  <a name="benchmark"></a>Átjáró-termékváltozatok alagút, kapcsolat és átviteli sebesség szerint

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>Által szolgáltatáskészlet Gateway SKU-n

Az új VPN-átjáró termékváltozatok egyszerűsítésére átjáró kínált szolgáltatáskészletek:

| **Termékváltozat**| **Szolgáltatások**|
| ---    | ---         |
|**Alapszintű** (*)   | **Útvonalalapú VPN**: 10 alagutak rendelkező P2S; P2S; nincs RADIUS-hitelesítés nincs IKEv2 rendszer P2S<br>**Házirend-alapú VPN** (IKEv1): 1 alagút, P2S nélkül|
| **VpnGw1, VpnGw2 és VpnGw3** | **Útvonalalapú VPN**: legfeljebb 30 alagutak (*), P2S, a BGP, aktív-aktív, az egyéni IPsec/IKE házirend, ExpressRoute és a VPN-együttműködés |
|        |             |

( * ) A „PolicyBasedTrafficSelectors” paraméter konfigurálásával egy útvonalalapú VPN-átjárót (VpnGw1, VpnGw2, VpnGw3) több helyszíni, házirendalapú tűzfaleszközhöz is csatlakoztathat. További részletekért tekintse meg a [VPN-átjárók több helyszíni házirendalapú VPN-eszközhöz való csatlakoztatása a PowerShellel](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) című cikket.

(**) Az alapszintű Termékváltozat egy örökölt SKU minősül. Az alapszintű Termékváltozat bizonyos funkció korlátozásokkal rendelkezik. Nem méretezhető át egy átjáró, amely egy alapszintű Termékváltozat az új átjáró-termékváltozat egyikét használja, ezért módosítania kell helyette egy új másikra, amely magában foglalja a törlése és a VPN-átjáró újbóli létrehozása.

###  <a name="workloads"></a>Gateway SKU - éles vs. Dev-Test számítási feladatok

SLA-k és szolgáltatáskészletek eltérései miatt a következő termékváltozatok és fejlesztési-tesztelési célú üzemi javasoljuk:

| **Számítási feladat**                       | **Termékváltozatok**               |
| ---                                | ---                    |
| **Termelés, kritikus fontosságú számítási feladatok** | VpnGw1, VpnGw2, VpnGw3 |
| **Dev-test vagy a koncepció igazolása**   | Basic (**)                 |
|                                    |                        |

(**) Az alapszintű Termékváltozat egy örökölt SKU minősülnek, és a szolgáltatás korlátozások vonatkoznak. Győződjön meg arról, hogy a szükséges támogatja az alapszintű Termékváltozat használata előtt.

Ha a régi termékváltozatok (örökölt) használ, Standard és a HighPerformance által a termelési SKU ajánlások. További információt és útmutatást a régi SKU,: [Gateway SKU-n (örökölt)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).