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
ms.date: 09/12/2019
ms.author: cherylmc
ms.openlocfilehash: 3867e4c59f541fb50c536850e89e03d49a831e42
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162091"
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
| **Európa** | 1 |Közép-Franciaország, Dél-Franciaország, Észak-Európa, Nyugat-Európa, az Egyesült Királyság nyugati régiója, az Egyesült Királyság déli régiója |Amszterdam, Amsterdam2, Koppenhága, Dublin, Frankfurt, London, London2, Marseille, Newport (Wales), Párizs, Stockholm, Zürich, München |
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
| **Location** | **Tulajdonos és a címe** | **Helyi Azure-régiók** | **Szolgáltatók** |
| --- | --- | --- | --- |
| **Amszterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | Nyugat-Európa | Aryaka Networks Networks, & T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, Cloud, Interxion, KPN, IX REACH, 3. szintű kommunikáció, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amszterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | Nyugat-Európa | CenturyLink Cloud-kapcsolat, DE-CIX, Colt, Interxion, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | n/a | Equinix, Megaport |
| **Auckland** | [Vocus csoport, NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | n/a | Devoli, Kordia, Megaport, Spark NZ, Vocus csoport, NZ |
| **Busan** | [LG CNS](https://datacenter.lgcns.com/Contents/En/Menu_1/Locations_1.aspx) | Korea déli régiója | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | Ausztrália középső régiója | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | Ausztrália 2. középső régiója| CDC |
| **Fokváros** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | Dél-Afrika nyugati régiója | Internet Solutions – Cloud Connect, Liquid Telecom, Teraco |
| **Csennai** | Tata Communications | Dél-India | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Csennai2** | Airtel | Dél-India | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | USA északi középső régiója | Aryaka Networks hálózatok, & T NetBond, CenturyLink Cloud-kapcsolat, Cologix, Comcast, Coresite, Equinix, felhő, Internet2, level 3 Communications, Megaport, PacketFabric, PCCW globális korlátozott, sprint, Telia Carrier, Verizon, Zayo |
| **Koppenhága** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | n/a | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/a | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | USA nyugati középső régiója | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | Egyesült Arab Emírségek északi régiója | Etisalat UAE |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | Egyesült Arab Emírségek északi régiója | du datamena, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | Észak-Európa | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | n/a | DE-CIX, Interxion, narancs |
| **Hongkong (KKT)** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | Kelet-Ázsia | Aryaka Networks Networks, British Telecom, CenturyLink Cloud-kapcsolat, telekommunikáció, kínai távközlési globális, Equinix, Megaport, NTT Communications, Orange, PCCW globális korlátozott, Tata Communications, Telia Carrier, Verizon |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | Dél-Afrika északi régiója | British Telecom, Internet Solutions – Cloud-kapcsolat, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [Az idő dotCom Menara célja](https://www.aims.com.my/co-location/points-of-presence.html) | n/a | TIME dotCom |
| **Las Vegas** | [LV kapcsoló](https://www.switch.com/las-vegas) | n/a | CenturyLink Cloud Connect, Megaport |
| **London** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | Az Egyesült Királyság déli régiója | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Ház, Észak-két](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | Az Egyesült Királyság déli régiója | IX Reach, Equinix |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | n/a | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | Dél-Franciaország | DE-CIX, Interxion, Jaguar Network |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | Délkelet-Ausztrália | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | n/a | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | n/a | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | Nyugat-India | Globális CloudXchange (GCX), bizalom jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | Nyugat-India | Airtel, Sify, Vodafone Idea |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | n/a | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport (Wales)** | [Következő generációs adatok](https://www.nextgenerationdata.co.uk) | Az Egyesült Királyság nyugati régiója | British Telecom, Colt, 3. szintű kommunikáció, következő generációs adatforgalom |
| **Oszaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | Nyugat-Japán | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Párizs** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | Közép-Franciaország | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | n/a | Megaport, NextDC |
| **Quebec város** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | Kelet-Kanada | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | USA déli középső régiója | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | Dél-Brazília | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | USA nyugati régiója, 2. | Aryaka Networks Networks, Equinix, level 3 Communications, Megaport, Telus, Zayo |
| **Szöul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | Korea középső régiója | KINX, LG CNS, Sejong Telecom |
| **Szilícium-völgy** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | USA nyugati régiója | Aryaka Networks hálózatok, & T NetBond, British Telecom, CenturyLink Cloud-kapcsolat, Comcast, Coresite, Equinix, felhő, Internet2, IX REACH, csomag, PacketFabric, level 3 Communications, Megaport, Orange, sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Szilícium Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | USA nyugati régiója | Coresite | 
| **Szingapúr** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | Délkelet-Ázsia | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Globális kapcsoló, Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | Délkelet-Ázsia | Colt, epszilon, globális kommunikáció, Megaport, énekel |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | n/a | Telia-szolgáltató |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | Kelet-Ausztrália | AARNet, & T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus csoport NZ |
| **Taipei** | Telekommunikációs vezető | n/a | Telekommunikációs vezető, FarEasTone |
| **Tokió** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | Kelet-Japán | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | Közép-Kanada | & T NetBond, Bell Canada, CenturyLink Cloud kapcsolatba, Cologix, Equinix, IX REACH Megaport, Telus, Verizon, Zayo |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | USA keleti régiója, USA 2. keleti régiója | Aryaka Networks hálózatok, & T NetBond, British Telecom, CenturyLink Cloud-kapcsolat, Cologix, Comcast, Coresite, Equinix, Internet2, felhő, 3. szintű kommunikáció, Megaport, Neutrona hálózatok, NTT Communications, Orange, PacketFabric, SES, sprint, Tata Kommunikáció, Telia-szállító, Verizon, Zayo |
| **Washington DC2** | [Coresite Restonban](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | USA keleti régiója, USA 2. keleti régiója |Coresite, Viasat, Zayo | 
| **Zurich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | n/a | Felhő, Interxion |

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
| **Location** | **Szolgáltatók** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Kapcsolódás az Exchange-szolgáltatókon keresztül
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

## <a name="connectivity-through-satellite-operators"></a>Csatlakozás műholdas operátorokon keresztül
Ha távoli, és nem rendelkezik száloptikás kapcsolattal, vagy más csatlakozási lehetőségeket szeretne felfedezni, tekintse meg a következő műhold-operátorokat. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Kapcsolódás további szolgáltatókon keresztül
| **Location** | **Exchange** | **Kapcsolódási szolgáltatók** |
| --- | --- | --- |
| **Amszterdam** | Equinix, Interxion, 3. szint kommunikáció | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, Gulf Bridge International, Kalaam Telecom Bahrain B. S. C, MainOne, Nianet, POST Telecom Luxembourg, proximus, TDC erhverv, Telecom Italia szikrázó, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Crown-kastély
| **Fokváros** | Teraco | MTN |
| **Chicago** | Equinix| Crown Castle, Spectrum Enterprise, windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, adatgyűjtő, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hongkong (KKT)** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, epszilon Telecom Limited, exponenciális E, HSO, NexGen Networks, proximus, Tamar, az adatszolgáltató |
| **Los Angeles** | Equinix |Crown Castle, Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, WEBAIR |
| **Párizs** | Equinix | Proximus |
| **Quebec város** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha PRA nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Szilícium-völgy** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Szingapúr** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, epszilon távközlés Limited, LGA Telecom, Egyesült információs autópálya (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokió** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
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
