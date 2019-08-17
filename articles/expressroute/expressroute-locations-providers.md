---
title: 'Helyszínek és kapcsolódási szolgáltatók: Azure ExpressRoute | Microsoft Docs'
description: A cikk részletes áttekintést nyújt a szolgáltatási helyekről és az Azure-régiókhoz való csatlakozásról. Hely alapján rendezve.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: cherylmc
ms.openlocfilehash: cce14a2f6e7f8aeca6c7f716d66520cef9484dde
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562637"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partnerek és társviszony-létesítési helyszínek.

> [!div class="op_single_selector"]
> * [Helyek szolgáltató alapján](expressroute-locations.md)
> * [Szolgáltatók hely alapján](expressroute-locations-providers.md)


A cikkben szereplő táblázatok a földrajzi lefedettséggel és a ExpressRoute, a ExpressRoute és a ExpressRoute rendszerintegrátorokkal (SIs) kapcsolatos információkat tartalmaznak.

> [!Note]
> Az Azure-régiók és a ExpressRoute két különböző fogalmat alkotnak, és a kettő közötti különbség kritikus fontosságú az Azure Hybrid Networking-kapcsolat megismeréséhez. 
>
>

## <a name="azure-regions"></a>Azure-régiók
Az Azure-régiók olyan globális adatközpontok, ahol az Azure számítási, hálózati és tárolási erőforrásai találhatók. Az Azure-erőforrások létrehozásakor az ügyfélnek ki kell választania egy erőforrás-helyet. Az erőforrás helye határozza meg, hogy az erőforrás melyik Azure-adatközpontban (vagy rendelkezésre állási zónában) jön létre.

## <a name="expressroute-locations"></a>ExpressRoute-helyek
A ExpressRoute helyek (más néven a társítási helyek vagy a saját helyek) olyan közös elhelyezési létesítmények, ahol a Microsoft Enterprise Edge (MSEE) eszközei találhatók. A ExpressRoute helye a Microsoft hálózatának belépési pontja – globálisan elosztott, így az ügyfelek a világ különböző pontjain csatlakozhatnak a Microsoft hálózatához. Ezek a helyszínek, ahol a ExpressRoute-partnerek és a ExpressRoute Direct-ügyfelek a Microsoft hálózatához csatlakoznak. Általánosságban elmondható, hogy a ExpressRoute helye nem kell, hogy egyezzen az Azure-régióval. Például egy ügyfél létrehozhat egy ExpressRoute-áramkört az *USA keleti*régiójában, a *Seattle* -beli összevonási helyen.

Ha legalább egy ExpressRoute-helyhez csatlakozott egy geopolitikai régióban, az adott geopolitikai régióban lévő összes régióban hozzáférhet az Azure-szolgáltatásokhoz. 

## <a name="locations"></a>Azure-régiók egy geopolitikai régión belüli ExpressRoute
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Zóna** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- | --- |
| **Ausztrália kormánya** | 1 | Ausztrália középső régiója, Ausztrália 2. középső régiója |Canberra, Canberra2 |
| **Európa** | 1 |Közép-Franciaország, Dél-Franciaország, Észak-Európa, Nyugat-Európa, az Egyesült Királyság nyugati régiója, az Egyesült Királyság déli régiója |Amszterdam, Amsterdam2, Koppenhága, Dublin, Frankfurt, London, London2, Marseille, Newport (Wales), Párizs, Stockholm, Zürich |
| **Észak-Amerika** | 1 |USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Szilícium-völgy, szilícium-Valley2, Washington, D.C., Washington DC2, Montreal, Québec város, Toronto |
| **Ázsia** | 2 |Kelet-Ázsia, Délkelet-Ázsia |Hongkong KKT, Kuala Lumpur, Szingapúr, Szingapúr2, Taipei |
| **India** | 2 |Nyugat-India, Közép-India, Dél-India |Csennai, Csennai2, Mumbai, Mumbai2 |
| **Japán** | 2 |Nyugat-Japán, Kelet-Japán |Oszaka, Tokió |
| **Oceania** | 2 |Délkelet-Ausztrália, Kelet-Ausztrália |Auckland, Melbourne, Perth, Sydney | 
| **Dél-Korea** | 2 |Korea középső régiója, Korea déli régiója |Busan, Szöul|
| **EGYESÜLT ARAB EMÍRSÉGEK** | 3 | UAE Central, Észak-Egyesült Arab | Dubai, Dubai2 |
| **Dél-Afrika** | 3 |Dél-Afrika nyugati régiója, Dél-Afrika északi régiója |Fokváros, Johannesburg |
| **Dél-Amerika** | 3 |Dél-Brazília |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Az Azure-régiók és az országos felhők geopolitikai határai
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD – középső régió, US DoD – keleti régió  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Szilícium-völgy, Washington, D.C. |
| **Kelet-Kína** |Kelet-Kína, Kelet-Kína2 |Shanghai, Shanghai2 |
| **Észak-Kína** |Észak-Kína, Észak-Kína2 |Peking, Beijing2 |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="partners"></a>ExpressRoute-kapcsolatszolgáltatók

