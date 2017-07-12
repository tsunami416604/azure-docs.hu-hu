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
ms.date: 06/30/2017
ms.author: kaanan
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 7fbdecd6fcaa784c5cdeebe46bf1991bfe730017
ms.contentlocale: hu-hu
ms.lasthandoff: 07/04/2017

---
<a id="expressroute-partners-and-peering-locations" class="xliff"></a>

# ExpressRoute-partnerek és társviszony-létesítési helyszínek.

> [!div class="op_single_selector"]
> * [Helyek szolgáltató alapján](expressroute-locations.md)
> * [Szolgáltatók hely alapján](expressroute-locations-providers.md)


A cikkben található táblázatok információkat nyújtanak az ExpressRoute-kapcsolatszolgáltatókkal, az ExpressRoute földrajzi lefedettségével, az ExpressRoute-on támogatott Microsoft-felhőszolgáltatásokkal és az ExpressRoute-rendszerintegrátorokkal (SI) kapcsolatban.

## <a name="partners"></a>ExpressRoute-kapcsolatszolgáltatók
Az ExpressRoute az összes Azure-régióban és -helyen támogatott. A következő térkép az Azure-régiók és ExpressRoute-helyek listáját tartalmazza. Az ExpressRoute-helyek azokra a helyekre utalnak, ahol a Microsoft több szolgáltatóval is társul.

![Helyek térképe][0]

Ha legalább egy ExpressRoute-helyhez csatlakozott egy geopolitikai régióban, az adott geopolitikai régióban lévő összes régióban hozzáférhet az Azure-szolgáltatásokhoz. 

<a id="azure-regions-to-expressroute-locations-within-a-geopolitical-region" class="xliff"></a>

### Az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Észak-Amerika** |USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Szilícium-völgy, Washington, D.C., Montreal, Québec város, Toronto |
| **Dél-Amerika** |Dél-Brazília |Sao Paulo |
| **Európa** |Észak-Európa, Nyugat-Európa, az Egyesült Királyság nyugati része, az Egyesült Királyság déli része |Amszterdam, Dublin, London, Newport (Wales), Párizs |
| **Ázsia** |Kelet-Ázsia, Délkelet-Ázsia |Hongkong, Szingapúr |
| **Japán** |Nyugat-Japán, Kelet-Japán |Oszaka, Tokió |
| **Ausztrália** |Délkelet-Ausztrália, Kelet-Ausztrália |Melbourne, Sydney |
| **India** |Nyugat-India, Közép-India, Dél-India |Csennai, Mumbai |
| **Dél-Korea** |Korea középső régiója, Korea déli régiója |Busan, Szöul |

<a id="regions-and-geopolitical-boundaries-for-national-clouds" class="xliff"></a>

### A régiók és az országos felhők geopolitikai határai
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |USA-beli államigazgatás – Iowa, USA-beli államigazgatás – Virginia, USA Védelmi Minisztériuma – Középső régió, USA Védelmi Minisztériuma – Keleti régió  |Chicago, Dallas, New York, Seattle, Szilícium-völgy, Washington, D.C. |
| **Kína** |Észak-Kína, Kelet-Kína |Peking, Sanghaj |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="locations"></a>Kapcsolatszolgáltatói helyek

