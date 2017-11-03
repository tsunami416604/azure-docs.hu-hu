---
title: "Bevezetés az alkalmazás szolgáltatási környezet 1-es verzió"
description: "További tudnivalók az App Service Environment-környezet v1 szolgáltatás, hogy a biztonságos, virtuális hálózatot csatlakoztatott, dedikált méretezési egységek összes, az alkalmazások futtatásához."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: b13f726ada6deba67d5ed484eca769fed354e400
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-app-service-environment-v1"></a>Bevezetés az alkalmazás szolgáltatási környezet 1-es verzió

> [!NOTE]
> Ez a cikk az App Service Environment-környezet v1 tárgya.  Az App Service-környezet, amely könnyebben használható, és nagyobb teljesítményű infrastruktúra fut egy újabb verziója van telepítve. További információt az új verzió útmutató a [az App Service Environment bemutatása](intro.md).
> 

## <a name="overview"></a>Áttekintés
Az App Service Environment-környezet egy [prémium] [ PremiumTier] terv lehetőség a szolgáltatás [Azure App Service](../app-service-web-overview.md) biztosít egy teljesen elkülönített és dedikált környezetben történő biztonságos futtatására Az Azure App Service apps használhat nagy méretezéssel, beleértve a Web Apps, a Mobile Apps és az API-alkalmazások.  

App Service-környezetek ideálisak igénylő alkalmazások és szolgáltatások:

* Nagyon nagy méretű
* Elkülönítés és a biztonságos hálózati hozzáférést

Az ügyfelek több App Service Environment-környezetek belül egyetlen Azure-régió, valamint több Azure-régiók közötti hozhatnak létre.  Így az App Service Environment-környezetek magas RPS munkaterhelések támogatásához állapot nélküli alkalmazások szintek vízszintesen méretezéshez ideális.

App Service-környezetek csak az egyetlen ügyfél-alkalmazások futtatása érdekében elkülönített, és mindig telepített virtuális hálózatba.  Ügyfelek mindkét bejövő és kimenő hálózati forgalom részletes szabályozhatják, és alkalmazások képes kapcsolatot létrehozni a nagy sebességű biztonságos helyszíni vállalati erőforrások virtuális hálózatokon keresztül.

Megtudhatja, hogyan App Service Environment-környezetek engedélyezése a nagy méretű és a biztonságos hálózati hozzáférés, tekintse meg a [AzureCon mélyreható] [ AzureConDeepDive] az App Service Environment-környezetek!

Több App Service Environment-környezetek részletesen a vízszintes méretezést a cikk tekintse meg a telepítő hogyan egy [földrajzilag elosztott alkalmazás erőforrásigényét][GeodistributedAppFootprint].

Hogyan lett konfigurálva a biztonsági architektúrája a AzureCon mélyreható látható, olvassa el a cikk végrehajtási egy [biztonsági architektúrája réteges](app-service-app-service-environment-layered-security.md) az App Service Environment-környezetek.

App Service Environment-környezetek futó alkalmazásokra is hozzáférhet a saját felsőbb rétegbeli eszközök, például a webalkalmazási tűzfalak (WAF) által kezdeményezett.  A cikk a [egy WAF konfigurálása az App Service Environment-környezetek](app-service-app-service-environment-web-application-firewall.md) ebben a forgatókönyvben ismerteti. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Dedikált számítási erőforrásokat
A számítási erőforrásokat egy App Service Environment-környezetben a kizárólag egyetlen előfizetés számára vannak fenntartva, és az egyetlen alkalmazást az App Service-környezetek konfigurálható legfeljebb ötven (50) számítási erőforrással kizárólagos használatra.

Az App Service-környezetek előtér-számítási erőforráskészlet, valamint egy-három feldolgozókészletek számítási erőforrás áll. 

Az előtér-készlet számítási erőforrásokat is automatikus terheléselosztást app kérelmek belül az App Service-környezetek, SSL-lezárást felelős tartalmaz. 