Az alábbi táblázatban a kapcsolati helyek és az egyes helyekhez tartozó szolgáltatók láthatók. A szolgáltatók és a szolgáltatási helyek megtekintéséhez lásd: [Helyek szolgáltatók szerint](expressroute-locations.md). 

A **helyi Azure-régiók** olyanok, amelyek az egyes [ExpressRoute helyi](expressroute-faqs.md) helyeken is hozzáférhetnek. az **n/a** azt jelzi, hogy a ExpressRoute helyi helye nem érhető el az adott egyenrangú helyen.


### <a name="production-azure"></a>Termelési Azure
| **Location** | **Társviszony-létesítési helyszín tulajdonosa** | **Helyi Azure-régiók** | **Szolgáltatók** |
| --- | --- | --- | --- |
| **Amszterdam** | Equinix | Nyugat-Európa | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amszterdam2** | Interxion | Nyugat-Európa | CenturyLink Cloud Connect, DE-CIX, Interxion, Vodafone |
| **Atlanta** | Equinix | n/a | Equinix, Megaport |
| **Auckland** | Vocus-csoport – NZ | n/a | Devoli, Kordia, Megaport, Spark NZ, Vocus csoport, NZ |
| **Busan** |LG CNS | Korea déli régiója | LG CNS |
| **Canberra** | CDC | Ausztrália középső régiója | CDC |
| **Canberra2** | CDC | Ausztrália 2. középső régiója| CDC |
| **Fokváros** | Teraco | Dél-Afrika nyugati régiója | Internet Solutions – Cloud Connect, Liquid Telecom, Teraco |
| **Csennai** | Tata Communications | Dél-India | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Csennai2** | Airtel | Dél-India | Airtel |
| **Chicago** | Equinix | USA északi középső régiója | Aryaka Networks hálózatok, & T NetBond, CenturyLink Cloud-kapcsolat, Cologix, Comcast, Coresite, Equinix, felhő, Internet2, level 3 Communications, Megaport, PacketFabric, PCCW globális korlátozott, sprint, Telia Carrier, Verizon, Zayo |
| **Koppenhága** | Interxion | n/a | Interxion |
| **Dallas** | Equinix | n/a | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | CoreSite | USA nyugati középső régiója | CoreSite, Megaport, Zayo |
| **Dubai** | Etisalat UAE | Egyesült Arab Emírségek északi régiója | Etisalat UAE |
| **Dubai2** | du datamena | Egyesült Arab Emírségek északi régiója | du datamena, Orixcom |
| **Dublin** | Equinix | Észak-Európa | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | Interxion | n/a | DE-CIX, Interxion, narancs |
| **Hongkong (KKT)** | Equinix | Kelet-Ázsia | Aryaka Networks Networks, British Telecom, CenturyLink Cloud-kapcsolat, telekommunikáció, kínai távközlési globális, Equinix, Megaport, NTT Communications, Orange, PCCW globális korlátozott, Tata Communications, Telia Carrier, Verizon |
| **Johannesburg** | Teraco | Dél-Afrika északi régiója | British Telecom, Internet Solutions – Cloud-kapcsolat, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | TIME dotCom | n/a | TIME dotCom |
| **Las Vegas** | Kapcsoló | n/a | CenturyLink Cloud Connect, Megaport |
| **London** | Equinix | Az Egyesült Királyság déli régiója | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | Teleház | Az Egyesült Királyság déli régiója | IX Reach, Equinix |
| **Los Angeles** | CoreSite | n/a | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marseille** |Interxion | Dél-Franciaország | DE-CIX, Interxion, Jaguar Network |
| **Melbourne** | NextDC | Délkelet-Ausztrália | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | Equinix | n/a | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | n/a | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | Nyugat-India | Globális CloudXchange (GCX), bizalom jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | Nyugat-India | Airtel, Sify, Vodafone Idea |
| **New York** | Equinix | n/a | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport (Wales)** | Következő generációs adatok | Az Egyesült Királyság nyugati régiója | British Telecom, Colt, 3. szintű kommunikáció, következő generációs adatforgalom |
| **Oszaka** | Equinix | Nyugat-Japán | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Párizs** | Interxion | Közép-Franciaország | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | NextDC | n/a | Megaport, NextDC |
| **Quebec város** | 4Degrees | Kelet-Kanada | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | USA déli középső régiója | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | Equinix | Dél-Brazília | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | USA nyugati régiója, 2. | Aryaka Networks Networks, Equinix, level 3 Communications, Megaport, Telus, Zayo |
| **Szöul** | KINX | Korea középső régiója | KINX, LG CNS, Sejong Telecom |
| **Szilícium-völgy** | Equinix | USA nyugati régiója | Aryaka Networks hálózatok, & T NetBond, British Telecom, CenturyLink Cloud-kapcsolat, Comcast, Coresite, Equinix, felhő, Internet2, IX REACH, csomag, PacketFabric, level 3 Communications, Megaport, Orange, sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Szilícium Valley2** | Coresite | USA nyugati régiója | Coresite | 
| **Szingapúr** | Equinix | Délkelet-Ázsia | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | Global Switch | Délkelet-Ázsia | Colt, epszilon, globális kommunikáció, Megaport, énekel |
| **Stockholm** | Equinix | n/a | Telia-szolgáltató |
| **Sydney** | Equinix | Kelet-Ausztrália | AARNet, & T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus csoport NZ |
| **Taipei** | Telekommunikációs vezető | n/a | Telekommunikációs vezető, FarEasTone |
| **Tokió** | Equinix | Kelet-Japán | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | Cologix | Közép-Kanada | & T NetBond, Bell Canada, CenturyLink Cloud kapcsolatba, Cologix, Equinix, IX REACH Megaport, Telus, Verizon, Zayo |
| **Washington DC** | Equinix | USA keleti régiója, USA 2. keleti régiója | Aryaka Networks hálózatok, & T NetBond, British Telecom, CenturyLink Cloud-kapcsolat, Cologix, Comcast, Coresite, Equinix, Internet2, felhő, 3. szintű kommunikáció, Megaport, Neutrona hálózatok, NTT Communications, Orange, PacketFabric, sprint, Tata Kommunikáció, Telia-szállító, Verizon, Zayo |
| **Washington DC2** | Coresite | USA keleti régiója, USA 2. keleti régiója |Coresite | 
| **Zurich** | Interxion | n/a | Felhő, Interxion |

 **+** = hamarosan elérhető

