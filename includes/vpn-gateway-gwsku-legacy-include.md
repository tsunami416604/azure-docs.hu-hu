---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: f2ee442d0d6fecf34449ad28f058615a1274bbea
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179007"
---
A VPN-átjárók örökölt (régi) termékváltozatai:

* Alapszintű
* Standard
* HighPerformance

A VPN Gateway nem használja az UltraPerformance átjáró-termékváltozatot. Az UltraPerformance termékváltozattal kapcsolatos információkért lásd az [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) dokumentációját.

Örökölt termékváltozatok használata esetén vegye figyelembe a következőket:

* PolicyBased-típusú VPN-ekhez az alapszintű átjáró-termékváltozatot kell használni. A PolicyBased VPN-eket (régebbi nevén Statikus útválasztás) semmilyen más termékváltozat nem támogatja.
* A BGP az alapszintű termékváltozathoz nem támogatott.
* Az alapszintű termékváltozat nem támogatja az ExpressRoute-VPN-átjárók egyszerre konfigurált beállításait.
* Aktív/aktív módú S2S VPN Gateway-kapcsolatokat csak a nagy teljesítményű termékváltozaton lehet konfigurálni.