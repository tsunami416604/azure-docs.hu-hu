---
title: "Az Azure ExpressRoute bevezetésének előfeltételei | Microsoft Docs"
description: "Ez az oldal azon követelmények listáját tartalmazza, amelyeknek teljesülniük kell, mielőtt megrendel egy Azure ExpressRoute-kapcsolatcsoportot."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: f872d25e-acfd-405d-9d1b-dcb9f323a2ff
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/30/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 8629235511e0dda149ceef6a9c834c3042f64f90
ms.contentlocale: hu-hu
ms.lasthandoff: 07/04/2017

---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute-előfeltételek és ellenőrzőlista
Ahhoz, hogy az ExpressRoute-tal tudjon csatlakozni a Microsoft-felhőszolgáltatásokhoz, ellenőriznie kell az alábbi szakaszokban felsorolt követelmények teljesülését.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure-fiók
* Egy érvényes és aktív Microsoft Azure-fiók. Erre a fiókra az ExpressRoute-kapcsolatcsoport beállításához van szükség. Az ExpressRoute-kapcsolatcsoportok az Azure-előfizetéseken belüli erőforrások. Egy Azure-előfizetés akkor is szükséges, ha a kapcsolat csak nem Azure-os Microsoft-felhőszolgáltatásokra korlátozódik, például az Office 365-szolgáltatásokra és a Dynamics 365-re.
* Egy aktív Office 365-előfizetés (ha az Office 365 szolgáltatásokat használja). További információkért lásd a cikk [Office 365-specifikus követelmények](#office-365-specific-requirements) című szakaszát.

## <a name="connectivity-provider"></a>Kapcsolatszolgáltató

* Egy [ExpressRoute kapcsolati partnerrel](expressroute-locations.md#partners) együttműködve csatlakozhat a Microsoft Cloudhoz. A helyszíni hálózata és a Microsoft között [háromféleképpen](expressroute-introduction.md) állíthat be kapcsolatot.
* Ha a szolgáltató nem ExpressRoute-kapcsolatszolgáltató, akkor is csatlakozhat a Microsoft Cloudhoz egy [felhőalapú adatcsere-szolgáltatóval](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények
* **Redundáns kapcsolat**: az Ön és a szolgáltató közötti fizikai kapcsolatra nem vonatkoznak redundanciakövetelmények. A Microsoft nem követeli meg, hogy redundáns BGP-munkamenetek legyenek beállítva a Microsoft útválasztói és a társviszony-létesítési útválasztók között, még akkor sem, ha csak [egy fizikai kapcsolattal rendelkezik a felhőalapú adatforgalomhoz](expressroute-faqs.md#onep2plink).
* **Útválasztás**: attól függően, hogy hogyan csatlakozik a Microsoft Cloudhoz, Önnek vagy a szolgáltatójának BGP-munkameneteket kell beállítania és kezelnie az [útválasztási tartományokhoz](expressroute-circuit-peerings.md). Egyes Ethernet-kapcsolatszolgáltatók vagy felhőalapú adatcsere-szolgáltatók a BGP-felügyeletet értéknövelt szolgáltatásként kínálhatják.
* **NAT**: A Microsoft csak nyilvános IP-címeket fogad el a Microsoft társviszony-létesítésen keresztül. Ha privát IP-címeket használ a helyszíni hálózatban, Önnek vagy a szolgáltatónak [a NAT használatával](expressroute-nat.md) le kell fordítania a privát IP-címeket nyilvános IP-címekre.
* **QoS**: A Skype Vállalati verzió különböző szolgáltatásokat tartalmaz (például hang, videó, szöveg), amelyek különböző QoS-kezelést igényelnek. Önnek és a szolgáltatónak teljesítenie kell a [QoS-követelményeket](expressroute-qos.md).
* **Hálózati biztonság**: fontolja meg a [hálózati biztonság](../best-practices-network-security.md) használatát, amikor ExpressRoute-on keresztül csatlakozik a Microsoft Cloudhoz.

## <a name="office-365"></a>Office 365
Ha azt tervezi, hogy engedélyezi az Office 365-öt az ExpressRoute-on, tekintse át a következő dokumentumokat az Office 365 követelményeivel kapcsolatos további információkért.

* [Az Office 365-höz használt ExpressRoute áttekintése](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Útválasztás az Office 365-höz használt ExpressRoute-tal](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Office 365 URL-címek és IP-címtartományok](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Hálózattervezés és teljesítményhangolás az Office 365-höz](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Hálózatisávszélesség-kalkulátorok és eszközök](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Az Office 365 integrációja helyszíni környezetekkel](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [ExpressRoute az Office 365-ön haladó szintű oktatóvideók](https://channel9.msdn.com/series/aer/)

## <a name="dynamics-365"></a>Dynamics 365
Ha azt tervezi, hogy engedélyezi a Dynamics 365 szolgáltatást az ExpressRoute-on, tekintse át a következő dokumentumokat a Dynamics 365 követelményeivel kapcsolatos további információkért

* [Tanulmány – Dynamics 365 és ExpressRoute](http://download.microsoft.com/download/B/2/8/B2896B38-9832-417B-9836-9EF240C0A212/Microsoft%20Dynamics%20365%20and%20ExpressRoute.pdf)
* [Dynamics 365-ös URL-címek](https://support.microsoft.com/kb/2655102) és [IP-címtartományok](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Következő lépések
* További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
* Egy ExpressRoute-kapcsolatszolgáltató keresése. Lásd: [ExpressRoute-partnerek és társviszony-létesítési helyszínek](expressroute-locations.md).
* Tekintse meg az [Útválasztás](expressroute-routing.md), a [NAT](expressroute-nat.md) és a [QoS](expressroute-qos.md) követelményeit.
* Az ExpressRoute-kapcsolat konfigurálása.
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-classic.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-classic.md)