Az alábbi táblázatban a kapcsolati helyek és az egyes helyekhez tartozó szolgáltatók láthatók. A szolgáltatók és a szolgáltatási helyek megtekintéséhez lásd: [Helyek szolgáltatók szerint](expressroute-locations.md#locations). 


<a id="production-azure" class="xliff"></a>

### Termelési Azure
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Amszterdam** |Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Internet Solutions – Cloud Connect, Interxion, KPN, Level 3 Communications, Megaport, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Verizon, Zayo Group |
| **Atlanta** |Equinix |
| **Busan** |LG CNS |
| **Csennai** | Airtel+, Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chicago** |AT&T NetBond, Comcast, Equinix, Level 3 Communications, Megaport, Verizon, Zayo Group |
| **Dallas** |Aryaka Networks, AT&T NetBond, Cologix, Equinix, Level 3 Communications, Megaport, Verizon, Zayo Group+ |
| **Denver** |CoreSite |
| **Dublin** |Colt, Telecity Group |
| **Hongkong** |Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Las Vegas** |Level 3 Communications+, Megaport |
| **London** |AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telehouse – KDDI, Telenor, Verizon, Vodafone, Zayo Group+ |
| **Los Angeles** |CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **Melbourne** |AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **Miami** |Megaport |
| **Montreal** |Bell Canada, Cologix |
| **Mumbai** |Airtel+, Tata Communications |
| **New York** |Coresite, Equinix, Megaport, Zayo Group |
| **Newport (Wales)** |Következő generációs adatok |
| **Oszaka** |Equinix, Internet Initiative Japan Inc. – IIJ, NTT Communications, NTT SmartConnect+, Softbank |
| **Párizs** |Colt, Interxion, Equinix, Orange+ |
| **Quebec város** | Megaport |
| **San Antonio** |Megaport |
| **Sao Paulo** |Ascenty Data Centers+, Equinix, Level 3 Communications, Telefonica, UOLDIVEO+ |
| **Seattle** |Equinix, Level 3 Communications, Megaport |
| **Szöul** |KINX, Sejong Telecom |
| **Szilícium-völgy** |Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink+, Comcast, Console, Equinix, Level 3 Communications, Megaport, Orange, Tata Communications, Verizon, Zayo Group |
| **Szingapúr** |Aryaka Networks, AT&T NetBond, British Telecom, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Verizon |
| **Sydney** |AARNet, AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Orange, Telstra Corporation, Verizon |
| **Tokió** |Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, Internet Initiative Japan Inc. – IIJ, NTT Communications, Softbank, Verizon |
| **Toronto** |AT&T NetBond, Bell Canada, Cologix, Console, Equinix, Megaport, Telus, Zayo Group |
| **Washington, D.C.** |Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Verizon, Zayo Group |

 **+** = hamarosan elérhető

<a id="national-cloud-environments" class="xliff"></a>

### Országos felhőkörnyezetek

<a id="us-government-cloud" class="xliff"></a>

### Az Egyesült Államok kormányának felhője
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, Level 3 Communications+, Verizon |
| **Szilícium-völgy** | Equinix, Level 3 Communications |
| **Seattle** | Equinix |
| **Washington, D.C.** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |

<a id="china" class="xliff"></a>

### Kína
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Peking** |China Telecom |
| **Sanghaj** |China Telecom |

További tudnivalókért lásd: [Az ExpressRoute Kínában](http://www.windowsazure.cn/home/features/expressroute/).

<a id="germany" class="xliff"></a>

### Németország
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Berlin** |Colt+, e-shelter, Megaport+ |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Kapcsolódás adatcsere-szolgáltatókon keresztül
Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](http://www.cologix.com/)
  * [Console](https://www.consoleconnect.com/partners/cloudsaas/)
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

## <a name="c1partners"></a>Kapcsolódás további szolgáltatókon keresztül
| **Hely** | **Exchange** | **Kapcsolatszolgáltatók** |
| --- | --- | --- |
| **Amszterdam** | Equinix, Telecity | Eurofiber, Fastweb S.p.A, Nianet |
| **Chicago** | Equinix | Windstream |
| **Dallas** | Equinix, Megaport | C3ntro Telecom, Cox Business, Data Foundry, Transtelco |
| **Frankfurt** | Telecity | Nianet, QSC AG |
| **Hongkong** | Equinix | Macroview Telecom |
| **London** | Equinix, euNetworks, Telecity | Bezeq International Ltd., Epsilon, Exponential E, HSO, NexGen Networks, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies. Inc, Cogeco Peer 1, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Lightower, Webair |
| **Seattle** |Equinix | Alaska Communications |
| **Szilícium-völgy** |Equinix | Cox Business, Windstream |
| **Szingapúr** |Equinix |1CLOUDSTAR, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokió** | Equinix | ARTERIA Networks Corporation |
| **Toronto** | Equinix | Airgate Technologies. Inc, Cogeco Peer 1, Rogers, Thinktel, Zirro|
| **Washington, D.C.** |Equinix | Altice Business, Gtt Communications Inc, Epsilon, Lightower, Masergy, Windstream |

<a id="expressroute-system-integrators" class="xliff"></a>

## ExpressRoute-rendszerintegrátorok
A privát kapcsolatok igény szerinti beállítása nehéz feladat lehet, a hálózat méretétől függően. A következő táblában felsorolt rendszerintegrátorok bármelyike segítségére lehet az ExpressRoute üzembe helyezésében.

| **Kontinens** | **Rendszerintegrátorok** |
| --- | --- |
| **Ázsia** |Avanade Inc., OneAs1a |
| **Ausztrália** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Európa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Észak-Amerika** |Avanade Inc., Equinix Professional Services, FlexManage, Perficient, Presidio |
| **Dél-Amerika** |Avanade Inc. |
<a id="next-steps" class="xliff"></a>

## Következő lépések
* További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Helyek térképe"

