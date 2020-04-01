---
title: Bevezetés az ASE 1-es v1-be
description: Ismerje meg az App Service-környezet v1 funkcióit. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: cd3881617227430488f8dd3f2f3d24072b24b8ce
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478727"
---
# <a name="introduction-to-app-service-environment-v1"></a>Bevezetés az App Service-környezet 1-es alkalmazásába

> [!NOTE]
> Ez a cikk az App Service-környezet 1-es v1-es programjáról szól.  Az App Service-környezet egy újabb verziója könnyebben használható, és hatékonyabb infrastruktúrán fut. Ha többet szeretne megtudni az új verzió kezdődik az [App Service-környezet bemutatása](intro.md).

## <a name="overview"></a>Áttekintés

Az App Service-környezet az [Azure App Service](../overview.md) prémium [szintű][PremiumTier] szolgáltatási csomagja, amely teljesen elszigetelt és dedikált környezetet biztosít az Azure App Service-alkalmazások nagy léptékű biztonságos futtatásához, beleértve a webalkalmazásokat, a mobilalkalmazásokat és az API-alkalmazásokat.  

Az App Service-környezetek ideálisak az alkalmazás-munkaterhelések számára, amelyeka következőket igénylik:

* Nagyon nagy léptékű
* Elkülönítés és biztonságos hálózati hozzáférés

Az ügyfelek több App Service-környezetet is létrehozhatnak egyetlen Azure-régióban, valamint több Azure-régióban.  Ez ideálissá teszi az App Service-környezeteket az állapotnélküli alkalmazásszintek horizontális skálázásához a magas RPS-számítási feladatok támogatása érdekében.

Az App Service-környezetek csak egyetlen ügyfél alkalmazásainak futtatásához vannak elkülönítve, és mindig egy virtuális hálózatba vannak telepítve.  Az ügyfelek részletesen szabályozhatják mind a bejövő, mind a kimenő alkalmazáshálózati forgalmat, és az alkalmazások nagy sebességű biztonságos kapcsolatokat hozhatnak létre a virtuális hálózatokon keresztül a helyszíni vállalati erőforrásokkal.

Ha áttekintést nyújt arról, hogy az App Service-környezetek hogyan teszik lehetővé a nagy léptékű és biztonságos hálózati hozzáférést, tekintse meg az [AzureCon Deep Dive][AzureConDeepDive] az App Service-környezeteken!

A több App Service-környezethasználatával történő horizontális skálázásrészletes merülést a [földrajzilag elosztott alkalmazáslábnyom][GeodistributedAppFootprint]beállításáról szóló cikk ismerteti.

Az AzureCon Deep Dive-ban látható biztonsági architektúra konfigurálásának módjáról az App Service-környezetekkel [egy réteges biztonsági architektúra](app-service-app-service-environment-layered-security.md) megvalósításáról szóló cikkben olvashat.

Az App Service-környezetekben futó alkalmazások hozzáférését a felsőáramú eszközök, például a webalkalmazás-tűzfalak (WAF) biztosíthatják.  A [WAF app service-környezetek konfigurálása](app-service-app-service-environment-web-application-firewall.md) című cikk ismerteti ezt a forgatókönyvet.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Dedikált számítási erőforrások

Az App Service-környezet ben lévő összes számítási erőforrás kizárólag egyetlen előfizetéshez van leképezve, és egy App Service-környezet legfeljebb ötven (50) számítási erőforrással konfigurálható egyetlen alkalmazás kizárólagos használatához.

Az App Service-környezet egy előtér-alapú számítási erőforráskészletből, valamint egy-három feldolgozó számítási erőforráskészletből áll.

Az előtér-készlet a TLS-végződésért felelős számítási erőforrásokat, valamint az App Service-környezeten belüli alkalmazáskérelmek automatikus terheléselosztását tartalmazza.

Minden munkavégző készlet az [App Service-csomagokhoz][AppServicePlan]rendelt számítási erőforrásokat tartalmaz, amelyek viszont egy vagy több Azure App Service-alkalmazást tartalmaznak.  Mivel egy App Service-környezetben legfeljebb három különböző munkavégző készlet lehet, rugalmasan választhat különböző számítási erőforrásokat az egyes munkavégző készletekhez.  

