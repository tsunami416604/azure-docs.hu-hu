---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64859057"
---
* **Házirendalapú:** Házirendalapú VPN-eket korábban megnevezése statikus útválasztó átjáró volt a klasszikus üzemi modellben. Házirendalapú VPN-eket titkosítják és irányítják a csomagokat a helyszíni hálózat és az Azure VNet közötti címelőtag-kombinációkkal konfigurált IPsec-házirendek alapján IPsec-alagutakon keresztül. A házirend (vagy forgalomválasztó) általában egy hozzáférési listaként van megadva a VPN-eszköz konfigurációjában. Házirendalapú VPN-típus értéke *PolicyBased*. Ha használ a házirendalapú VPN, vegye figyelembe a következő korlátozások vonatkoznak:
  
  * Házirendalapú VPN-eket is **csak** az alapszintű átjáró-Termékváltozatot kell használni. A VPN típus nem kompatibilis a más átjáró-termékváltozatokat használják.
  * Ha használ a házirendalapú VPN csak 1 alagút rendelkezhet.
  * Házirendalapú VPN-eket S2S-kapcsolatokat, és csak bizonyos konfigurációk csak használható. A legtöbb VPN Gateway-konfigurációk egy Útvonalalapú VPN van szükség.
* **Útvonalalapú**: Útvonalalapú VPN-ekre korábban megnevezése dinamikus útválasztó átjáró volt a klasszikus üzemi modellben. Útvonalalapú VPN-EK "útvonalakat" használnak az IP-továbbítási vagy útvonalválasztási táblán a csomagokat, azokat a megfelelő alagútkapcsolatokhoz irányítsák. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. A házirend (vagy forgalomválasztó) Útvonalalapú VPN-EK megtörténik-bármely, (vagy helyettesítő karakterekként). Útvonalalapú VPN-típus értéke *RouteBased*.