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
ms.openlocfilehash: 8fb9e9ea0e126509697b4874bf1e5e0b6a380e7f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66425756"
---
|**Termékváltozat**   | **S2S/Virtuális hálózatok közötti kapcsolat<br>alagutak** | **P2S<br> SSTP-kapcsolatok** | **P2S<br> IKEv2/OpenVPN kapcsolatok** | **Összesített<br>átviteli sebesség tesztje** | **BGP** | **Zone-redundant** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Basic** | Legfeljebb 10    | Legfeljebb 128  | Nem támogatott  | 100 Mbps  | Nem támogatott| Nem |
|**VpnGw1**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 250       | 650 Mbps  | Támogatott | Nem |
|**VpnGw2**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 500       | 1 Gbps    | Támogatott | Nem |
|**VpnGw3**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 1000      | 1,25 Gbps | Támogatott | Nem |
|**VpnGw1AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 250       | 650 Mbps  | Támogatott | Igen |
|**VpnGw2AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 500       | 1 Gbps    | Támogatott | Igen |
|**VpnGw3AZ**| Legfeljebb 30*   | Legfeljebb 128  | Legfeljebb 1000      | 1,25 Gbps | Támogatott | Igen |


(*) Ha 30 S2S VPN-alagútnál többre van szüksége, használja a [Virtual WAN-t](../articles/virtual-wan/virtual-wan-about.md).

* Az Összesített átviteli sebesség tesztje több alagút egyetlen átjárón keresztül összesített mérésein alapul. A VPN-átjárók összesített átviteli sebességtesztje az S2S és a P2S ötvözésével készült. **Az átviteli sebességre vonatkozó korlátozások miatt negatív hatással lehet az S2S-kapcsolatokra, ha számos P2S-kapcsolattal rendelkezik.** Az összesített átviteli sebesség tesztje nem egy garantált átviteli internetes forgalom körülményei és az alkalmazás viselkedése miatt.

* Ezek a kapcsolati korlátok egymástól függetlenek. Például egy VpnGw1 termékváltozat esetén rendelkezhet 128 SSTP-kapcsolattal és 250 IKEv2-kapcsolattal is.

* A díjakról a [Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway) oldalon tájékozódhat.

* A szolgáltatói szerződésekről információt az [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (szolgáltatói szerződés) oldalán találhat.

* A VpnGw1, a VpnGw2 és a VpnGw3 kizárólag Resource Manager-alapú üzemi modellt használó VPN-átjárók esetében támogatott.

* Az alapszintű Termékváltozat számít egy örökölt Termékváltozat. Az alapszintű Termékváltozat bizonyos funkció korlátozásokkal rendelkezik. Egy átjáróval, amely az új átjáró-termékváltozatok egyik alapszintű Termékváltozat nem méretezhetők át, az új Termékváltozatra, amely magában foglalja a törlését és újbóli létrehozását a VPN-átjáró inkább módosítania. Győződjön meg arról, hogy támogatott-e a szolgáltatás, amely van szüksége, az alapszintű Termékváltozat használata előtt.
