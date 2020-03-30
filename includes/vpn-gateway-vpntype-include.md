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
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179001"
---
* **PolicyBased:** A policybased VPN-eket korábban statikus útválasztási átjáróknak nevezték a klasszikus telepítési modellben. A házirend-alapú VPN-ek titkosítják és irányítják a csomagokat az IPsec-alagutakon keresztül a helyszíni hálózat és az Azure virtuális hálózat közötti címelőtagok kombinációjával konfigurált IPsec-házirendek alapján. A házirend (vagy forgalomválasztó) általában egy hozzáférési listaként van megadva a VPN-eszköz konfigurációjában. A PolicyBased VPN-típus értéke *PolicyBased*. Házirendalapú VPN használata esetén tartsa szem előtt az alábbi korlátozásokat:
  
  * A policybased VPN-ek **csak** az alapszintű átjáró termékváltozatán használhatók. Ez a VPN-típus nem kompatibilis más átjárótermékkel.
  * Házirendalapú VPN használata esetén csak 1 alagutat használhat.
  * A PolicyBased VPN-ek csak S2S-kapcsolatokhoz használhatók, és csak bizonyos konfigurációkhoz. A legtöbb VPN-átjárókonfigurációhoz routealapú VPN szükséges.
* **RouteBased**: RouteBased VPN-eket korábban dinamikus útválasztási átjáróknak hívták a klasszikus telepítési modellben. A routealapú VPN-ek az IP-továbbítási vagy útválasztási táblában "útvonalakat" használnak a csomagok megfelelő bújtatási összeköttetéseikbe való irányításához. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. A routealapú VPN-ek házirendje (vagy forgalomválasztója) bármely bármely (vagy helyettesítő kártya) néven van konfigurálva. A RouteBased VPN-típus értéke *RouteBased*.
