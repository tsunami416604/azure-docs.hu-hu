---
title: 'Kapcsolatszolgáltatók és helyek: Azure ExpressRoute | Microsoft Docs'
description: A cikk részletes áttekintést nyújt a szolgáltatási helyekről és az Azure-régiókhoz való csatlakozásról. Kapcsolatszolgáltatók szerint rendezve.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 41e12a93f333936468cbdfbe7aa6bfa6e0b7e8ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278747"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partnerek és társviszony-létesítési helyszínek.

> [!div class="op_single_selector"]
> * [Helyek szolgáltató alapján](expressroute-locations.md)
> * [Szolgáltatók hely alapján](expressroute-locations-providers.md)


Az ebben a cikkben található táblák az ExpressRoute földrajzi lefedettségéről és helyéről, az ExpressRoute-kapcsolatszolgáltatókról és az ExpressRoute rendszerintegrátorokról (SI) nyújtanak tájékoztatást.

> [!Note]
> Az Azure-régiók és az ExpressRoute-helyek két különböző és különböző fogalom, a kettő közötti különbség megértése elengedhetetlen az Azure hibrid hálózati kapcsolatának felfedezéséhez. 
>
>

## <a name="azure-regions"></a>Azure-régiók
Az Azure-régiók olyan globális adatközpontok, ahol az Azure számítási, hálózati és tárolási erőforrásai találhatók. Egy Azure-erőforrás létrehozásakor az ügyfélnek ki kell választania egy erőforrás helyét. Az erőforrás helye határozza meg, hogy melyik Azure-adatközpont (vagy rendelkezésre állási zóna) az erőforrás jön létre.

## <a name="expressroute-locations"></a>ExpressRoute-helyek
Az ExpressRoute-helyek (más néven társviszony-létesítési helyek vagy meet-me-helyek) olyan helymegosztási helyek, ahol a Microsoft Enterprise Edge (MSEE) eszközök találhatók. Az ExpressRoute-helyek a Microsoft hálózatának belépési pontjai – és globálisan terjesztve vannak, így az ügyfelek világszerte csatlakozhatnak a Microsoft hálózatához. Ezek a helyek azok a helyek, ahol az ExpressRoute-partnerek és az ExpressRoute Direct-ügyfelek keresztkapcsolatokat létesítenek a Microsoft hálózatával. Általában az ExpressRoute-hely nem kell egyeznie az Azure-régióban. Egy ügyfél létrehozhat például egy ExpressRoute-áramkört az *USA keleti része n.* erőforrás-helyével a *Seattle-i* társviszony-létesítési helyen.

Ha legalább egy ExpressRoute-helyhez csatlakozott egy geopolitikai régióban, az adott geopolitikai régióban lévő összes régióban hozzáférhet az Azure-szolgáltatásokhoz.

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek.
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Ausztrália kormányzati** |Ausztrália középső régiója, Ausztrália 2. középső régiója |Canberra, Canberra2 |
| **Európa** | Franciaország közép, Franciaország Dél, Németország Észak, Németország Nyugat-Közép, Észak-Európa, Norvégia Kelet, Norvégia Nyugat, Svájc Észak, Svájc Nyugat, Egyesült Királyság Nyugati, Egyesült Királyság Dél, Nyugat-Európa |Amszterdam, Amszterdam2, Koppenhága, Dublin, Frankfurt, Genf, London, London2, Marseille, Milánó, München, Newport (Wales), Oslo, Párizs, Stavanger, Stockholm, Zürich |
| **Észak-Amerika** |USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Szilícium-völgy, Szilícium-völgy2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto, Vancouver |
| **Ázsia** | Kelet-Ázsia, Délkelet-Ázsia | Bangkok, Hong Kong, Hong Kong2, Jakarta, Kuala Lumpur, Szingapúr, Szingapúr2, Tajpej |
| **India** | Nyugat-India, Közép-India, Dél-India |Csennai, Csennai2, Mumbai, Mumbai2 |
| **Japán** | Nyugat-Japán, Kelet-Japán |Oszaka, Tokió, Tokió2 |
| **Óceánia** | Délkelet-Ausztrália, Kelet-Ausztrália |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Dél-Korea** | Dél-Korea középső régiója, Korea déli régiója |Busan, Szöul|
| **EAE** | Egyesült Arab Emírségek középső részén, észak-amerikai egyesült arab emírségekbeli | Dubaj, Dubaj2 |
| **Dél-afrikai Köztársaság** | Dél-Afrika Nyugat, Dél-Afrika Észak-Afrika |Fokváros, Johannesburg |
| **Dél-Amerika** | Dél-Brazília |Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>A régiók és az országos felhők geopolitikai határai
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD – középső régió, US DoD – keleti régió  |Atlanta,Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Szilícium-völgy, Washington DC |
| **Kelet-Kína** |Kelet-Kína, Kelet-Kína2 |Sanghaj, Sanghaj2 |
| **Észak-Kína** |Észak-Kína, Észak-Kína2 |Peking, Peking2 |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute-kapcsolatszolgáltatók