Ez lehetővé teszi például, hogy hozzon létre egy munkavégző készlet kevésbé hatékony számítási erőforrásokat app service-csomagok fejlesztési vagy tesztelési alkalmazások hoz létre.  Egy második (vagy akár harmadik) feldolgozókészlet hatékonyabb számítási erőforrásokat használhat az éles alkalmazásokat futtató App Service-csomagok számára.

Az előtér- és feldolgozókészletek számára elérhető számítási erőforrások mennyiségéről az [App Service-környezet konfigurálása][HowToConfigureanAppServiceEnvironment]című témakörben talál további információt.  

Az App Service-környezetben támogatott, rendelkezésre álló számítási erőforrás-méretekkel kapcsolatos részletekért tekintse meg az [App Service díjszabási][AppServicePricing] lapját, és tekintse át az App Service-környezetek elérhető beállításait a prémium díjszabási szinten.

## <a name="virtual-network-support"></a>Virtuális hálózati támogatás

Az App Service-környezet létrehozható **az** Azure Resource Manager virtuális hálózatában **vagy** egy klasszikus üzembe helyezési modell virtuális hálózatában[(további információ a virtuális hálózatokról).][MoreInfoOnVirtualNetworks]  Mivel az App Service-környezet mindig létezik egy virtuális hálózatban, pontosabban egy virtuális hálózat alhálózatán belül, a virtuális hálózatok biztonsági szolgáltatásaival szabályozhatja a bejövő és a kimenő hálózati kommunikációt is.  

Az App Service-környezet lehet egy nyilvános IP-címmel rendelkező internet, vagy csak egy Azure belső terheléselosztó (ILB) címmel rendelkező belső.

[A hálózati biztonsági csoportok][NetworkSecurityGroups] segítségével korlátozhatja a bejövő hálózati kommunikációt arra az alhálózatra, ahol az App Service-környezet található.  Ez lehetővé teszi, hogy alkalmazásokat futtasson a felső streamelési eszközök és szolgáltatások, például a webalkalmazás-tűzfalak és a hálózati SaaS-szolgáltatók mögött.

Az alkalmazásoknak gyakran kell hozzáférniük vállalati erőforrásokhoz, például belső adatbázisokhoz vagy webes szolgáltatásokhoz.  Gyakori megközelítés, hogy ezeket a végpontokat csak az Azure virtuális hálózaton belül áramló belső hálózati forgalom számára elérhetővé teszi.  Miután egy App Service-környezet csatlakozik ugyanahhoz a virtuális hálózathoz, mint a belső szolgáltatások, a környezetben futó alkalmazások hozzáférhetnek hozzájuk, beleértve a helyek közötti és [az Azure ExpressRoute-kapcsolatokon][ExpressRoute] keresztül elérhető végpontokat is. [Site-to-Site][SiteToSite]

Az App Service-környezetek virtuális hálózatokkal és helyszíni hálózatokkal való működéséről a hálózati architektúra , a bejövő forgalom szabályozása és a háttérrendszerekhez való biztonságos csatlakozás című cikkekről szóló további részletekért további információt a [hálózati architektúráról][NetworkArchitectureOverview], [a bejövő forgalom szabályozásáról][ControllingInboundTraffic]és [a háttérrendszerekhez való biztonságos csatlakozásról][SecurelyConnectingToBackends]szóló cikkben talál. 

## <a name="getting-started"></a>Első lépések

Az App Service-környezetek ismerkedése az [App Service-környezet létrehozása][HowToCreateAnAppServiceEnvironment] című témakörben

Az App Service-környezet hálózati architektúrájának áttekintését a [Hálózati architektúra áttekintése][NetworkArchitectureOverview] című cikkben találja.

Az App Service-környezet ExpressRoute-tal való használatáról az alábbi cikkben olvashat az Express Route és az [App Service-környezetek szolgáltatásról.][NetworkConfigDetailsForExpressRoute]

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