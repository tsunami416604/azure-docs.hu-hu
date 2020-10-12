---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "74828945"
---
|**VPN- <br> átjáró <br> létrehozása** |**Termékváltozat**   | **S2S/Virtuális hálózatok közötti kapcsolat<br>alagutak** | **P2S<br> SSTP-kapcsolatok** | **P2S <br> IKEv2/OpenVPN-kapcsolatok** | **Összesített<br>átviteli sebesség tesztje** | **BGP** | **Zóna – redundáns** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generation1**|**Basic**   | Legfeljebb 10    | Legfeljebb 128  | Nem támogatott  | 100 Mbps  | Nem támogatott| Nem |
|**Generation1**|**VpnGw1**  | Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 250       | 650 Mbit/s  | Támogatott | Nem |
|**Generation1**|**VpnGw2**  | Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 500       | 1 Gbit/s    | Támogatott | Nem |
|**Generation1**|**VpnGw3**  | Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 1000      | 1,25 Gbps | Támogatott | Nem |
|**Generation1**|**VpnGw1AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 250       | 650 Mbit/s  | Támogatott | Igen |
|**Generation1**|**VpnGw2AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 500       | 1 Gbit/s    | Támogatott | Igen |
|**Generation1**|**VpnGw3AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 1000      | 1,25 Gbps | Támogatott | Igen |
|        |            |            |           |                |           |           |     |
|**Generation2**|**VpnGw2**  | Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 500       | 1,25 Gbps | Támogatott | Nem |
|**Generation2**|**VpnGw3**  | Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 1000      | 2,5 GB/s  | Támogatott | Nem |
|**Generation2**|**VpnGw4**  | Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 5000      | 5 Gbps    | Támogatott | Nem |
|**Generation2**|**VpnGw5**  | Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 10000      | 10 Gbps   | Támogatott | Nem |
|**Generation2**|**VpnGw2AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 500       | 1,25 Gbps | Támogatott | Igen |
|**Generation2**|**VpnGw3AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 1000      | 2,5 GB/s  | Támogatott | Igen |
|**Generation2**|**VpnGw4AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 5000      | 5 Gbps    | Támogatott | Igen |
|**Generation2**|**VpnGw5AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 10000      | 10 Gbps   | Támogatott | Igen |

(*) Ha 30 S2S VPN-alagútnál többre van szüksége, használja a [Virtual WAN-t](../articles/virtual-wan/virtual-wan-about.md).

* Az VpnGw SKU-i átméretezése ugyanazon a generáción belül engedélyezett, kivéve az alapszintű SKU átméretezését. Az alapszintű SKU egy örökölt SKU, és szolgáltatási korlátozásokkal rendelkezik. Ahhoz, hogy az alapszintű egy másik VpnGw SKU-ra váltson, törölnie kell az alapszintű SKU VPN-átjárót, és létre kell hoznia egy új átjárót a kívánt generációs és SKU-méret kombinációval.

* Ezek a kapcsolati korlátok egymástól függetlenek. Például egy VpnGw1 termékváltozat esetén rendelkezhet 128 SSTP-kapcsolattal és 250 IKEv2-kapcsolattal is.

* A díjakról a [Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway) oldalon tájékozódhat.

* A szolgáltatói szerződésekről információt az [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (szolgáltatói szerződés) oldalán találhat.

* Egyetlen alagúton legfeljebb 1 GB/s átviteli sebesség érhető el. A fenti táblázatban szereplő összesített átviteli sebesség a több, egyetlen átjárón keresztül összesít alagutak mérési eredményein alapul. A VPN-átjárók összesített átviteli sebességtesztje az S2S és a P2S ötvözésével készült. **Az átviteli sebességre vonatkozó korlátozások miatt negatív hatással lehet az S2S-kapcsolatokra, ha számos P2S-kapcsolattal rendelkezik.** Az összesített átviteli sebességre vonatkozó teljesítményteszt nem garantált átviteli sebesség az internetes forgalmi feltételek és az alkalmazás viselkedése miatt.

Annak érdekében, hogy ügyfeleink megértsék az SKU-ket a különböző algoritmusok használatával, a nyilvánosan elérhető iPerf-és CTSTraffic-eszközöket használták a teljesítmény mérésére. Az alábbi táblázat az 1. generációs VpnGw tartozó teljesítménytesztek eredményeit sorolja fel. Amint láthatja, a legjobb teljesítmény akkor jelenik meg, ha a GCMAES256 algoritmust is használták az IPsec-titkosításhoz és az integritáshoz. Az AES256 használata az IPsec-titkosításhoz és a SHA256 az integritás érdekében az átlagos teljesítmény volt. Ha a DES3 az IPsec-titkosításhoz és az SHA256-hez használta a legalacsonyabb teljesítményű integritás érdekében.

|**Generáció**|**Termékváltozat**   | **Használt algoritmusok <br>** | **Megfigyelt átviteli sebesség <br>** | **Másodpercenként <br> megfigyelt csomagok** |
|---           |---       | ---                 | ---            | ---                    |
|**Generation1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbit/s<br>500 Mbps<br>120 Mbps   | 58 000<br>50,000<br>50,000|
|**Generation1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55 000|
|**Generation1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105 000<br>90,000<br>60.000|
|**Generation1**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbit/s<br>500 Mbps<br>120 Mbps   | 58 000<br>50,000<br>50,000|
|**Generation1**|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55 000|
|**Generation1**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gbps<br>550 Mbps<br>120 Mbps | 105 000<br>90,000<br>60.000|