Az alábbi táblázat a szolgáltatók szerint jeleníti meg a helyeket. Ha az elérhető szolgáltatókat hely szerint kívánja megtekinteni, tekintse meg a következőt: [Szolgáltatók hely szerint](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Globális kereskedelmi Azure

| **Szolgáltató** | **Microsoft Azure** | **Office 365**  | **Helyek** |
| --- | --- | --- | --- |
| **[AARNet között](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Támogatott |Támogatott |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Támogatott | Támogatott | Csennai2, Mumbai2 |
| **[Ais](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Támogatott | Támogatott | Bangkok |
| **[Aryaka Networks](https://www.aryaka.com/)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Hong Kong SAR, Sao Paulo, Seattle, Szilícium-völgy, Szingapúr, Tokió, Washington DC |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Támogatott |Támogatott |Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, London, Szilícium-völgy, Szingapúr, Sydney, Tokió, Toronto, Washington, D.C. |
| **[TOKIÓBAN](https://www.attokyo.com/service/cloudsconnection/forazure.html)** | Támogatott | Támogatott | Tokió2 |
| **[BBIX KÖZÖTT](https://www.bbix.net/en/service/ix/)** | Támogatott | Támogatott | Tokió |
| **[BCX](https://www.bcx.co.za/solutions)** |Támogatott |Támogatott |Fokváros|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Támogatott |Támogatott |Montréal, Toronto, Québec város |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Támogatott |Támogatott |Amszterdam, Hong Kong SAR, Johannesburg, London, Newport (Wales), Sao Paulo, Szilícium-völgy, Szingapúr, Sydney, Tokió, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Támogatott |Támogatott |Miami |
| **CDC** | Támogatott | Támogatott | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Támogatott |Támogatott |Amszterdam2, Chicago, Frankfurt, Hong Kong, Las Vegas, London2, New York, Párizs, San Antonio, Szilícium-völgy, Tokió, Toronto, Washington DC, Washington DC2 |
| **[Chief Telecom](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |Támogatott |Támogatott |Hongkong, Tajpej |
| **Kína Mobile International** |Támogatott |Támogatott | Szingapúr |
| **China Telecom Global** |Támogatott |Támogatott |Hongkong |
| **Kína Unicom Global** |Támogatott |Támogatott | Singapore2 |
| **[Cologix között](https://www.cologix.com/hyperscale/microsoft-azure/)** |Támogatott |Támogatott |Chicago, Dallas, Montreal, Toronto, Washington, D.C. |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Támogatott |Támogatott |Amszterdam, Amszterdam2, Chicago, Dublin, Frankfurt, London, London2, Newport, New York, Oszaka, Párizs, Szilícium-völgy, Szilícium-völgy2, Szingapúr2, Tokió, Washington DC |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Támogatott |Támogatott |Chicago, Denver, Los Angeles, New York, Szilícium-völgy, Szilícium-völgy2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Támogatott |Támogatott |Amszterdam2, Frankfurt, Marseille|
| **[Devoli között](https://devoli.com/expressroute)** | Támogatott |Támogatott | Auckland, Melbourne, Sydney |
| **du datamena (du datamena)** |Támogatott |Támogatott | Dubaj2 |
| **eir** |Támogatott |Támogatott |Dublin|
| **[Epsilon Globális Kommunikáció](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Támogatott |Támogatott |Szingapúr, Szingapúr2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Támogatott |Amszterdam, Atlanta, Chicago, Dallas, Dublin, Frankfurt, Genf, Hong Kong SAR, London, London2, Los Angeles, Melbourne, Miami, New York, Oszaka, Párizs, Sao Paulo, Seattle, Szilícium-völgy, Szingapúr, Stockholm, Sydney, Tokió, Toronto, Washington DC |
| **Etisalat Egyesült Arab Emírségek** |Támogatott |Támogatott |Dubai|
| **[euHálózatok](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Támogatott |Támogatott |Amszterdam, Amszterdam2, Dublin, London |
| **FarEasTone között** |Támogatott |Támogatott |Taipei|
| **GÉANT** |Támogatott |Támogatott |Amszterdam, Frankfurt, Marseille |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Támogatott| Támogatott | Csennai, Mumbai |
| **Intelsat** | Támogatott | Támogatott | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Támogatott |Támogatott |Amszterdam, Chicago, Hongkong, London, New York, Párizs, Szilícium-völgy, Szingapúr, Washington DC, Zürich |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Támogatott |Támogatott |Chicago, Dallas, Szilícium-völgy, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Támogatott |Támogatott |Oszaka, Tokió |
| **[Internetes megoldások - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Támogatott |Támogatott |Fokváros, Johannesburg, London |
| **[Interxion között](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Támogatott |Támogatott |Amszterdam, Amszterdam2, Koppenhága, Dublin, Frankfurt, London, Marseille, Párizs, Zürich |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Támogatott |Támogatott | Amszterdam, London2, Szilícium-völgy, Toronto, Washington DC |
| **Jaguar Hálózat** |Támogatott |Támogatott |Marseille|
| **[Jisc között](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Támogatott |Támogatott |London |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Támogatott |Támogatott |Szöul |
| **[Kordia között](https://www.kordia.co.nz/cloudconnect)** | Támogatott |Támogatott |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Támogatott | Támogatott | Amszterdam |
| **[Kt](https://cloud.kt.com/portal/ktcloudportal.epc.productintro.partnershipcloud_ConnectHub.html)** | Támogatott | Támogatott | Szöul |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, London, Newport (Wales), Sao Paulo, Seattle, Szilícium-völgy, Szingapúr, Washington DC |
| **LG CNS** |Támogatott |Támogatott |Busan, Szöul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Támogatott |Támogatott |Fokváros, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott |Támogatott |Amszterdam, Atlanta, Auckland, Chicago, Dallas, Denver, Dubai2, Dublin, Frankfurt, Genf, Hong Kong SAR, Las Vegas, London, London2, Los Angeles, Melbourne, Miami, Montreal, New York, Oslo, Perth, Quebec City, San Antonio, Seattle, Szilícium-völgy, Szingapúr, Szingapúr2, Sydney, Sydney2, Tokió, Toronto, Washington DC, Zürich |
| **[Mtn](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Támogatott |Támogatott |London |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Támogatott |Támogatott |Dallas, Los Angeles, Miami, Sao Paulo, Washington DC |
| **[Következő generációs adatok](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Támogatott |Támogatott |Newport (Wales) |
| **[KÖVETKEZŐ TARTOMÁNYVEZÉRLŐ](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Támogatott |Támogatott |Melbourne, Perth, Sydney, Sydney2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Támogatott |Támogatott |Amszterdam, Hong Kong SAR, London, Los Angeles, Oszaka, Szingapúr, Sydney, Tokió, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Támogatott |Támogatott |Tokió |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Támogatott |Támogatott |Oszaka |
| **[Ooredoo felhő csatlakozás](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Támogatott |Támogatott |Marseille |
| **[Optus](https://www.optus.com.au/enterprise/)** |Támogatott |Támogatott |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Támogatott |Támogatott |Amszterdam, Amsterdam2, Dubai2, Frankfurt, Hong Kong SAR, Johannesburg, London, Párizs, Sao Paulo, Szilícium-völgy, Szingapúr, Sydney, Tokió, Washington DC |
| **[Orixcom között](https://www.orixcom.com/cloud-solutions/)** | Támogatott | Támogatott | Dubaj2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Támogatott |Támogatott |Chicago, Hongkong KKT, London |
| **[Sejong Telekommunikáció](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Támogatott |Támogatott |Szöul |
| **[Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Támogatott |Támogatott | Washington DC |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Támogatott |Támogatott |Csennai, Mumbai2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Támogatott |Támogatott |Szingapúr, Szingapúr2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Támogatott |Támogatott |Oszaka, Tokió |
| **[Szikra ÚJ-Zéland](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Támogatott |Támogatott |Auckland, Sydney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Támogatott | Támogatott | Zürich |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Támogatott |Támogatott |Amszterdam, Chennai, Hong Kong SAR, London, Mumbai, Sao Paulo, Szilícium-völgy, Szingapúr, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Támogatott |Támogatott |Amszterdam, Sao Paulo |
| **[Telehouse – KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Támogatott |Támogatott |London, London2 |
| **Telenor** |Támogatott |Támogatott |Amszterdam, London, Oslo |
| **[Telia Carrier](https://www.teliacarrier.com/)** | Támogatott | Támogatott |Amszterdam, Chicago, Dallas, Frankfurt, Hong Kong, London, Oslo, Párizs, Szilícium-völgy, Stockholm, Washington DC |
| **Telmex Uninet**| Támogatott | Támogatott | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Támogatott |Támogatott |Melbourne, Sydney, Szingapúr |
| **[Telus](https://www.telus.com)** |Támogatott |Támogatott |Montreal, Seattle, Torontó |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Támogatott |Támogatott |Fokváros, Johannesburg |
| **[IDŐ dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Támogatott | Támogatott | Kuala Lumpur |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Támogatott |Támogatott |Dallas, Los Angeles|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Támogatott |Támogatott |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Hong Kong SAR, London, Mumbai, Szilícium-völgy, Szingapúr, Sydney, Tokió, Toronto, Washington DC |
| **[Viasat között](http://www.directcloud.viasatbusiness.com/)** | Támogatott | Támogatott | Washington DC2 |
| **[Vocus Csoport NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Támogatott | Támogatott | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Támogatott |Támogatott |Amsterdam2, London, Szingapúr |
| **[Vodafone ötlet](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Támogatott | Támogatott | Mumbai, Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Denver, London, Los Angeles, Montreal, New York, Párizs, Seattle, Szilícium-völgy, Toronto, Washington DC, Washington DC2 |

 **+** azt jelzi, hamarosan

### <a name="national-cloud-environment"></a>Országos felhőkörnyezet

Az Azure nemzeti felhői el vannak szigetelve egymástól és a globális kereskedelmi Azure-tól. ExpressRoute egy Azure-felhő nem tud csatlakozni az Azure-régiók a többi. 

### <a name="us-government-cloud"></a>Az Egyesült Államok kormányának felhője

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Támogatott |Támogatott |Chicago, Phoenix, Szilícium-völgy, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Támogatott |Támogatott |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Támogatott |Atlanta, Chicago, Dallas, New York, Seattle, Szilícium-völgy, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott | Támogatott | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Támogatott |Támogatott |Chicago, Dallas, New York, Szilícium-völgy, Washington, D.C. |

### <a name="china"></a>Kína

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **China Telecom** |Támogatott |Nem támogatott |Peking, Peking2, Sanghaj, Sanghaj2 |
| **Kína Unicom** | Támogatott | Nem támogatott | Peking2 |
| **[Gds](http://www.gds-services.com/en/about_2.html)** |Támogatott |Nem támogatott |Peking2, Sanghaj2 |

További információ: [ExpressRoute In China](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Németország

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Támogatott |Nem támogatott |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Nem támogatott |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Támogatott |Nem támogatott |Berlin |
| **Interxion** |Támogatott |Nem támogatott |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott  | Nem támogatott | Berlin |
| **T-Systems** |Támogatott |Nem támogatott |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Kapcsolat exchange-szolgáltatókon keresztül

Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [Következő tartományvezérlő](https://www.nextdc.com/)
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

## <a name="connectivity-through-additional-service-providers"></a>Kapcsolat további szolgáltatókon keresztül

| **Kapcsolatszolgáltató** | **Exchange** | **Helyek** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Szingapúr |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington, D.C. |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokió |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-connect)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amszterdam, Frankfurt, London, Szingapúr, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokió |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Hongkong (KKT) |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amszterdam | 
| **[CMC Távközlés](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Szingapúr | 
| **[Aptum Technológiák](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montréal, Toronto |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Szilícium-völgy, Washington, D.C. |
| **[Koronavár](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | London, Szingapúr, Washington, D.C. |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amszterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amszterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec város |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge Nemzetközi](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amszterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrein B.S.C.](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Level 3 Communications |Amszterdam |
| **LGA Telecom** |Equinix |Szingapúr|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hongkong (KKT) 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amszterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[Mtn](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Fokváros, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[POST Telecom Luxemburg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amszterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amszterdam, Dublin, London, Párizs |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[RETN között](https://retn.net/services/cloud-connect/)** | Equinix | Amszterdam |
| **Rogers** | Cologix, Equinix | Montréal, Toronto |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Szilícium-völgy | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | London | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amszterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amszterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amszterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Szingapúr |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Szilícium-völgy, Washington, D.C. |
| **[X2nsat Kft.](https://www.x2nsat.com/expressroute/)** |Coresite |Szilícium-völgy, Szilícium-völgy 2|
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| 3-as szint | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montréal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Kapcsolat adatközpont-szolgáltatókon keresztül

| **Szolgáltató** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, Csomagszövet |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, Csomagszövet |
| **[Adatbank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry között](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Elérése, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, Csomagszövet |
| **[Rugalmas](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Elérése, Megaport, PacketFabric |
| **[QTS adatközpontok](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, Csomagszövet |
| **[Adattárház-adatfolyamok]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Elérése, Megaport, PacketFabric |
| **[vXchnge között](https://www.vxchnge.com/colocation-services/interconnection)** | IX Elérése, Megaport |
| **[T5 Data Centers](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Konnektivitás a nemzeti kutatási és oktatási hálózatokon (NREN) keresztül

| **Szolgáltató**|
| --- |
| **AARNET között**| 
| **DeIC, a GÉANT-on keresztül**|
| **GARR, a GÉANT-on keresztül**|
| **GÉANT**|
| **HEAnet, a GÉANT-on keresztül**|
| **Internet2**|
| **JISC**|
| **RedIRIS, a GÉANT-on keresztül**|
| **SINET**|
| **Surfnet, a GÉANT-on keresztül**|

* Ha a kapcsolatszolgáltatója nem szerepel itt, ellenőrizze, hogy kapcsolódik-e a fent felsorolt ExpressRoute adatcsere-partnerek bármelyikéhez.

## <a name="expressroute-system-integrators"></a>ExpressRoute-rendszerintegrátorok
A privát kapcsolatok igény szerinti beállítása nehéz feladat lehet, a hálózat méretétől függően. A következő táblában felsorolt rendszerintegrátorok bármelyike segítségére lehet az ExpressRoute üzembe helyezésében.

| **Rendszerintegrátor** | **Kontinens** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Európa |
| **[Avanade Inc.](https://www.avanade.com/)** | Ázsia, Európa, Észak-Amerika, Dél-Amerika |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Európa
| **[Ensyst](https://www.ensyst.com.au)** | Ázsia
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Észak-Amerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Észak-Amerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Észak-Amerika |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Ausztrália |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Ausztrália |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Európa (Németország) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Európa |
| **[Új aláírás](https://newsignature.com/technologies/express-route/)** | Európa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Ázsia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Európa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Észak-Amerika |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Észak-Amerika |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Európa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Dél-Amerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Ausztrália |

## <a name="next-steps"></a>További lépések
* Az ExpressRoute-ról további információt az [ExpressRoute gyakori kérdések című](expressroute-faqs.md)témakörben talál.
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Helytérkép"
