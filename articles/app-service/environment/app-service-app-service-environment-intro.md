---
title: Alkalmazás bemutatása környezet v1 - Azure-szolgáltatás
description: Ismerje meg az App Service Environment v1 funkció, amely biztonságos, VNet-csatlakozású, dedikált skálázási egységek biztosít a futó összes alkalmazását.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 205e61634a902ef052f665c65f2b67a66e6fe598
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728227"
---
# <a name="introduction-to-app-service-environment-v1"></a>Bevezetés az App Service környezet v1

> [!NOTE]
> Ez a cikk az App Service Environment-környezet v1 szól.  Nincs az App Service-környezet, amely egyszerűbb és nagyobb teljesítményű infrastruktúra fut egy újabb verziója. További információ az új verzió elindítása a [az App Service Environment bemutatása](intro.md).
> 

## <a name="overview"></a>Áttekintés
App Service-környezet van egy [prémium] [ PremiumTier] csomag lehetőséget a szolgáltatás [Azure App Service](../overview.md) biztonságos futtatása teljesen elkülönített és dedikált környezetet biztosít, amely Az Azure App Service-alkalmazások nagy méretben, beleértve a Web Apps, Mobile Apps és API-alkalmazások  

App Service Environment-környezetek ideálisak igénylő alkalmazások és szolgáltatások:

* Nagyon nagy skálázhatóság
* Elkülönítés és biztonságos hálózati hozzáférést

Ügyfeleinknek létrehozhatnak több App Service Environment-környezetek egy adott Azure-régión belül, valamint több Azure-régiók között.  Így App Service Environment-környezetek ideális a horizontális skálázás az állapot nélküli alkalmazásrétegek magas RPS-munkaterhelések támogatásához.

App Service Environment-környezetek elkülönített futnak, ha csak egy egyetlen ügyfél alkalmazásait, és mindig egy virtuális hálózatban üzemelnek.  Mindkét alkalmazás bejövő és kimenő hálózati forgalom részletesen szabályozhatja az ügyfél rendelkezik, és az alkalmazások nagy sebességű, biztonságos kapcsolatot létesíthet virtuális hálózatokon keresztül kapcsolódjanak a helyi vállalati erőforrásokhoz.

Áttekintését, hogyan App Service Environment-környezetek nagy méretű és biztonságos hálózati hozzáférés, tekintse meg a [AzureCon Deep Dive] [ AzureConDeepDive] az App Service Environment-környezetek!

Részletesen a horizontális skálázás használatával több App Service Environment-környezetek tekintse meg a cikk beállítása egy [földrajzilag elosztott alkalmazás üzembe helyezésének előkészítése][GeodistributedAppFootprint].

Hogyan lett konfigurálva az AzureCon Deep Dive látható biztonsági architektúra, olvassa el a cikk a végrehajtási egy [szintekre épülő biztonsági architektúra](app-service-app-service-environment-layered-security.md) App Service Environment-környezetekkel.

App Service-környezetekben futó alkalmazások elérhetik a felsőbb rétegbeli eszközök, például webalkalmazás-tűzfalak (WAF) engedi át.  A cikk a [a WAF konfigurálása az App Service Environment-környezetek](app-service-app-service-environment-web-application-firewall.md) ebben a forgatókönyvben ismerteti. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Dedikált számítási erőforrások
Kizárólag egyetlen előfizetéshez az dedikált App Service Environment-környezetben a számítási erőforrásokat, és a egy bizonyos alkalmazás az App Service Environment is konfigurálhatók, legfeljebb ötven (50) számítási erőforrások kizárólagos használatára.

App Service-környezet egy előtér-számítási erőforráskészlethez, valamint egy, három feldolgozókészletek számítási erőforrás áll. 

Az előtérkészlet felelős alkalmazás kérelmek App Service-környezetben is automatikus terheléselosztást, SSL-lezárást számítási erőforrásokat tartalmaz. 

