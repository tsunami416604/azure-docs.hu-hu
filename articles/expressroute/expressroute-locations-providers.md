---
title: 'Helyek és kapcsolatszolgáltatók: Azure ExpressRoute | Microsoft Docs'
description: A cikk részletes áttekintést nyújt a szolgáltatási helyekről és az Azure-régiókhoz való csatlakozásról. Hely alapján rendezve.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: cherylmc
ms.openlocfilehash: 3b45c20e2d3e8829913c9e566516f85866ee80d4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390324"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partnerek és társviszony-létesítési helyszínek.

> [!div class="op_single_selector"]
> * [Helyek szolgáltató szerint](expressroute-locations.md)
> * [Szolgáltatók hely szerint](expressroute-locations-providers.md)


Az ebben a cikkben található táblák az ExpressRoute földrajzi lefedettségéről és helyéről, az ExpressRoute-kapcsolatszolgáltatókról és az ExpressRoute rendszerintegrátorokról (SI) nyújtanak tájékoztatást.

> [!Note]
> Az Azure-régiók és az ExpressRoute-helyek két különböző és különböző fogalom, a kettő közötti különbség megértése elengedhetetlen az Azure hibrid hálózati kapcsolatának felfedezéséhez. 
>
>

## <a name="azure-regions"></a>Azure-régiók
Az Azure-régiók olyan globális adatközpontok, ahol az Azure számítási, hálózati és tárolási erőforrásai találhatók. Egy Azure-erőforrás létrehozásakor az ügyfélnek ki kell választania egy erőforrás helyét. Az erőforrás helye határozza meg, hogy melyik Azure-adatközpont (vagy rendelkezésre állási zóna) az erőforrás jön létre.

## <a name="expressroute-locations"></a>ExpressRoute-helyek
Az ExpressRoute-helyek (más néven társviszony-létesítési helyek vagy meet-me-helyek) olyan helymegosztási helyek, ahol a Microsoft Enterprise edge (MSEE) eszközei találhatók. Az ExpressRoute-helyek a Microsoft hálózatának belépési pontjai – és globálisan terjesztve vannak, így az ügyfelek világszerte csatlakozhatnak a Microsoft hálózatához. Ezek a helyek azok a helyek, ahol az ExpressRoute-partnerek és az ExpressRoute Direct-ügyfelek keresztkapcsolatokat létesítenek a Microsoft hálózatával. Általában az ExpressRoute-hely nem kell egyeznie az Azure-régióban. Egy ügyfél létrehozhat például egy ExpressRoute-áramkört az *USA keleti része n.* erőforrás-helyével a *Seattle-i* társviszony-létesítési helyen.

Ha legalább egy ExpressRoute-helyhez csatlakozott egy geopolitikai régióban, az adott geopolitikai régióban lévő összes régióban hozzáférhet az Azure-szolgáltatásokhoz. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Az Azure-régiók expressroute-i helyekre egy geopolitikai régión belül
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Ausztrália kormányzati** | Ausztrália középső régiója, Ausztrália 2. középső régiója |Canberra, Canberra2 |
| **Európa** | Franciaország közép, Franciaország Dél, Németország Észak, Németország Nyugat-Közép, Észak-Európa, Norvégia Kelet, Norvégia Nyugat, Svájc Észak, Svájc Nyugat, Egyesült Királyság Nyugati, Egyesült Királyság Dél, Nyugat-Európa |Amszterdam, Amszterdam2, Koppenhága, Dublin, Frankfurt, Genf, London, London2, Marseille, Milánó, München, Newport (Wales), Oslo, Párizs, Stavanger, Stockholm, Zürich, München |
| **Észak-Amerika** | USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Szilícium-völgy, Szilícium-völgy2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto, Vancouver |
| **Ázsia** | Kelet-Ázsia, Délkelet-Ázsia | Bangkok, Hong Kong, Hong Kong2, Jakarta, Kuala Lumpur, Szingapúr, Szingapúr2, Tajpej |
| **India** | Nyugat-India, Közép-India, Dél-India |Csennai, Csennai2, Mumbai, Mumbai2 |
| **Japán** | Nyugat-Japán, Kelet-Japán |Oszaka, Tokió, Tokió2 |
| **Óceánia** | Délkelet-Ausztrália, Kelet-Ausztrália |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Dél-Korea** | Dél-Korea középső régiója, Korea déli régiója |Busan, Szöul|
| **EAE** | Egyesült Arab Emírségek középső részén, észak-amerikai egyesült arab emírségekbeli | Dubaj, Dubaj2 |
| **Dél-afrikai Köztársaság** | Dél-Afrika Nyugat, Dél-Afrika Észak-Afrika |Fokváros, Johannesburg |
| **Dél-Amerika** | Dél-Brazília |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Az Azure régiói és geopolitikai határai a nemzeti felhőkszámára
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD – középső régió, US DoD – keleti régió  |Atlanta,Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Szilícium-völgy, Washington DC |
| **Kelet-Kína** |Kelet-Kína, Kelet-Kína2 |Sanghaj, Sanghaj2 |
| **Észak-Kína** |Észak-Kína, Észak-Kína2 |Peking, Peking2 |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute-kapcsolatszolgáltatók

