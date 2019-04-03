---
title: 'Kapcsolatszolgáltatók és helyek: Azure ExpressRoute | Microsoft Docs'
description: A cikk részletes áttekintést nyújt a szolgáltatási helyekről és az Azure-régiókhoz való csatlakozásról. Kapcsolatszolgáltatók szerint rendezve.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: pareshmu
ms.openlocfilehash: 06e93639e487439207aface0e56616b6492c6f5e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879245"
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

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek.
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Zóna** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- | --- |
| **Ausztrál kormányzati** | 1 | Ausztrália középső régiója, Ausztrália 2. középső régiója |Canberra, Canberra2 |
| **Európa** | 1 |Közép-Franciaország, Dél-Franciaország, Észak-Európa, Nyugat-Európa, az Egyesült Királyság nyugati régiója, az Egyesült Királyság déli régiója |Amsterdam, Amsterdam2, Dublin, London, London2, Marseille, Newport(Wales), Paris, Zurich |
| **Észak-Amerika** | 1 |USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, szilícium-völgy, Washington DC, Washington DC2, Montréal, Québec város, Toronto |
| **Ázsia** | 2 |Kelet-Ázsia, Délkelet-Ázsia |Hongkong KKT, Kuala Lumpur, Szingapúr, szingapúr2, Tajpej |
| **Ausztrália** | 2 |Délkelet-Ausztrália, Kelet-Ausztrália |Melbourne, Perth, Sydney | 
| **India** | 2 |Nyugat-India, Közép-India, Dél-India |Csennai, Csennai2, Mumbai, Mumbai2 |
| **Japán** | 2 |Nyugat-Japán, Kelet-Japán |Oszaka, Tokió |
| **Dél-Korea** | 2 |Korea középső régiója, Korea déli régiója |Busan, Szöul|
| **Dél-Afrika** | 3 |Dél-Afrika nyugati régiója, Dél-Afrika északi régiója |Fokváros, Johannesburg |
| **Dél-Amerika** | 3 |Dél-Brazília |Sao Paulo |


### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>A régiók és az országos felhők geopolitikai határai
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD – középső régió, US DoD – keleti régió  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Szilícium-völgy, Washington, D.C. |
| **Kelet-Kína** |Kelet-Kína, Kelet-Kína2 |Sanghaj, Shanghai2 |
| **Észak-Kína** |Észak-Kína, Észak-Kína2 |Beijing, Beijing2 |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="locations"></a>Kapcsolatszolgáltatói helyek

