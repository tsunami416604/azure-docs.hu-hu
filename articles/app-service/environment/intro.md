---
title: "Bevezetés az Azure App Service-környezetek"
description: "Azure App Service-környezetek rövid áttekintése"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 63e2256ca6d392a0cd284269e8c2d059f94e9139
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="introduction-to-app-service-environments"></a>App Service-környezetek bemutatása #
 
## <a name="overview"></a>Áttekintés ##

Az Azure App Service Environment-környezet az Azure App Service szolgáltatása, amely egy teljesen elkülönített és dedikált környezetben történő biztonságos futtatására, nagy méretekben App Service-alkalmazásokhoz. Ez a funkció, a webalkalmazások rendelkezhet [mobilalkalmazások][mobileapps], API-alkalmazás, és [funkciók][Functions].

App Service-környezetek (ASEs) megfelelőek igénylő alkalmazások és szolgáltatások:

- Nagyon nagy méretű.
- Elkülönítés és a biztonságos hálózati hozzáférést.
- Magas memóriahasználat.

Az ügyfelek több ASEs egyetlen Azure régión belül vagy között több Azure-régiók hozhatnak létre. A rugalmasságnak köszönhetően az ASEs ideális vízszintesen skálázás állapot nélküli alkalmazások rétegek magas RPS munkaterhelések támogatásához.

ASEs csak az egyetlen ügyfél-alkalmazások futtatása érdekében elkülönített, és mindig telepített virtuális hálózatba. Az ügyfelek befolyásolni részletes bejövő és kimenő hálózati forgalmat. Alkalmazások képes kapcsolatot létrehozni a nagy sebességű biztonságos keresztül VPN-ek a helyszíni vállalati erőforrásokhoz.

* ASEs engedélyezése a nagy méretű alkalmazást üzemeltető biztonságos hálózati hozzáférést. További információkért lásd: a [AzureCon mélyreható](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/) ASEs meg.
* Több ASEs segítségével horizontálisan méretezhető. További információkért lásd: [egy földrajzilag elosztott alkalmazás erőforrásigényét beállításával](app-service-app-service-environment-geo-distributed-scale.md).
* ASEs segítségével konfigurálja a biztonsági architektúrája látható módon a AzureCon mélyreható. Hogyan lett konfigurálva a biztonsági architektúrája a AzureCon mélyreható látható, olvassa el a [cikk egy többrétegű biztonsági architektúra megvalósításához](app-service-app-service-environment-layered-security.md) az App Service-környezetek.
* ASEs futó alkalmazásokra is hozzáférhet a saját felsőbb rétegbeli eszközök, például a webalkalmazási tűzfalak (WAFs) által kezdeményezett. További információkért lásd: [egy WAF az App Service-környezetek konfigurálása](app-service-app-service-environment-web-application-firewall.md).

## <a name="dedicated-environment"></a>Dedikált környezetben ##

Egy ASE kizárólag egyetlen előfizetés van kijelölve, és tárolhatja, 100 példányok. A tartomány 100 egypéldányos App Service-csomagokról egy egyetlen App Service-csomaggal, és mindent az között is kiterjedhet 100 példányok.

Egy ASE előtér-webkiszolgálóinak és alkalmazottak tevődik össze. Előtér-webkiszolgálóinak HTTP/HTTPS-lezárást és automatikus terheléselosztást app kérelmek belül egy ASE felelősek. Előtér-webkiszolgálóinak automatikusan, a ASE az App Service-csomagokról is kiterjeszthető.

Munkavállalók alkalmazások vevői üzemeltető szerepkörök. Három rögzített méretű munkavállalók érhetők el:

* Egy mag/3.5 GB RAM
* Két alapvető/7 GB RAM
* Négy alapvető/14 GB RAM

Az ügyfelek nem kell kezelniük, előtér-webkiszolgálóinak és alkalmazottak. Az összes infrastruktúra automatikusan nincs felvéve az ügyfelek kibővítési az App Service-csomagokról. Az App Service-csomagok létrehozása, illetve -környezetben méretezhető, a rendszer hozzáadásakor vagy eltávolításakor szükség szerint a szükséges infrastruktúrát.

Egy egyszerű havi arány egy, az infrastruktúra fizet, és nem módosítja a ASE méretének ASE van. Emellett nincs egy App Service-csomag core onkénti költséget. Minden alkalmazás-környezetben üzemeltetett a Termékváltozat árképzési elszigetelt szerepelnek. Egy ASE az árakkal kapcsolatos információkért lásd: a [App Service díjszabás] [ Pricing] lapon, és tekintse át az elérhető lehetőségek a ASEs.

## <a name="virtual-network-support"></a>Virtuális hálózati támogatása ##

Egy ASE csak az Azure Resource Manager virtuális hálózat is létrehozható. Egy Azure virtuális hálózatot kapcsolatos további tudnivalókért tekintse meg a [Azure virtuális hálózatok – gyakori kérdések](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). Egy ASE mindig létezik-e virtuális hálózatban, és pontosabban, a virtuális hálózat egy alhálózaton belül. A biztonsági szolgáltatások a virtuális hálózatok segítségével szabályozhatja a bejövő és kimenő hálózati kommunikáció az alkalmazásokhoz.

Egy ASE lehet internetre irányuló nyilvános IP-cím vagy a belső hálózati rendelkező csak egy Azure belső terheléselosztó (ILB)-címén.

[Hálózati biztonsági csoportok] [ NSGs] bejövő hálózati kommunikáció a egy ASE tartalmazó alhálózathoz korlátozása. Az NSG-k segítségével mögött fölérendelt eszközöket és szolgáltatásokat, például WAFs és hálózati Szolgáltatottszoftver-szolgáltatók alkalmazásainak futtatásához.

Alkalmazások is gyakran kell hozzáférhet a vállalati erőforrásokhoz, például a belső adatbázisok és webszolgáltatásokat. Ha a VPN-kapcsolat a helyszíni hálózat a virtuális hálózatban ASE telepít, az alkalmazásokat a ASE érhetik el a helyszíni erőforrások. Ez a funkció értéke true, függetlenül attól, hogy van-e VPN-Kapcsolaton egy [pont-pont](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/) vagy [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/) VPN.

A virtuális hálózatok és a helyszíni hálózatokban ASEs működése további információkért lásd: [App Service Environment-környezet hálózati szempontok][ASENetwork].

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>App Service-környezet v1 ##

App Service Environment-környezet két verziója van: ASEv1 és ASEv2. A fenti adatokat ASEv2 alapján. Ez a szakasz a ASEv1 és ASEv2 közötti különbséget mutatja. 

ASEv1 szükség manuális kezelése összes erőforrást. Amely tartalmazza az előtér-webkiszolgálóinak dolgozó munkatársak és az IP-alapú SSL-hez használt IP-címek. Ki lehet terjeszteni a App Service-csomagot, mielőtt a feldolgozókészleten, ahol szeretné futtatni az első bővíteni kell.

ASEv1 ASEv2 a különböző árképzési modellt használ. ASEv1 a minden lefoglalt core fizetnie. Az előtér-webkiszolgálóinak vagy bármilyen számítási feladatot nem futtató munkavállalók használt mag, amely tartalmazza. A ASEv1 az alapértelmezett maximális méretű egy ASE mérete 55 összes állomás. Amely tartalmazza a dolgozók és első akkor ér véget. Egy ASEv1 előnye, hogy központilag telepíthető a klasszikus virtuális hálózatot és egy erőforrás-kezelő virtuális hálózatot. ASEv1 kapcsolatos további információkért lásd: [App Service Environment-környezet v1 bemutatása][ASEv1Intro].

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
