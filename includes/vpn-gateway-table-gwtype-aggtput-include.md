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
ms.openlocfilehash: c9457e51858d4a073d8baffdd435c8100d95d566
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
|**Termékváltozat**   | **S2S/Virtuális hálózatok közötti kapcsolat<br>alagutak** | **P2S<br>kapcsolatok** | **Összesített<br>átviteli sebesség tesztje** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Legfeljebb 30                         | Legfeljebb 128               | 650 Mbps                    |
|**VpnGw2**| Legfeljebb 30                         | Legfeljebb 128               | 1 Gbps                      |
|**VpnGw3**| Legfeljebb 30                         | Legfeljebb 128               | 1,25 Gbps                   |
|**Basic** | Legfeljebb 10                         | Legfeljebb 128               | 100 Mbps                    | 
|          |                                 |                        |                             | 

- Az Összesített átviteli sebesség tesztje több alagút egyetlen átjárón keresztül összesített mérésein alapul. A teljesítmény nem garantált az internetes forgalom körülményei és az alkalmazás viselkedése miatt.

- A díjakról a [Díjszabás](https://azure.microsoft.com/pricing/details/vpn-gateway) oldalon tájékozódhat.

- A szolgáltatói szerződésekről információt az [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (szolgáltatói szerződés) oldalán találhat.

- VPN-átjárók használata csak a Resource Manager üzembe helyezési modellel VpnGw1 VpnGw2 és VpnGw3 támogatottak.