---
title: Az Azure Edge zónák – előzetes verzió
description: Ismerje meg a Microsoft peremhálózati számítástechnikai ajánlatait.
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 03/31/2020
ms.author: ganesr
ms.openlocfilehash: 4666022fc62fcb423906b46f0ff2274a2191f341
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437628"
---
# <a name="about-azure-edge-zones---preview"></a>Az Azure Edge zónák – előzetes verzió

Az Azure Edge Zones a Microsoft Azure ajánlatainak családja, amely lehetővé teszi a felhasználóhoz közeli adatfeldolgozást. Virtuális gépek, tárolók és más kiválasztott Azure-szolgáltatások edge zónákban az alkalmazások alacsony késésű és nagy átviteli követelmények.

Az Edge zónák tipikus használati esetesetei a következők:

- Valós idejű irányítás és vezérlés a robotikában
- Valós idejű elemzés és következtetés a mesterséges intelligenciával és a gépi tanulással
- Gépi látás
- Távoli renderelés vegyes valóságú és VDI-forgatókönyvekhez
- Magával ragadó, többjátékos játék
- Médiaadatfolyam-továbbítás és tartalomkézbesítés
- Felügyelet és biztonság

Az Azure Edge zónák három különálló ajánlatban érkeznek:

- Azure peremhálózati zónák
- Azure edge zónák carrierrel
- Azure privát peremhálózati zónák

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure peremhálózati zónák

![Szegélyzónák](./media/edge-zones-overview/edge-zones.png "Szegélyzónák")

Az Azure Edge zónák az Azure kis helyigényű bővítményei, amelyek az Azure-régióktól távol lévő népességi központokba kerülnek. Az Azure Edge-zónák támogatják a virtuális gépeket, a tárolókat és az Azure-szolgáltatások bizonyos készletét, amelyek lehetővé teszik a késésérzékeny és átviteli igényű alkalmazások futtatását a végfelhasználók közelében. Az Azure Edge-zónák a Microsoft globális hálózatának részét képezik, és biztonságos, megbízható és nagy sávszélességű kapcsolatot biztosítanak a felhasználóhoz közeli Edge Zone-ban futó alkalmazások és az Azure-régiókon belül futó Azure-szolgáltatások teljes készlete között. Az Azure Edge-zónák tulajdonosa és üzemeltetője a Microsoft, és lehetővé teszi, hogy ugyanazokat az Azure-eszközöket és portálokat használja a szolgáltatások kezeléséhez és üzembe helyezéséhez az Edge zónákban.

A tipikus használati esetek a következők:

- Játék- és játékstreamelés
- Médiaadatfolyam-továbbítás és tartalomkézbesítés
- Valós idejű elemzés és következtetés a mesterséges intelligencia és a gépi tanulás használatával
- Renderelés a vegyes valósághoz

Az Azure Edge zónák a következő metrókban lesznek elérhetők:

- New York, New York, Egyesült Államok
- Los Angeles, Amerikai
- Miami, FL

