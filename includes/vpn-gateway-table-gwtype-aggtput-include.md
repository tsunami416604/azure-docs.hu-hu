---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 65c1011e6e005c190d1ae5d51fdd009f66a20956
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919687"
---
|**Termékváltozat**   | **S2S/Virtuális hálózatok közötti kapcsolat<br>alagutak** | **P2S<br> SSTP-kapcsolatok** | **P2S<br> IKEv2/OpenVPN-kapcsolatok** | **Összesített<br>átviteli sebesség tesztje** | **BGP** | **Zone-redundant** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Basic** | Legfeljebb 10    | Legfeljebb 128  | Nem támogatott  | 100 Mbps  | Nem támogatott| Nem |
|**VpnGw1**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 250       | 650 Mbps  | Támogatott | Nem |
|**VpnGw2**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 500       | 1 Gbps    | Támogatott | Nem |
|**VpnGw3**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 1000      | 1,25 Gbps | Támogatott | Nem |
|**VpnGw1AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 250       | 650 Mbps  | Támogatott | Igen |
|**VpnGw2AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 500       | 1 Gbps    | Támogatott | Igen |
|**VpnGw3AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 1000      | 1,25 Gbps | Támogatott | Igen |

(*) Ha 30 S2S VPN-alagútnál többre van szüksége, használja a [Virtual WAN-t](../articles/virtual-wan/virtual-wan-about.md).

* Ezek a kapcsolati korlátok egymástól függetlenek. Például egy VpnGw1 termékváltozat esetén rendelkezhet 128 SSTP-kapcsolattal és 250 IKEv2-kapcsolattal is.

* A díjakról a [Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway) oldalon tájékozódhat.

* A szolgáltatói szerződésekről információt az [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (szolgáltatói szerződés) oldalán találhat.

* A VpnGw1, a VpnGw2, a VpnGw3, a VpnGw1AZ, a VpnGw2AZ és a VpnGw3AZ csak a Resource Manager-alapú üzemi modellt használó VPN-átjárók esetében támogatott.

* Az alapszintű SKU egy örökölt SKU-nak minősül. Az alapszintű SKU bizonyos szolgáltatási korlátozásokkal rendelkezik. Alapszintű SKU-t használó átjárót nem lehet átméretezni az új átjárók egyikére, ehelyett egy új SKU-ra kell váltania, amely magában foglalja a VPN-átjáró törlését és újbóli létrehozását. Az alapszintű SKU használata előtt ellenőrizze, hogy az Ön által igényelt szolgáltatás támogatott-e.

* Az Összesített átviteli sebesség tesztje több alagút egyetlen átjárón keresztül összesített mérésein alapul. A VPN-átjárók összesített átviteli sebességtesztje az S2S és a P2S ötvözésével készült. **Az átviteli sebességre vonatkozó korlátozások miatt negatív hatással lehet az S2S-kapcsolatokra, ha számos P2S-kapcsolattal rendelkezik.** Az összesített átviteli sebességre vonatkozó teljesítményteszt nem garantált átviteli sebesség az internetes forgalmi feltételek és az alkalmazás viselkedése miatt.

* Annak érdekében, hogy ügyfeleink megértsék a VpnGw SKU-ket, a nyilvánosan elérhető iPerf és CTSTraffic eszközöket használtuk a teljesítmény mérésére. Az alábbi táblázat felsorolja a teljesítménytesztek eredményeit a különböző algoritmusok használatával. Amint láthatja, a legjobb teljesítmény akkor jelenik meg, ha a GCMAES256 algoritmust is használták az IPsec-titkosításhoz és az integritáshoz. Az AES256 használata az IPsec-titkosításhoz és a SHA256 az integritás érdekében az átlagos teljesítmény volt. Ha a DES3 az IPsec-titkosításhoz és az SHA256-hez használta a legalacsonyabb teljesítményű integritás érdekében.

|**Termékváltozat**   | **Használt<br>algoritmusok** | **Megfigyelt átviteli sebesség<br>** | **Másodpercenként<br>megfigyelt csomagok** |
|---       | ---                 | ---            | ---                    |
|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58 000<br>50,000<br>50,000|
|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55 000|
|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105 000<br>90,000<br>60,000|
|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58 000<br>50,000<br>50,000|
|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55 000|
|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105 000<br>90,000<br>60,000|