### <a name="national-cloud-environments"></a>Országos felhőkörnyezetek

### <a name="us-government-cloud"></a>Az Egyesült Államok kormányának felhője
| **Location** | **Szolgáltatók** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Szilícium-völgy** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Washington DC** |AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Kína
| **Location** | **Szolgáltatók** |
| --- | --- |
| **Peking** |China Telecom |
| **Beijing2** | China Telecom, GDS |
| **Sanghaj** |China Telecom |
| **Shanghai2** | China Telecom, GDS |

További tudnivalókért lásd: [Az ExpressRoute Kínában](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Németország
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Kapcsolódás adatcsere-szolgáltatókon keresztül
Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Kérje meg a kapcsolatszolgáltatót, hogy terjessze ki a hálózatot a választott társviszony-létesítési helyszínre.
  * Győződjön meg róla, hogy a kapcsolatszolgáltató magas rendelkezésre állással terjesztette ki a kapcsolatot, tehát nincsenek kritikus hibapontok a rendszeren belül.
* Rendeljen meg egy ExpressRoute-kapcsolatcsoportot, ahol az adatcserélő a Microsofthoz kapcsolódó kapcsolatszolgáltató.
  * Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md) című témakör lépéseit a kapcsolat beállításához.

## <a name="c1partners"></a>Kapcsolódás további szolgáltatókon keresztül
| **Location** | **Exchange** | **Kapcsolatszolgáltatók** |
| --- | --- | --- |
| **Amszterdam** | Equinix, Interxion, 3. szint Communications, City | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, Gulf Bridge International, Kalaam Telecom Bahrain B. S. C, MainOne, Nianet, POST Telecom Luxembourg, proximus, TDC erhverv, Telecom Italia szikrázó, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Crown-kastély
| **Fokváros** | Teraco | MTN |
| **Chicago** | Equinix| Crown Castle, Spectrum Enterprise, windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, adatgyűjtő, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Interxion, Budapest | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hongkong (KKT)** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure, epszilon Telecom Limited, exponenciális E, HSO, NexGen Networks, proximus, Tamar, az adatszolgáltató |
| **Los Angeles** | Equinix |Crown Castle, Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco peer 1, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, WEBAIR |
| **Párizs** | Equinix | Proximus |
| **Quebec város** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha PRA nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Szilícium-völgy** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Szingapúr** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokió** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Cogeco peer 1, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, GTT Communications Inc, epszilon távközlés Limited, Masergy, windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute-rendszerintegrátorok
A privát kapcsolatok igény szerinti beállítása nehéz feladat lehet, a hálózat méretétől függően. A következő táblában felsorolt rendszerintegrátorok bármelyike segítségére lehet az ExpressRoute üzembe helyezésében.

| **Kontinens** | **Rendszerintegrátorok** |
| --- | --- |
| **Ázsia** |Avanade Inc., OneAs1a |
| **Ausztrália** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Európa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Észak-Amerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Dél-Amerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>További lépések
* További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Helyek térképe"