Az alábbi táblázat a szolgáltatók szerint jeleníti meg a helyeket. Ha az elérhető szolgáltatókat hely szerint kívánja megtekinteni, tekintse meg a következőt: [Szolgáltatók hely szerint](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Termelési Azure

| **Szolgáltató** | **Microsoft Azure** | **Az Office 365 és Dynamics 365** | **Helyek** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Támogatott |Támogatott |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/creatingsmiles/)** | Támogatott | Támogatott | Csennai2, Mumbai2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Hongkong, Sao Paulo, Seattle, Szilícium-völgy, Szingapúr, Tokió, Washington, D.C. |
| **[Ascenty Data Centers](https://ascenty.com/servicos/cloud-connect/microsoft-expressroute/)** |Támogatott |Támogatott |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, London, Szilícium-völgy, Szingapúr, Sydney, Tokió, Toronto, Washington, D.C. |
| **[Bell-Kanada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Támogatott |Támogatott |Montréal, Toronto, Québec város |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Támogatott |Támogatott |Amszterdam, Hongkong, London, Newport (Wales), Sao Paulo, szilícium-völgy, Szingapúr, Sydney, Tokió, Washington, D.C. |
| **C3ntro** |Hamarosan elérhető |Hamarosan elérhető |Miami |
| **CDC** | Támogatott | Támogatott | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Támogatott |Támogatott |Las Vegas, New York, San Antonio, Szilícium-völgy, Tokió, Toronto |
| **Vezető Telecom** |Támogatott |Támogatott |Taipei |
| **China Telecom Global** |Támogatott |Nem támogatott |Hongkong KKT |
| **[Cologix](https://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Támogatott |Támogatott |Chicago, Dallas, Montreal, Toronto, Washington, D.C. |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Támogatott |Támogatott |Amszterdam, Dublin, London, Párizs, szingapúr2, Tokió |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Támogatott |Támogatott |Chicago, Denver, Los Angeles, New York, szilícium-völgy, Washington DC, Washington dc2 számítógépre |
| **DE-CIX** | Támogatott |Támogatott |Amsterdam2|
| **EIR** |Támogatott |Támogatott |Dublin|
| **Epszilon globális kommunikáció** |Támogatott |Támogatott |Szingapúr, Szingapúr2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Támogatott |Amszterdam, Atlanta, Chicago, Dallas, Dublin, Hongkong, London, London2, Los Angeles, Melbourne, Miami, New York, Oszaka, Párizs, Sao Paulo, Seattle, szilícium-völgy, Szingapúr, Sydney, Tokió, Toronto, Washington, D.C. |
| **euNetworks** |Támogatott |Támogatott |Amszterdam, Dublin, London |
| **GÉANT** |Támogatott |Támogatott |Amszterdam |
| **[Globális Felhőszolgáltatóként Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Támogatott| Támogatott | Csennai, Mumbai |
| **[InterCloud](https://www.intercloud.com/)** |Támogatott |Támogatott |Amszterdam, Chicago, London, New York, Párizs, szilícium-völgy, Szingapúr, Washington, D.C. |
| **Internet2** |Támogatott |Támogatott |Chicago, Dallas, Washington, D.C. |
| **[Internet Initiative Japan Inc. – IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Támogatott |Támogatott |Oszaka, Tokió |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Támogatott |Támogatott |Fokváros, Johannesburg, London |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Támogatott |Támogatott |Amsterdam, Amsterdam2, Dublin, London, Marseille, Paris, Zurich |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Támogatott |Támogatott | Amszterdam, London2, szilícium-völgy, Toronto |
| **Jisc** |Támogatott |Támogatott |London |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Támogatott |Támogatott |Szöul |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Támogatott | Támogatott | Amszterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, London, Newport (Wales), Sao Paulo, Seattle, szilícium-völgy, Szingapúr, Washington, D.C. |
| **LG CNS** |Támogatott |Támogatott |Busan, Szöul |
| **[Folyékony Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Támogatott |Támogatott |Fokváros, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott |Támogatott |Amszterdam, Atlanta, Chicago, Dallas, Denver, Dublin, Hongkong, Las Vegas, London, Los Angeles, Melbourne, Miami, New York, Perth, Québec város, San Antonio, Seattle, szilícium-völgy, Szingapúr, szingapúr2, Sydney, Toronto, Washington, D.C. |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Támogatott |Támogatott |London |
| **[Neutrona hálózatok](http://www.neutrona.com/index.php/azure-expressroute/)** |Támogatott |Támogatott |Dallas, Los Angeles, Miami, Sao Paulo |
| **[Következő generációs adatok](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Támogatott |Támogatott |Newport (Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Támogatott |Támogatott |Melbourne, Perth, Sydney |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Támogatott |Támogatott |Amszterdam, Hongkong, London, Los Angeles, Oszaka, Szingapúr, Sydney, Tokió, Washington DC |
| **[NTT KELETI RÉGIÓJA](https://flets.com/cloudgateway/crossconnect/)** |Támogatott |Támogatott |Tokió |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Támogatott |Támogatott |Oszaka |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Támogatott |Támogatott |Melbourne, Sydney |
| **[Narancssárga](https://www.orange-business.com/en/products/business-vpn-galerie)** |Támogatott |Támogatott |Amszterdam, Hongkong, London, Párizs, Sao Paulo, szilícium-völgy, Szingapúr, Sydney, Tokió, Washington, D.C. |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Támogatott |Támogatott |Chicago, Hongkong, London |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Támogatott |Támogatott |Szöul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Támogatott |Támogatott |Csennai, Mumbai2 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Támogatott |Támogatott |Szingapúr, Szingapúr2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Támogatott |Támogatott |Oszaka, Tokió |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Támogatott |Támogatott |Amszterdam, Csennai, Hongkong, London, Mumbai, Sao Paulo, Szilícium-völgy, Szingapúr, Washington, D.C. |
| **Telecity Group** |Támogatott |Támogatott |Amszterdam |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Támogatott |Támogatott |Amszterdam, Sao Paulo |
| **[Telehouse – KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Támogatott |Támogatott |London |
| **Telenor** |Támogatott |Támogatott |Amszterdam, London |
| **[Telia szolgáltatója](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Támogatott | Támogatott |Amszterdam, Chicago, Dallas, London, Washington, D.C. |
| **TELMEX Uninet**| Támogatott | Támogatott | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Támogatott |Támogatott |Melbourne, Sydney, Szingapúr |
| **[Telus](https://www.telus.com)** |Támogatott |Támogatott |Montréal, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Támogatott |Támogatott |Fokváros, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Támogatott | Támogatott | Kuala Lumpur |
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Támogatott |Támogatott |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Hongkong, London, Szilícium-völgy, Szingapúr, Sydney, Tokió, Washington, D.C. |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Támogatott |Nem támogatott |London, Szingapúr |
| **Vodafone Idea** | Támogatott | Támogatott | Mumbai, Mumbai2 |
| **[Zayo](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Denver, London, Los Angeles, Montreal, New York, Párizs, Seattle, szilícium-völgy, Toronto, Washington, D.C. |

 **+** = hamarosan elérhető

### <a name="national-cloud-environment"></a>Országos felhőkörnyezet

### <a name="us-government-cloud"></a>Az Egyesült Államok kormányának felhője

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Támogatott |Támogatott |Chicago, Phoenix, Washington, D.C. |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Támogatott |Támogatott |New York, Phoenix |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Támogatott |Chicago, Dallas, New York, Seattle, Szilícium-völgy, Washington, D.C. |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott | Támogatott | Chicago, Dallas, San Antonio, Seattle |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Támogatott |Támogatott |Chicago, Dallas, New York, Szilícium-völgy, Washington, D.C. |

### <a name="china"></a>Kína

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **China Telecom** |Támogatott |Nem támogatott |Peking, Sanghaj |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Támogatott |Nem támogatott |Beijing2, Shanghai2 |

További tudnivalókért lásd: [Az ExpressRoute Kínában](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Németország

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Támogatott |Nem támogatott |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Nem támogatott |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Támogatott |Nem támogatott |Berlin |
| **Interxion** |Támogatott |Nem támogatott |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott  | Nem támogatott | Berlin |
| **T-Systems** |Támogatott |Nem támogatott |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Kapcsolódás adatcsere-szolgáltatókon keresztül

Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Kérje meg a kapcsolatszolgáltatót, hogy terjessze ki a hálózatot a választott társviszony-létesítési helyszínre.
  * Győződjön meg róla, hogy a kapcsolatszolgáltató magas rendelkezésre állással terjesztette ki a kapcsolatot, tehát nincsenek kritikus hibapontok a rendszeren belül.
* Rendeljen meg egy ExpressRoute-kapcsolatcsoportot, ahol az adatcserélő a Microsofthoz kapcsolódó kapcsolatszolgáltató.
  * Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md) című témakör lépéseit a kapcsolat beállításához.

## <a name="connectivity-through-additional-service-providers"></a>Kapcsolódás további szolgáltatókon keresztül

| **Kapcsolatszolgáltató** | **Exchange** | **Helyek** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Szingapúr |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice üzleti](https://golightpath.com/transport)** |Equinix |New York, Washington, D.C. |
| **[Arteria hálózatok Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokió |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, London, Singapore, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokió |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Vezető](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Hongkong KKT |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amszterdam | 
| **[Cogeco társ 1](https://www.cogecopeer1.com/en/)**| Equinix | Montréal, Toronto |
| **[CoreAzure](http://coreazure.com/expressroute)**| Equinix | London |
| **[Cox üzleti](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Dallas, Szilícium-völgy, Washington, D.C. | 
| **[Adatok Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epszilon távközlési korlátozott](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | London, Szingapúr, Washington, D.C. |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amszterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amszterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec város |
| **[BTt kommunikációs Inc vállalattól](https://www.gtt.net)** |Equinix | Washington, D.C. |
| **[Nemzetközi öböl híd](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amszterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **LGA Telecom** |Equinix |Szingapúr|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Chicago, New York, Washington, D.C. |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hongkong KKT |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amszterdam |
| **[A Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington, D.C. |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Fokváros, Johannesburg |
| **[NexGen hálózatok](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfurt |
| **[Közzététel](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amszterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amszterdam, Dublin, London, Párizs |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montréal, Toronto |
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | London | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amszterdam | 
| **[Telecom Italia csillogás](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amszterdam |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amszterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |
| **[USA információk Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Szingapúr |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Szilícium-völgy, Washington, D.C. |
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| 3-as szint | Madrid |
| **[Zirro](https://zirro.com/services/)**| Equinix | Montréal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Kapcsolódás adatközpont-szolgáltatókon keresztül

| **Szolgáltató** | **Exchange** |
| --- | --- |
| **[Egy Cyrus](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport |
| **[Digitális kivitelezés, ingatlan](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[RagingWire adatközpontok](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach |
| **[T5 Data centers](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Kapcsolódás nemzeti kutatási és oktatási hálózatokon (NREN) keresztül

| **Szolgáltató**|
| --- |
| **AARNET**| 
| **DeIC, a Géant-on keresztül**|
| **GARR, a Géant-on keresztül**|
| **GÉANT**|
| **HEAnet, a Géant-on keresztül**|
| **Internet2**|
| **JISC**|
| **RedIRIS, a Géant-on keresztül**|
| **SINET**|
| **Surfnet, a Géant-on keresztül**|

* Ha a kapcsolatszolgáltatója nem szerepel itt, ellenőrizze, hogy kapcsolódik-e a fent felsorolt ExpressRoute adatcsere-partnerek bármelyikéhez.

## <a name="expressroute-system-integrators"></a>ExpressRoute-rendszerintegrátorok
A privát kapcsolatok igény szerinti beállítása nehéz feladat lehet, a hálózat méretétől függően. A következő táblában felsorolt rendszerintegrátorok bármelyike segítségére lehet az ExpressRoute üzembe helyezésében.

| **Rendszerintegrátor** | **Kontinens** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Európa |
| **[Avanade Inc.](https://www.avanade.com/)** | Ázsia, Európa, Észak-Amerika, Dél-Amerika |
| **[Világos Skies GmbH](http://bskies.io/expressroute)** | Európa
| **[Ensyst](https://www.ensyst.com.au)** | Ázsia
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Észak-Amerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Észak-Amerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Észak-Amerika |
| **[Az informatikai tanácsadó csoport](https://itconsult.com.au/)** | Ausztrália |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Ausztrália |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Európa (Németország) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Európa |
| **[Új aláírás](https://newsignature.com/technologies/express-route/)** | Európa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Ázsia |
| **[Narancssárga hálózatok](https://orange-networks.com/blog/88-azureexpressroute)** | Európa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Észak-Amerika |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Észak-Amerika |
| **[sol-tec](https://www.sol-tec.com/services)** | Európa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Dél-Amerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Ausztrália |

## <a name="next-steps"></a>További lépések
* További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Helyek térképe"