Minden egyes munkavégző készletét tartalmazza a számítási erőforrások vannak rendelve [App Service-csomagok][AppServicePlan], viszont tartalmazó egy vagy több Azure App Service-alkalmazásokhoz.  Az App Service-környezetek legfeljebb három különböző feldolgozókészletek lehet, mivel így rugalmasan kiválaszthatja a különböző számítási erőforrásokat az egyes munkavégző készletét.  

Például így kevésbé hatékony számítási erőforrással egy munkavégző-készlet létrehozása az App Service-csomagok fejlesztési vagy tesztelési alkalmazásokhoz készült.  A második (vagy akár harmadik) feldolgozókészletek használhatja az App Service-csomagok futó termelési alkalmazások szánt nagyobb teljesítményű számítási erőforrásokat.

Az előtér- és a feldolgozói készletek elérhető számítási erőforrások mennyisége további részletekért lásd: [az App Service-környezetek konfigurálása][HowToConfigureanAppServiceEnvironment].  

A rendelkezésre álló számítási erőforrás mérete egy App Service Environment-környezet támogatja a részletekért tekintse meg a [App Service szolgáltatás díjszabása] [ AppServicePricing] lapon, és tekintse át az elérhető lehetőségek a prémium tarifacsomag az App Service-környezetek számára.

## <a name="virtual-network-support"></a>Virtuális hálózati támogatása
Az App Service-környezetek hozhatók létre **vagy** az Azure Resource Manager virtuális hálózati **vagy** klasszikus telepítési modell virtuális hálózat ([további információ a virtuális hálózatok][MoreInfoOnVirtualNetworks]).  Mivel az App Service-környezetek mindig szerepel egy virtuális hálózatot, és pontosan egy virtuális hálózati alhálózat, belül kihasználható a biztonsági mindkét bejövő és kimenő hálózati kommunikáció vezérlésére virtuális hálózatok.  

Az App Service Environment-környezet vagy nyilvános IP-címmel, vagy a belső csak Azure belső Load Balancer (ILB) címmel rendelkező irányuló internetes lehet.

Használhat [hálózati biztonsági csoportok] [ NetworkSecurityGroups] korlátozni a bejövő hálózati kommunikáció az alhálózathoz, ahol az App Service-környezetek található.  Ez lehetővé teszi a felsőbb rétegbeli eszközöket és szolgáltatásokat, például a webalkalmazási tűzfalak és a hálózati Szolgáltatottszoftver-szolgáltatók mögött alkalmazásainak futtatásához.

Alkalmazások is gyakran kell hozzáférhet a vállalati erőforrásokhoz, például a belső adatbázisok és webszolgáltatásokat.  Egy gyakori megoldás, hogy elérhetővé tegye ezeket a végpontokat csak a belső hálózati forgalom továbbítására egy Azure virtuális hálózaton belül.  A belső szolgáltatásként az azonos virtuális hálózatban egy App Service Environment-környezet része, ha a környezetben futó alkalmazások érhetik el azokat, többek között a végpontok segítségével elérhető [pont-pont] [ SiteToSite] és [Azure ExpressRoute] [ ExpressRoute] kapcsolatok.

Az App Service Environment-környezetek virtuális hálózatok és a helyszíni hálózatokban működése vonatkozó részletes információért tekintse meg a következő cikkeket a [hálózati architektúra][NetworkArchitectureOverview], [bejövő forgalom szabályozásának][ControllingInboundTraffic], és [háttérkiszolgálókon való biztonságos kapcsolódás][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Bevezetés
App Service Environment-környezetek megkezdéséhez, lásd: [hogyan számára hozzon létre egy App Service-környezet][HowToCreateAnAppServiceEnvironment]

Az App Service Environment-környezet hálózati architektúra áttekintése, tekintse meg a [hálózati architektúra áttekintése] [ NetworkArchitectureOverview] cikk.

További információ az App Service-környezetek használatáról az ExpressRoute, a következő cikkben: a [Expressroute és az App Service Environment-környezetek][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->