További információkért [forduljon az Edge Zones csapatához.](https://aka.ms/EdgeZones)

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Azure edge zónák carrierrel

![Perzseszéli zónák hordozóval](./media/edge-zones-overview/edge-carrier.png "Perzseszéli zónák hordozóval")

Az Azure Edge-zónák szolgáltatóval az Azure kis helyigényű bővítményei a mobilszolgáltatók adatközpontjaiban a népességi központokban elhelyezett adatközpontokban. A mobilszolgáltató 5G-hálózatától egy ugrásra eső Azure Edge-zónák 10 ezredmásodperces késleltetést kínálnak a mobileszközökről származó alkalmazásoknak. Az Azure Edge-zónák szolgáltatóval mobilszolgáltatók adatközpontjaiban vannak telepítve, és a Microsoft globális hálózatához csatlakoznak. Biztonságos, megbízható és nagy sávszélességű kapcsolatot biztosítanak a felhasználó hoz közel futó alkalmazások és az Azure-régiókon belül futó Azure-szolgáltatások teljes készlete között. A fejlesztők ugyanazokat a megszokott eszközöket használhatják a szolgáltatások létrehozásához és üzembe helyezéséhez az Edge zónákban.

A tipikus használati esetek a következők:

- Játék- és játékstreamelés
- Médiaadatfolyam-továbbítás és tartalomkézbesítés
- Valós idejű elemzés és következtetés a mesterséges intelligencia és a gépi tanulás használatával
- Renderelés a vegyes valósághoz
- Csatlakoztatott autók
- Távorvoslás

Az Edge Zónákat a következő operátorokkal együttműködve kínáljuk:

- AT&T (Atlanta, Dallas és Los Angeles)
- Etisalat
- Rogers
- Vodafone

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure privát peremhálózati zónák

![Privát peremzónák](./media/edge-zones-overview/private-edge.png "Privát peremzónák")

Az Azure Private Edge zones a helyszíni Azure-ban elhelyezett kis helyigényű bővítmények. Az Azure Private Edge Zones az [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) platformon alapul, és alacsony késésű hozzáférést biztosít a helyszínen üzembe helyezett számítástechnikai és tárolási szolgáltatásokhoz. A privát peremzónák lehetővé teszik a virtualizált hálózati funkciók (VNF-ek) telepítését is, például a mobilcsomag-magokat, az útválasztókat, a tűzfalakat és az SD-WAN-készülékeket, valamint az ISVS-ből származó alkalmazásokat, valamint az [Azure által felügyelt alkalmazásokat](https://azure.microsoft.com/services/managed-applications/) egymás mellett, virtuális gépekkel és a helyszíni tárolókkal együtt. Az Azure Private Edge Zones egy natív natív vezénylési megoldással rendelkezik, amely lehetővé teszi a virtualizált hálózati függvények (VNF) és az Azure Portalon elérhető alkalmazások életciklusának kezelését.

Az Azure Private Edge Zones lehetővé teszi, hogy a helyszíni alkalmazások at fejlesztheti és telepítheti ugyanazokat a jól ismert eszközöket, amelyek et az Azure-ban az alkalmazások létrehozásához és üzembe helyezéséhez használnak. Privát mobilhálózatokat (privát LTE, privát 5G), biztonsági funkciókat, például tűzfalakat is futtathat, és a helyszíni hálózatokat több ágra és az Azure-ra is kiterjesztheti ugyanazon a Private Edge Zone-készülékeken, és kezelheti őket az Azure-ból.

A tipikus használati esetek a következők:

- Valós idejű irányítás és vezérlés a robotikában
- Valós idejű elemzés és következtetés a mesterséges intelligenciával és a gépi tanulással
- Gépi látás
- Távoli renderelés vegyes valóságú és VDI-forgatókönyvekhez
- Felügyelet és biztonság

VNF-szállítók, független szoftverszállítók és MSP-partnerek gazdag ökoszisztémájával rendelkezünk, amelyek lehetővé teszik a teljes körű megoldásokat a privát peremzónák használatával. További információkért [forduljon a Privát peremzónák csapatához.](https://aka.ms/EdgeZonesPartner)

## <a name="private-edge-zones---partners"></a><a name="private-edge-partners"></a>Privát peremzónák - Partnerek

![Privát peremhálózati partnerek](./media/edge-zones-overview/partners.png "Privát peremhálózati partnerek")

### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>Virtualizált hálózati függvények (VNFs)

#### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>Virtualizált evolved packet core (vEPC) mobilhálózatokhoz

- [Megerősített hálózatok](https://www.affirmednetworks.com/)
- [Druid szoftver](https://www.druidsoftware.com/)
- [Expeto között](https://www.expeto.io/)
- [Mavenir között](https://mavenir.com/)
- [Metaswitch között](https://www.metaswitch.com/)
- [Nokia digitális automatizálási felhő](https://www.dac.nokia.com/)

#### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>Mobil rádió partnerek

- [Commscope Ruckus](https://support.ruckuswireless.com/)

#### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN szállítók

- [NetFoundry között](https://netfoundry.io/)
- [NuageNetworks a Nokia](https://www.nuagenetworks.net/)
- [VMware SD-WAN által Velocloud](https://www.velocloud.com/)

#### <a name="router-vendors"></a><a name="router-vendors"></a>Útválasztó-szállítók

- [Arista között](https://www.arista.com/)

[Lépjen kapcsolatba a Privát peremhálózati zónák csapatával,](https://aka.ms/EdgeZonesPartner) hogy további információra bírja, hogyan válhat partnerré.

#### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>Tűzfal forgalmazói

- Palo Alto Networks

### <a name="managed-solutions-providers---mobile-operators-and-global-system-integrators"></a><a name="msp-mobile"></a>Menedzselt megoldások szolgáltatói – Mobilszolgáltatók és globális rendszerintegrátorok

| Globális sis és operátorok | Mobilszolgáltatók |
| --- | --- |
| Amdocs között                       | Etisalat             |
| Amerikai torony               | Rogers               |
| Expeto között                       | Énekel              |
| Összevont vezeték nélküli           | Vodafone             |
| Infosys között                      |      *                |
| Tech Mahindra                |      *                |

[Lépjen kapcsolatba a Privát peremhálózati zónák csapatával,](https://aka.ms/EdgeZonesPartner) hogy további információra bírja, hogyan válhat partnerré.

## <a name="private-edge-zones---solutions"></a><a name="solutions-private-edge"></a>Privát peremzónák - Megoldások

### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Privát mobilhálózat privát peremhálózati zónákon

![Privát mobilhálózat privát peremhálózati zónákon](./media/edge-zones-overview/mobile-networks.png "Privát mobilhálózat privát peremhálózati zónákon")

Most már telepíthet egy privát mobilhálózatot a magánpereki zónákban. A privát mobilhálózatok rendkívül alacsony késleltetést, nagy kapacitású és megbízható és biztonságos vezeték nélküli hálózatot tesznek lehetővé, amely az üzleti legkritikusabb alkalmazásokhoz szükséges. A privát mobilhálózatok olyan forgatókönyveket tesznek lehetővé, mint az automatizált irányított járművek (AGV) irányítása és vezérlése egy raktárban, valós idejű kommunikáció a robotok között egy intelligens gyárban és a kibővített ingatlan, valamint a virtuális valóság élalkalmazások.

A virtualizált fejlett csomagmag (vEPC) hálózati funkció egy magán mobilhálózat agyát alkotja. Most már üzembe helyezhet egy vEPC-t a privát peremhálózati zónákban. A privát peremhálózati zónákban elérhető vEPC-partnerek listáját a [vEPC-s független szoftverszállítók](#vEPC)között láthatja el.

A privát mobilhálózati megoldások privát peremhálózati zónákra való üzembe helyezése más összetevőket igényel, például mobil hozzáférési pontokat, SIM-kártyákat és más VNF-eket, például útválasztókat. A licencelt vagy nem licencelt spektrumhoz való hozzáférés kritikus fontosságú a magánmobil-hálózat létrehozásához. Ezenkívül segítségre lehet szüksége az RF tervezéssel, a fizikai elrendezéssel, a telepítéssel és a támogatással kapcsolatban. A partnerek listáját a [Mobilrádió-partnerek ( Mobilrádió-partnerek ) oldalon található.](#mobile-radio)

A Microsoft olyan partnerökoszisztémát biztosít, amely a folyamat minden aspektusát segíti – a hálózat tervezésétől, a szükséges eszközök megvásárlásától, a hardver beállításától az Azure-ból történő konfiguráció kezeléséig. A Microsofttal szorosan integrált, validált partnerekkel biztos lehet benne, hogy a megoldás megbízható és könnyen használható lesz. Az alapvető forgatókönyvekre összpontosíthat, miközben a Microsoftra és partnereire támaszkodik, hogy segítsen a többiben.

### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>SD-WAN a privát peremhálózati zónákon

![SD-WAN a privát peremhálózati zónákon](./media/edge-zones-overview/sd-wan.png "SD-WAN a privát peremhálózati zónákon")
 
Az SD-WAN mint technológia lehetővé teszi nagyvállalati szintű nagyvállalati szintű wide area networks (WAN) létrehozását nagyobb sávszélességgel, nagy teljesítményű hozzáféréssel a felhőhöz, a szolgáltatás beillesztéséhez, a megbízhatósághoz, a házirend-kezeléshez és a kiterjedt hálózati láthatósághoz. Az SD-WAN zökkenőmentes fiókirodai kapcsolatot biztosít a redundáns központi vezérlőktől, alacsonyabb tulajdonlási költség mellett.
Az SD-WAN a privát peremhálózati zónákon lehetővé teszi, hogy a capex-központú modellről a Szoftverszolgáltatásként (SaaS) modellre lépjen az informatikai költségvetés csökkentése érdekében. Használhatja a választott SD-WAN partnerek orchestrator vagy vezérlő, hogy az új szolgáltatások és propagálják azokat a teljes hálózaton azonnal.

## <a name="next-steps"></a>További lépések

További információkért forduljon a következő csapatokhoz:

* [Edge Zónák csapata](https://aka.ms/EdgeZones)
* [Private Edge Zones csapat - partnerré válni](https://aka.ms/EdgeZonesPartner)
