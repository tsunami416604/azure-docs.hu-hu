---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211417"
---
A VPN-átjárók örökölt (régi) termékváltozatai:

* Alapértelmezett (alapszintű)
* Standard
* HighPerformance

A VPN Gateway nem használja az UltraPerformance átjáró-termékváltozatot. Az UltraPerformance termékváltozattal kapcsolatos információkért lásd az [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) dokumentációját.

Örökölt termékváltozatok használata esetén vegye figyelembe a következőket:

* PolicyBased-típusú VPN-ekhez az alapszintű átjáró-termékváltozatot kell használni. A PolicyBased VPN-eket (régebbi nevén Statikus útválasztás) semmilyen más termékváltozat nem támogatja.
* A BGP az alapszintű termékváltozathoz nem támogatott.
* Az alapszintű termékváltozat nem támogatja az ExpressRoute-VPN-átjárók egyszerre konfigurált beállításait.
* Aktív/aktív módú S2S VPN Gateway-kapcsolatokat csak a nagy teljesítményű termékváltozaton lehet konfigurálni.