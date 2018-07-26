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
ms.openlocfilehash: fad9b990b6ff1021efdaf8aadeb1e19d8a55871d
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39138110"
---
|**Termékváltozat**   | **S2S/Virtuális hálózatok közötti kapcsolat<br>alagutak** | **P2S<br>kapcsolatok** | **Összesített<br>átviteli sebesség tesztje** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Legfeljebb 30*                         | Legfeljebb 128**             | 650 Mbps                    |
|**VpnGw2**| Legfeljebb 30*                         | Legfeljebb 128**             | 1 Gbps                      |
|**VpnGw3**| Legfeljebb 30*                         | Legfeljebb 128**             | 1,25 Gbps                   |
|**Basic** | Legfeljebb 10                         | Legfeljebb 128               | 100 Mbps                    | 

* (*) Ha 30 S2S VPN-alagútnál többre van szüksége, használja a [Virtual WAN-t](../articles/virtual-wan/virtual-wan-about.md).

* (**) Ha további kapcsolatokra van szüksége, forduljon az ügyfélszolgálathoz

* Az Összesített átviteli sebesség tesztje több alagút egyetlen átjárón keresztül összesített mérésein alapul. A teljesítmény nem garantált az internetes forgalom körülményei és az alkalmazás viselkedése miatt.

* A díjakról a [Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway) oldalon tájékozódhat.

* A szolgáltatói szerződésekről információt az [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (szolgáltatói szerződés) oldalán találhat.

* A VpnGw1, a VpnGw2 és a VpnGw3 kizárólag Resource Manager-alapú üzemi modellt használó VPN-átjárók esetében támogatott.