Az alábbi táblázatban a kapcsolati helyek és az egyes helyekhez tartozó szolgáltatók láthatók. A szolgáltatók és a szolgáltatási helyek megtekintéséhez lásd: [Helyek szolgáltatók szerint](expressroute-locations.md).

* **A helyi Azure-régiók** azok, amelyek [ExpressRoute helyi](expressroute-faqs.md) minden társviszony-létesítési helyen férhetnek hozzá. **n/a** azt jelzi, hogy az ExpressRoute Local nem érhető el az adott társviszony-létesítési helyen.

* **A zóna** az [árképzésre vonatkozik.](https://azure.microsoft.com/pricing/details/expressroute/)


### <a name="global-commercial-azure"></a>Globális kereskedelmi Azure
| **Helyen** | **Cím** | **Zóna** | **Helyi Azure-régiók** | **ER közvetlen** | **Szolgáltatók** |
| --- | --- | --- | --- | --- | --- |
| **Amszterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Nyugat-Európa | 10g, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amszterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Nyugat-Európa | 10g, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/a | n/a | Equinix, Megaport |
| **Auckland** | [Vocus Csoport NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/a | 10G | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **Bangkok** | [Ais](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | n/a | 10G | Ais |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Dél-Korea déli régiója | n/a | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Ausztrália középső régiója | 10g, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Ausztrália 2. középső régiója| 10g, 100G | CDC |
| **Fokváros** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Dél-Afrika Nyugati | 10G | BCX, Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Csennai** | Tata Communications | 2 | Dél-India | 10G | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Csennai2** | Airtel | 2 | Dél-India | n/a | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | USA északi középső régiója | 10g, 100G | Aryaka Networks, AT&T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Koppenhága** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/a | 10G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/a | 10g, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | USA nyugati középső régiója | n/a | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Egyesült Arab Emírségek északi | n/a | Etisalat Egyesült Arab Emírségek |
| **Dubaj2** | [du datamena (du datamena)](http://datamena.com/solutions/data-centre) | 3 | Egyesült Arab Emírségek északi | n/a | du datamena, Megaport, Narancs, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Észak-Európa | 10g, 100G | Colt, eir, Equinix, euNetworks, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Németország Nyugat-Közép | 10g, 100G | AT&T NetBond, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, GEANT, Interxion, Megaport, Orange, Telia Carrier |
| **Genf** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Svájc Nyugati | 10g, 100G | Equinix, Megaport |
| **Hongkong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Kelet-Ázsia | n/a | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, Equinix, InterCloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Hongkong2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | n/a | 10G | |
| **Jakarta** | Telkom Indonézia | 4 | n/a | 10G | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Dél-Afrika Észak-Afrika | 10G | British Telecom, Internet Solutions - Cloud Connect, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [TIME dotCom Menara CÉLOK](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | n/a | n/a | TIME dotCom |
| **Las Vegas** | [LV kapcsoló](https://www.switch.com/las-vegas) | 1 | n/a | n/a | CenturyLink Cloud Connect, Megaport |
| **London** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Az Egyesült Királyság déli régiója | 10g, 100G | AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse Északi Kettő](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Az Egyesült Királyság déli régiója | 10g, 100G | CenturyLink Cloud Connect, Colt, IX Reach, Equinix, Megaport, Telehouse - KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/a | n/a | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Transtelco, Zayo |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Dél-Franciaország | n/a | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Délkelet-Ausztrália | 10g, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/a | 10G | C3ntro, Equinix, Megaport, Neutrona Hálózatok |
| **Milánó** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n/a | 10G | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/a | 10g, 100G | Bell Kanada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Nyugat-India | n/a | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | Nyugat-India | n/a | Airtel, Sify, Vodafone Idea |
| **München** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | n/a | 10g, 100G | |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n/a | n/a | CenturyLink Cloud Connect, Colt, Coresite, Equinix, InterCloud, Megaport, Csomag, Zayo |
| **Newport (Wales)** | [Következő generációs adatok](https://www.nextgenerationdata.co.uk) | 1 | Az Egyesült Királyság nyugati régiója | n/a | British Telecom, Colt, 3- as szintű kommunikáció, következő generációs adatok |
| **Oszaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Nyugat-Japán | 10g, 100G | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norvégia Kelet | 10g, 100G | Megaport, Telenor, Telia Fuvarozó |
| **Párizs** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Közép-Franciaország | 10g, 100G | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/a | 10G | Megaport, NextDC |
| **Quebec város** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Kelet-Kanada | n/a | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | USA déli középső régiója | 10g, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Dél-Brazília | n/a | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | USA nyugati régiója, 2. | 10g, 100G | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Szöul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Dél-Korea középső régiója | 10g, 100G | KINX, KT, LG CNS, Sejong Telecom |
| **Szilícium-völgy** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | USA nyugati régiója | 10g, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Szilícium-völgy2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | USA nyugati régiója | 10g, 100G | Colt, Coresite | 
| **Szingapúr** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Délkelet-Ázsia | 10g, 100G | Aryaka Networks, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Globális Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Délkelet-Ázsia | 10g, 100G | Kína Unicom Global, Colt, Epsilon Global Communications, Megaport, PCCW Global Limited, SingTel |
| **Stavanger** | [Zöld-hegyi DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norvégia Nyugati | 10g, 100G | |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/a | 10G | Equinix, Telia hordozó |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Kelet-Ausztrália | 10g, 100G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2 között** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Kelet-Ausztrália | 10g, 100G | Megaport, NextDC |
| **Taipei** | Chief Telecom | 2 | n/a | 10G | Chief Telecom, FarEasTone |
| **Tokió** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Kelet-Japán | 10g, 100G | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Tokió2** | [TOKIÓBAN](https://www.attokyo.com/) | 2 | Kelet-Japán | 10g, 100G | TOKIÓBAN |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Közép-Kanada | 10g, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | Cologix | 10g, 100G | |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | USA keleti része, USA keleti része 2 | 10g, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, InterCloud, IX Reach, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | USA keleti része, USA keleti része 2 | 10g, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Viasat, Zayo | 
| **Zürich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Észak-Svájc | 10g, 100G | Intercloud, Interxion, Megaport, Swisscom |

 **+** azt jelzi, hamarosan

### <a name="national-cloud-environments"></a>Országos felhőkörnyezetek

Az Azure nemzeti felhői el vannak szigetelve egymástól és a globális kereskedelmi Azure-tól. ExpressRoute egy Azure-felhő nem tud csatlakozni az Azure-régiók a többi.

### <a name="us-government-cloud"></a>Az Egyesült Államok kormányának felhője
| **Helyen** | **Cím** | **Helyi Azure-régiók**| **ER közvetlen** | **Szolgáltatók** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n/a | 10g, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/a | 10g, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/a | 10g, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/a | 10g, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | USA-beli államigazgatás – Arizona | n/a | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | USA-beli államigazgatás – Texas | n/a | CenturyLink Cloud Connect, Megaport |
| **Szilícium-völgy** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/a | 10g, 100G | AT&T, Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/a | n/a | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD East, US Gov Virginia | 10g, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Kína
| **Helyen** | **Szolgáltatók** |
| --- | --- |
| **Peking** |China Telecom |
| **Peking2** | China Telecom, China Unicom, GDS |
| **Sanghaj** |China Telecom |
| **Sanghaj2** | China Telecom, GDS |

További tudnivalókért lásd: [Az ExpressRoute Kínában](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Németország
| **Helyen** | **Szolgáltatók** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Kapcsolat exchange-szolgáltatókon keresztül
Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion között](https://www.interxion.com/)
  * [Következő tartományvezérlő](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Kérje meg a kapcsolatszolgáltatót, hogy terjessze ki a hálózatot a választott társviszony-létesítési helyszínre.
  * Győződjön meg róla, hogy a kapcsolatszolgáltató magas rendelkezésre állással terjesztette ki a kapcsolatot, tehát nincsenek kritikus hibapontok a rendszeren belül.
* Rendeljen meg egy ExpressRoute-kapcsolatcsoportot, ahol az adatcserélő a Microsofthoz kapcsolódó kapcsolatszolgáltató.
  * Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md) című témakör lépéseit a kapcsolat beállításához.

## <a name="connectivity-through-satellite-operators"></a>Csatlakozás műholdas szolgáltatókon keresztül
Ha ön távoli és dont' volna rost connectivity vagy ön akar -hoz kikutat más connectivity választások tudod ellenőriz a következő hold kezelők. 

* Intelsat
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat között](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Kapcsolat további szolgáltatókon keresztül
| **Helyen** | **Exchange** | **Kapcsolódási szolgáltatók** |
| --- | --- | --- |
| **Amszterdam** | Equinix, Interxion, 3. | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrein B.S.C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, RETN, TDC Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Koronavár
| **Fokváros** | Teraco | MTN |
| **Chicago** | Equinix| Crown Castle, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, Data Foundry, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hongkong (KKT)** | Equinix | Vezető, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponenciális E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Crown Castle, Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, Webair |
| **Párizs** | Equinix | Proximus |
| **Quebec város** | Megaport | Fibrenoire |
| **Sao Pál** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Szilícium-völgy** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Szingapúr** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokió** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, Gtt Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

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
* Az ExpressRoute-ról további információt az [ExpressRoute gyakori kérdések című](expressroute-faqs.md)témakörben talál.
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Helytérkép"
