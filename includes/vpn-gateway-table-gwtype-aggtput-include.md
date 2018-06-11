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
ms.openlocfilehash: bc42697f756ec75d9a8f2c20c99b28b2f7886ca1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34670275"
---
|**Termékváltozat**   | **S2S/Virtuális hálózatok közötti kapcsolat<br>alagutak** | **P2S<br>kapcsolatok** | **Összesített<br>átviteli sebesség tesztje** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Legfeljebb 30                         | Legfeljebb 128*              | 650 Mbps                    |
|**VpnGw2**| Legfeljebb 30                         | Legfeljebb 128*              | 1 Gbps                      |
|**VpnGw3**| Legfeljebb 30                         | Legfeljebb 128*              | 1,25 Gbps                   |
|**Basic** | Legfeljebb 10                         | Legfeljebb 128               | 100 Mbps                    | 

*Ha további kapcsolatokra van szüksége, forduljon az ügyfélszolgálathoz.
- Az Összesített átviteli sebesség tesztje több alagút egyetlen átjárón keresztül összesített mérésein alapul. A teljesítmény nem garantált az internetes forgalom körülményei és az alkalmazás viselkedése miatt.

- A díjakról a [Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway) oldalon tájékozódhat.

- A szolgáltatói szerződésekről információt az [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (szolgáltatói szerződés) oldalán találhat.

- A VpnGw1, a VpnGw2 és a VpnGw3 kizárólag Resource Manager-alapú üzemi modellt használó VPN-átjárók esetében támogatott.