Minden feldolgozói készlethez hozzárendelt számítási erőforrások tartalmaz [App Service-csomagok][AppServicePlan], amelyek viszont egy vagy több Azure App Service-alkalmazások tartalmaznak.  Mivel az App Service-környezet legfeljebb három különböző feldolgozókészletek lehetnek, rugalmasan választhatja ki a különböző számítási erőforrásokhoz minden feldolgozói készlethez rendelkezik.  

Például ez lehetővé teszi, hogy hozzon létre egy feldolgozókészlet kevésbé hatékony számítási erőforrásokat az App Service-csomagok fejlesztési-tesztelési alkalmazások számára készült.  Egy második (vagy akár harmadik) feldolgozókészlet használhatja az App Service-csomagok éles alkalmazások futtatását szánt nagyobb teljesítményű számítási erőforrásokat.

Az előtér- és a feldolgozói készleteiben elérhető számítási erőforrások mennyisége a további részletekért lásd: [App Service Environment konfigurálása][HowToConfigureanAppServiceEnvironment].  

Az elérhető számítási erőforrás méretek támogatott az App Service-környezet részletes ismertetéséért tekintse meg a [App Service díjszabását] [ AppServicePricing] lapon, és tekintse át az App Service Environment-környezetek az elérhető lehetőségek a Prémium szintű tarifacsomag.

## <a name="virtual-network-support"></a>Virtuálishálózat-támogatást
Az App Service-környezet hozható létre **vagy** egy Azure Resource Managerbeli virtuális hálózat **vagy** a klasszikus üzemi modellt használó virtuális hálózatokhoz ([további információ a virtuális hálózatok] [MoreInfoOnVirtualNetworks]).  Mivel az App Service-környezet mindig létezik a virtuális hálózatban, és pontosan egy virtuális hálózat alhálózatához belül, kihasználhatja mind a bejövő és kimenő hálózati kommunikáció szabályozhatja a virtuális hálózatok biztonsági funkciói.  

App Service-környezet vagy az internetre irányuló nyilvános IP-címmel, vagy csak az Azure belső Load Balancer (ILB) címmel rendelkező belső is lehet.

Használhat [hálózati biztonsági csoportok] [ NetworkSecurityGroups] korlátozni a beérkező hálózati kommunikációt az alhálózaton, ahol az App Service-környezet található.  Ez lehetővé teszi, hogy alkalmazásokat rétegbeli eszközök és szolgáltatások, például a webalkalmazás-tűzfalak és hálózati SaaS-szolgáltatók mögött.

Az alkalmazásoknak gyakran kell hozzáférniük vállalati erőforrásokhoz, például belső adatbázisokhoz vagy webes szolgáltatásokhoz.  Általánosan használt megközelítés, hogy elérhetővé tegye ezeket a végpontokat csak a belső hálózati forgalmat egy Azure virtuális hálózatban lévő tárfiókba.  Után az App Service-környezet csatlakozik a belső szolgáltatások ugyanazon a virtuális hálózaton, akkor a környezetben futó alkalmazások elérheti őket, beleértve a végpontok segítségével elérhető [Site-to-Site] [ SiteToSite] és [Azure ExpressRoute] [ ExpressRoute] kapcsolatok.

Az App Service Environment-környezetek virtuális hálózatok és a helyszíni hálózatok működéséről további részleteiért tekintse meg a következő cikkeket a [hálózati architektúra][NetworkArchitectureOverview], [bejövő szabályozása Forgalom][ControllingInboundTraffic], és [biztonságosan kapcsolódó háttérkomponenseinek][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Első lépések
Első lépések az App Service Environment-környezetek, lásd: [hogyan, hozzon létre egy App Service-környezet][HowToCreateAnAppServiceEnvironment]

Az App Service Environment-környezet hálózati architektúra áttekintését lásd: a [a hálózati architektúra áttekintése] [ NetworkArchitectureOverview] cikk.

Az App Service-környezet használata az expressroute-tal, részletek: a következő cikkben található [Express Route- és App Service Environment-környezetek][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: https://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->


