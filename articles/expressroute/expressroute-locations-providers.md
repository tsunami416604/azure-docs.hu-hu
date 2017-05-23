---
title: "Helyek és kapcsolatszolgáltatók: Azure ExpressRoute | Microsoft Docs"
description: "A cikk részletes áttekintést nyújt a szolgáltatási helyekről és az Azure-régiókhoz való csatlakozásról. Hely alapján rendezve."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0a1aa87f0c1cc3fdc9f61788e528e3618e92a5a2
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017

---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partnerek és társviszony-létesítési helyszínek.

> [!div class="op_single_selector"]
> * [Helyek szolgáltató alapján](expressroute-locations.md)
> * [Szolgáltatók hely alapján](expressroute-locations-providers.md)


A cikkben található táblázatok információkat nyújtanak az ExpressRoute-kapcsolatszolgáltatókkal, az ExpressRoute földrajzi lefedettségével, az ExpressRoute-on támogatott Microsoft-felhőszolgáltatásokkal és az ExpressRoute-rendszerintegrátorokkal (SI) kapcsolatban.

## <a name="partners"></a>ExpressRoute-kapcsolatszolgáltatók
Az ExpressRoute az összes Azure-régióban és -helyen támogatott. A következő térkép az Azure-régiók és ExpressRoute-helyek listáját tartalmazza. Az ExpressRoute-helyek azokra a helyekre utalnak, ahol a Microsoft több szolgáltatóval is társul.

![Helyek térképe][0]

Ha legalább egy ExpressRoute-helyhez csatlakozott egy geopolitikai régióban, az adott geopolitikai régióban lévő összes régióban hozzáférhet az Azure-szolgáltatásokhoz. 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Észak-Amerika** |USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |Atlanta, Chicago, Dallas, Denver+, Las Vegas, Los Angeles, New York, Seattle, Szilícium-völgy, Washington, D.C., Montréal, Québec város, Toronto |
| **Dél-Amerika** |Dél-Brazília |Sao Paulo |
| **Európa** |Észak-Európa, Nyugat-Európa, az Egyesült Királyság nyugati része, az Egyesült Királyság déli része |Amszterdam, Dublin, London, Newport (Wales), Párizs |
| **Ázsia** |Kelet-Ázsia, Délkelet-Ázsia |Hongkong, Szingapúr |
| **Japán** |Nyugat-Japán, Kelet-Japán |Oszaka, Tokió |
| **Ausztrália** |Délkelet-Ausztrália, Kelet-Ausztrália |Melbourne, Sydney |
| **India** |Nyugat-India, Közép-India, Dél-India |Csennai, Mumbai |
| **Dél-Korea** |Korea középső régiója, Korea déli régiója |Busan, Szöul |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>A régiók és az országos felhők geopolitikai határai
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |USA-beli államigazgatás – Iowa, USA-beli államigazgatás – Virginia, USA Védelmi Minisztériuma – Középső régió, USA Védelmi Minisztériuma – Keleti régió  |Chicago, Dallas, New York, Seattle, Szilícium-völgy, Washington, D.C. |
| **Kína** |Észak-Kína, Kelet-Kína |Peking, Sanghaj |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="locations"></a>Kapcsolatszolgáltatói helyek

