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
ms.openlocfilehash: 5de227e5de5ef9b41f6e0f64db86b7195259f7d6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
ms.locfileid: "30196774"
---
* **PolicyBased:** PolicyBased VPN korábban megnevezése statikus útválasztó átjáró volt a klasszikus üzembe helyezési modellben. Házirendalapú VPN titkosításához, és irányítják a csomagokat a helyszíni hálózat és az Azure VNet közötti címelőtag-kombinációkkal konfigurált IPsec-házirendek alapján IPsec-alagutakon keresztül. A házirend (vagy forgalomválasztó) általában egy hozzáférési listaként van megadva a VPN-eszköz konfigurációjában. Egy szabályalapú VPN-típus értéke *PolicyBased*. Ha egy szabályalapú VPN-kapcsolattal, vegye figyelembe a következő korlátozások vonatkoznak:
  
  * A VPN-hálózatokhoz PolicyBased is **csak** használni, az alapszintű átjáró-Termékváltozat. A VPN-típus nem kompatibilis más átjáró SKU.
  * Akkor is csak 1 bújtatási beállítása PolicyBased VPN használatakor.
  * Csak használhat PolicyBased VPN S2S-kapcsolatok, és csak bizonyos konfigurációk. Legtöbb VPN Gateway-konfiguráció RouteBased VPN kell.
* **RouteBased**: VPN RouteBased korábban megnevezése dinamikus útválasztó átjáró volt a klasszikus üzembe helyezési modellben. RouteBased VPN-EK "útvonalakat" meg az IP-továbbítási vagy útvonalválasztási táblán, hogy a csomagokat a megfelelő alagútkapcsolatokhoz irányítsák a használja. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. A házirend (vagy forgalomválasztója) VPN RouteBased konfigurált bármely elem közöttiként (vagy helyettesítő karakterek). Egy RouteBased VPN-típus értéke *RouteBased*.
