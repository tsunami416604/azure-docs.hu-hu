---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 75152fabfc33dda0494d871fbdf9a388f4260c0d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495744"
---
Egy virtuális hálózati átjáró létrehozásakor meg kell adni a használni kívánt termékváltozatot. Válassza ki a számítási feladatok, az átviteli sebesség, a funkciók és a szolgáltatói szerződés igényeinek megfelelő termékváltozatot. A Azure Availability Zones virtuális hálózati átjárói SKU-ban tekintse meg a [Azure Availability Zones Gateway SKU](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)-ket.

###  <a name="benchmark"></a>Átjáró-termékváltozatok alagút, kapcsolat és átviteli sebesség szerint

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

###  <a name="feature"></a>Átjárók SKU-ként a szolgáltatáskészlet alapján

Az új VPN Gateway SKU-ra egyszerűsíti az átjárók által kínált szolgáltatáskészlet-készleteket:

| **Termékváltozat**| **Szolgáltatások**|
| ---    | ---         |
|**Alapszintű** (* *)   | **Útvonal-alapú VPN**: 10 alagút a S2S/kapcsolatok számára; a P2S nem rendelkezik RADIUS-hitelesítéssel; nincs IKEv2 a P2S<br>**Házirend alapú VPN**: (IKEv1): 1 S2S/kapcsolati alagút; nincs P2S|
| **Az összes Generation1 és Generation2 SKU, kivéve az alapszintű** | **Route-alapú VPN**: legfeljebb 30 alagút (*), P2S, BGP, aktív-aktív, egyéni IPSec/IKE-házirend, EXPRESSROUTE/VPN együttes létezése |
|        |             |

(*) A "PolicyBasedTrafficSelectors" konfigurálható úgy, hogy egy Route-alapú VPN-átjárót több helyszíni házirend-alapú tűzfallal csatlakoztasson. További részletekért tekintse meg a [VPN-átjárók több helyszíni házirendalapú VPN-eszközhöz való csatlakoztatása a PowerShellel](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) című cikket.

(\*\*) Az alapszintű SKU egy örökölt SKU-nak minősül. Az alapszintű SKU bizonyos szolgáltatási korlátozásokkal rendelkezik. Alapszintű SKU-t használó átjárót nem lehet átméretezni az új átjárók egyikére, ehelyett egy új SKU-ra kell váltania, amely magában foglalja a VPN-átjáró törlését és újbóli létrehozását.

###  <a name="workloads"></a>Gateway SKU-gyártás és fejlesztési-tesztelési feladatok

A SLA-kat és a szolgáltatáskészlet-készletekben mutatkozó különbségek miatt a következő SKU-ket ajánljuk a termeléshez és a dev-testhez:

| **Számítási feladat**                       | **Termékváltozatok**               |
| ---                                | ---                    |
| **Termelés, kritikus fontosságú számítási feladatok** | Az összes Generation1 és Generation2 SKU, kivéve az alapszintű |
| **Dev-test vagy a koncepció igazolása**   | Alapszintű (* *)                 |
|                                    |                        |

(\*\*) Az alapszintű SKU egy örökölt SKU-nak minősül, és szolgáltatási korlátozásokkal rendelkezik. Az alapszintű SKU használata előtt ellenőrizze, hogy az Ön által igényelt szolgáltatás támogatott-e.

Ha a régi SKU-t (örökölt) használja, a termelési SKU-javaslatok standard és HighPerformance. A régi SKU-ra vonatkozó információkkal és utasításokkal kapcsolatban lásd: [átjáró SKU-i (örökölt)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