Az alábbi táblázatban a kapcsolati helyek és az egyes helyekhez tartozó szolgáltatók láthatók. A szolgáltatók és a szolgáltatási helyek megtekintéséhez lásd: [Helyek szolgáltatók szerint](expressroute-locations.md#locations). 


### <a name="production-azure"></a>Termelési Azure
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Amszterdam** |Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Internet Solutions - Cloud Connect, Interxion, KPN, Level 3 Communications, Orange, Tata Communications, TeleCity Group, Telefonica+, Telenor, Verizon |
| **Atlanta** |Equinix |
| **Busan** |LG CNS |
| **Csennai** |Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chicago** |AT&T NetBond, Comcast, Equinix, Level 3 Communications, Verizon, Zayo Group |
| **Dallas** |Aryaka Networks, AT&T NetBond, Cologix, Equinix, Level 3 Communications, Megaport, Verizon, Zayo Group+ |
| **Denver** |CoreSite+ |
| **Dublin** |Colt, Telecity Group |
| **Hongkong** |Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Las Vegas** |Level 3 Communications+, Megaport |
| **London** |AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telehouse – KDDI, Telenor, Verizon, Vodafone, Zayo Group+ |
| **Los Angeles** |CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **Melbourne** |AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **New York** |Coresite, Equinix, Megaport, Zayo Group |
| **Newport (Wales)** |Következő generációs adatok |
| **Montreal** |Bell Canada, Cologix |
| **Mumbai** |Airtel+, Tata Communications |
| **Oszaka** |Equinix, Internet Initiative Japan Inc. – IIJ, NTT Communications, Softbank |
| **Párizs** |Interxion, Equinix+ |
| **Quebec város** | Megaport |
| **Sao Paulo** |Equinix, Telefonica |
| **Seattle** |Equinix, Level 3 Communications, Megaport |
| **Szöul** |KINX, Sejong Telecom |
| **Szilícium-völgy** |Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink+, Comcast, Console, Equinix, Level 3 Communications, Orange, Tata Communications, Verizon, Zayo Group |
| **Szingapúr** |Aryaka Networks, AT&T NetBond, British Telecom, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Verizon |
| **Sydney** |AARNet, AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Orange, Telstra Corporation, Verizon |
| **Tokió** |Aryaka Networks, British Telecom, Colt, Equinix, Internet Initiative Japan Inc. – IIJ, NTT Communications, Softbank, Verizon |
| **Toronto** |Bell Canada, Cologix, Console, Equinix, Megaport, Zayo Group |
| **Washington DC** |Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Verizon, Zayo Group |

 **+** = hamarosan elérhető

### <a name="national-cloud-environments"></a>Országos felhőkörnyezetek

### <a name="us-government-cloud"></a>Az Egyesült Államok kormányának felhője
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, Level 3 Communications+, Verizon |
| **Szilícium-völgy** | Equinix |
| **Seattle** | Equinix |
| **Washington DC** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |

### <a name="china"></a>Kína
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Peking** |China Telecom |
| **Sanghaj** |China Telecom |

További tudnivalókért lásd: [Az ExpressRoute Kínában](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Németország
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Berlin** |Colt+, e-shelter, Megaport+ |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Kapcsolódás a listában nem szereplő szolgáltatókon keresztül
Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](http://www.interxion.com/)
  * [NextDC](http://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Kérje meg a kapcsolatszolgáltatót, hogy terjessze ki a hálózatot a választott társviszony-létesítési helyszínre.
  * Győződjön meg róla, hogy a kapcsolatszolgáltató magas rendelkezésre állással terjesztette ki a kapcsolatot, tehát nincsenek kritikus hibapontok a rendszeren belül.
* Rendeljen meg egy ExpressRoute-kapcsolatcsoportot, ahol az adatcserélő a Microsofthoz kapcsolódó kapcsolatszolgáltató.
  * Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md) című témakör lépéseit a kapcsolat beállításához.

| **Hely** | **Exchange** | **Kapcsolatszolgáltatók** |
| --- | --- | --- |
| **Amszterdam** | Equinix, Telecity | Eurofiber, Fastweb S.p.A, Nianet |
| **Chicago** | Equinix | Windstream |
| **Dallas** | Equinix, Megaport | C3ntro, Data Foundry, Transtelco |
| **Frankfurt** | Telecity | Nianet, QSC AG |
| **London** | Equinix, euNetworks, Telecity | Bezeq International Ltd., Exponential E, HSO, NexGen Networks, Tamares Telecom |
| **Los Angeles** | Equinix |Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies. Inc, Cogeco Peer 1 |
| **New York** |Equinix | Lightower |
| **Seattle** |Equinix | Alaska Communications |
| **Szilícium-völgy** |Equinix | Windstream |
| **Szingapúr** |Equinix |1CLOUDSTAR, Epsilon Telecommunications Limited |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokió** | Equinix | ARTERIA Networks Corporation |
| **Toronto** | Equinix | Airgate Technologies. Inc, Cogeco Peer 1 |
| **Washington DC** |Equinix | Lightower, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute-rendszerintegrátorok
A privát kapcsolatok igény szerinti beállítása nehéz feladat lehet, a hálózat méretétől függően. A következő táblában felsorolt rendszerintegrátorok bármelyike segítségére lehet az ExpressRoute üzembe helyezésében.

| **Kontinens** | **Rendszerintegrátorok** |
| --- | --- |
| **Ázsia** |Avanade Inc., OneAs1a |
| **Ausztrália** | IT Consultancy, Vigilant.IT |
| **Európa** |Avanade Inc., Altogee, Bright Skies GmbH, Dotnet Solutions, MSG Services, Nelite, Orange Networks, sol-tec |
| **Észak-Amerika** |Avanade Inc., Equinix Professional Services, Perficient, Presidio, Project Leadership |
| **Dél-Amerika** |Avanade Inc. |
## <a name="next-steps"></a>Következő lépések
* További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Helyek térképe"

