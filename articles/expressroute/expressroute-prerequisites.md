---
title: 'Azure ExpressRoute: előfeltételek'
description: Ez az oldal azon követelmények listáját tartalmazza, amelyeknek teljesülniük kell, mielőtt megrendel egy Azure ExpressRoute-kapcsolatcsoportot. Ellenőrzőlista tartalmazza.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: a72eba9bde0745e66bdf8e7efd8eaec7d6a0b186
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083354"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute-előfeltételek és ellenőrzőlista
Ahhoz, hogy az ExpressRoute-tal tudjon csatlakozni a Microsoft-felhőszolgáltatásokhoz, ellenőriznie kell az alábbi szakaszokban felsorolt követelmények teljesülését.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure-fiók
* Egy érvényes és aktív Microsoft Azure-fiók. Erre a fiókra az ExpressRoute-kapcsolatcsoport beállításához van szükség. Az ExpressRoute-kapcsolatcsoportok az Azure-előfizetéseken belüli erőforrások. Az Azure-előfizetés akkor is követelmény, ha a kapcsolat nem Azure-beli Microsoft Cloud Services-szolgáltatásokra (például Office 365) korlátozódik.
* Egy aktív Office 365-előfizetés (ha az Office 365 szolgáltatásokat használja). További információkért tekintse meg a jelen cikk Office 365-specifikus követelmények című szakaszát.

## <a name="connectivity-provider"></a>Kapcsolatszolgáltató

* Egy [ExpressRoute kapcsolati partnerrel](expressroute-locations.md#partners) együttműködve csatlakozhat a Microsoft Cloudhoz. A helyszíni hálózata és a Microsoft között [háromféleképpen](expressroute-introduction.md) állíthat be kapcsolatot.
* Ha a szolgáltató nem ExpressRoute-kapcsolatszolgáltató, akkor is csatlakozhat a Microsoft Cloudhoz egy [felhőalapú adatcsere-szolgáltatóval](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények
* **Redundancia minden egyes egyenrangú helyen**: a Microsoft számára szükséges a redundáns BGP-munkamenetek beállítása a Microsoft útválasztói és a társítási útválasztók között minden ExpressRoute áramkörön (akkor is, ha csak [egy fizikai kapcsolat van a felhőalapú Exchange](expressroute-faqs.md#onep2plink)-hez).
* **Redundancia a vész-helyreállításhoz**: a Microsoft nyomatékosan javasolja, hogy legalább két ExpressRoute-áramkört hozzon létre különböző, egymástól eltérő helyeken, hogy elkerülje az adott meghibásodási pontot.
* **Útválasztás**: attól függően, hogy a Microsoft Cloud csatlakozik, akkor vagy a szolgáltató kell beállítása és kezelése a BGP-munkamenetet [útválasztási tartományok](expressroute-circuit-peerings.md). Egyes Ethernet-kapcsolatszolgáltatók vagy felhőalapú adatcsere-szolgáltatókon kínálhatják BGP-felügyeletet egy értéknövelt szolgáltatásként.
* **NAT**: A Microsoft csak nyilvános IP-címeket fogad el a Microsoft társviszony-létesítésen keresztül. Ha a helyszíni hálózat privát IP-címeket használ, Önnek vagy a szolgáltatónak kell a nyilvános IP-címek a privát IP-címek fordítása [a NAT használatával](expressroute-nat.md).
* **QoS**: A Skype Vállalati verzió különböző szolgáltatásokat tartalmaz (például hang, videó, szöveg), amelyek különböző QoS-kezelést igényelnek. Önnek és a szolgáltatónak teljesítenie kell a [QoS-követelményeket](expressroute-qos.md).
* **Hálózati biztonság**: fontolja meg a [hálózati biztonság](../best-practices-network-security.md) használatát, amikor ExpressRoute-on keresztül csatlakozik a Microsoft Cloudhoz.

## <a name="office-365"></a>Office 365
Ha azt tervezi, hogy engedélyezi az Office 365-öt az ExpressRoute-on, tekintse át a következő dokumentumokat az Office 365 követelményeivel kapcsolatos további információkért.

* [Az Office 365-höz használt ExpressRoute áttekintése](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Útválasztás az Office 365-höz használt ExpressRoute-tal](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Magas rendelkezésre állás és feladatátvétel az ExpressRoute használatával](https://aka.ms/erhighavailability)
* [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Hálózattervezés és teljesítményhangolás az Office 365-höz](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Hálózatisávszélesség-kalkulátorok és eszközök](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Az Office 365 integrációja helyszíni környezetekkel](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [ExpressRoute az Office 365-ön haladó szintű oktatóvideók](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Következő lépések
* További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
* Egy ExpressRoute-kapcsolatszolgáltató keresése. Lásd: [ExpressRoute-partnerek és társviszony-létesítési helyszínek](expressroute-locations.md).
* Tekintse meg az [Útválasztás](expressroute-routing.md), a [NAT](expressroute-nat.md) és a [QoS](expressroute-qos.md) követelményeit.
* Az ExpressRoute-kapcsolat konfigurálása.
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-arm.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)
