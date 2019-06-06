---
title: Belső terheléselosztó létrehozása az App Service-környezet – Azure
description: Részletes információk az internettől elzárt Azure App Service Environment létrehozásáról.
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5b05755502ad5836a21080a122d2e1721825f10c
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734683"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Létrehozhat és használhat egy belső Load Balancer App Service-környezet 

Az Azure App Service-környezet üzembe helyezése az Azure App Service egy Azure virtuális hálózaton (VNet) egyik alhálózatában. Az App Service Environment (ASE) üzembe helyezésének két módja van: 

- Egy virtuális IP-cím vagy külső IP-cím, azaz külső ASE használatával.
- Egy virtuális IP-cím vagy belső IP-cím, azaz ILB ASE használatával, amelyet azért neveznek így, mert a belső végpont egy belső terheléselosztó (ILB). 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy ILB ASE környezetet. Az ASE áttekintését [az App Service Environmentet bemutató][Intro] témakör tartalmazza. A külső ASE létrehozásának folyamatát a [külső ASE létrehozását][MakeExternalASE] ismertető témakor tartalmazza.

## <a name="overview"></a>Áttekintés 

Az ASE környezetet közvetlenül az internethez csatlakozó végponttal vagy a virtuális hálózatán belüli IP-címmel is telepítheti. Ahhoz, hogy egy IP-címet hozzárendeljen egy virtuális hálózati címhez, az ASE környezetet ILB terheléskiegyenlítővel kell üzembe helyezni. Az ilb ASE telepítésekor meg kell adnia a nevét, az ASE. Az ASE neve szerepel a tartományi utótag az alkalmazások az ASE környezetben.  A tartományi utótag az ILB ASE &lt;ASE neve&gt;. appservicewebsites.net. Az ILB ASE környezetben végzett alkalmazások ne kerüljenek a nyilvános DNS-ben. 

Az ILB ASE korábbi verzióit, hogy HTTPS-kapcsolatok a tartománynak az utótagját, és a egy alapértelmezett tanúsítvány szükséges. A tartományutótag ILB ASE létrehozásakor már nem gyűjt, és a egy alapértelmezett tanúsítvány már nem gyűjti. Az ILB ASE most létrehozásakor az alapértelmezett tanúsítvány a Microsoft biztosítja, és a böngésző által megbízhatónak tartott. Ön továbbra is képes egyéni tartománynév beállítása az alkalmazásokban az ASE környezetben, és állítsa be a tanúsítványok az egyéni tartománynevekkel. 

ILB ASE környezetben lehetőség van például:

-   Intranet-alkalmazásokat üzemeltethet biztonságosan a felhőben, amely a site-to-site, sem az ExpressRoute használatával érhet el.
-   WAF-eszközzel rendelkező alkalmazások védelme
-   A nyilvános DNS-kiszolgálókon nem szereplő alkalmazásokat üzemeltethet a felhőben.
-   Internettől elzárt háttéralkalmazásokat hozhat létre, amelyekkel az előtéri alkalmazások biztonságosan integrálódhatnak.

### <a name="disabled-functionality"></a>Letiltott funkciók ###

Néhány dolog, amit nem tehet meg ILB ASE használata esetén:

-   IP-alapú SSL használata.
-   IP-címek hozzárendelése az egyes alkalmazásokhoz.
-   Tanúsítvány vásárlása és használata egy alkalmazáshoz az Azure Portalon keresztül. A tanúsítványokat közvetlenül hitelesítésszolgáltatótól szerezheti be, és azokat használhatja az alkalmazásaihoz. Az Azure Portalon keresztül nem szerezheti be őket.

## <a name="create-an-ilb-ase"></a>ILB ASE létrehozása ##

Az ILB ASE létrehozása:

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Web** > **App Service Environment** elemet.

2. Válassza ki előfizetését.

3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.

4. Adja meg az App Service-környezet nevét.

5. Válassza ki a belső virtuális IP-típusa.

    ![ASE létrehozása](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Válassza ki a hálózatkezelés

7. Válassza ki vagy hozzon létre egy virtuális hálózatot. Ha létrehoz egy új Vnetet, azt 192.168.250.0/23 címtartománnyal fogja definiálni. Virtuális hálózat létrehozása vagy egy másik erőforráscsoportban található, mint az ASE-t egy másik címtartománnyal, használja az Azure Virtual Network létrehozása portal. 

8. Válassza ki vagy hozzon létre egy üres alhálózatot. Ha azt szeretné, válassza ki az alhálózatot, üres és nem delegált kell lennie. Az alhálózat méretét nem lehet módosítani, az ASE létrehozását követően. Mi a `/24` méretet javasoljuk, amely 256 címet tartalmaz, és képes kezelni egy maximális méretű ASE környezetet és tetszőleges skálázási igényeket. 

    ![ASE hálózatkezelés][1]

7. Válassza ki **áttekintése és létrehozás** majd **létrehozás**.

## <a name="create-an-app-in-an-ilb-ase"></a>Alkalmazás létrehozása az ILB ASE környezetben ##

Az ILB ASE környezetben a sima ASE környezettel megegyező módon hozhat létre alkalmazást.

1. Az Azure Portalon válassza ki a **erőforrás létrehozása** > **webes** > **webalkalmazás**.

1. Adja meg az alkalmazás nevét.

1. Válassza ki az előfizetést.

1. Válasszon ki vagy hozzon létre egy erőforráscsoportot.

1. Válassza ki a közzététel, a futtatókörnyezeti verem és az operációs rendszer.

1. Válassza ki a helyet, ahol a hely meglévő ILB ASE környezetben.  Egy új ASE app létrehozása során izolált App Service-csomag kiválasztásával is létrehozhat. Ha szeretne egy új ASE létrehozása, válassza ki a régiót, azt szeretné, hogy az ASE-t kell létrehozni.

1. Válassza ki vagy hozzon létre egy App Service-csomagot. 

1. Válassza ki **áttekintése és létrehozás** majd **létrehozás** amikor készen áll.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>WebJobs-feladatok, a Functions és az ILB ASE 

Az ILB ASE támogatja a Functionst és a WebJobs-feladatokat is, de ahhoz, hogy a portál is működjön velük, rendelkeznie kell hálózati hozzáféréssel egy SCM helyhez.  Ez azt jelenti, hogy a böngészőnek olyan gazdagépen kell lennie, amely csatlakozik a virtuális hálózathoz, vagy azon belül van. Ha az ILB ASE rendelkezik, amely nem a tartománynév *appserviceenvironment.net*, hogy a böngészőjéből az scm helyet használja a HTTPS-tanúsítvány megbízhatónak kell.

## <a name="dns-configuration"></a>DNS-konfiguráció 

Külső virtuális IP-cím használata esetén az Azure kezeli a DNS-t. Az ASE környezetben létrehozott összes alkalmazás automatikusan hozzáadódik Azure DNS-hez, amely egy nyilvános DNS. Az ILB ASE környezetben Önnek kell kezelnie a saját DNS-ét. Az ILB ASE környezetben használt tartományi utótag attól függ, hogy az ASE neve. A tartományi utótag  *&lt;ASE neve&gt;. appserviceenvironment.net*. Az ILB IP-címe van, a portál **IP-címek**. 

A DNS-kiszolgáló beállítása:

- Hozzon létre egy zónát a  *&lt;ASE neve&gt;. appserviceenvironment.net*
- create an A record in that zone that points * to the ILB IP address 
- Hozzon létre egy zónát a  *&lt;ASE neve&gt;. scm.appserviceenvironment.net* scm nevű
- Hozzon létre egy rekordot a scm zónában, amely az ILB IP-címre mutat

## <a name="publish-with-an-ilb-ase"></a>Közzététel ILB ASE környezetben

Minden létrehozott alkalmazásnak két végpontja van. Az ILB ASE rendelkezik *&lt;alkalmazásnév&gt;.&lt; ILB ASE tartomány&gt;* és  *&lt;alkalmazásnév&gt;.scm.&lt; ILB ASE tartomány&gt;* . 

Az SCM helynév a Kudu konzolhoz irányítja, az Azure Portal **Speciális portál** részére. A Kudu konzol a környezeti változók megtekintését, a lemez vizsgálatát, a konzol használatát és még sok más funkciót kínál. További információ: [Kudu konzol az Azure App Service-ben][Kudu]. 

A GitHubhoz és az Azure DevOpshoz hasonló internetes alapú CI-rendszerek akkor is működnek az ILB ASE környezettel, ha a buildügynök elérhető az internetről, és ugyanazon a hálózaton található, mint az ILB ASE. Tehát az Azure DevOps esetében, ha a buildügynök ugyanazon a VNET-en lett létrehozva, mint az ILB ASE (lehet más alhálózat), képes lesz lekérni a kódot az Azure DevOps-gitről és telepíteni az ILB ASE-re. Ha nem szeretne létrehozni saját buildügynököt, egy lekérési modellt használó CI-rendszert kell használnia helyette, például a Dropboxot.

Az ILB ASE alkalmazásainak közzétételi végpontjai az ILB ASE létrehozásakor megadott tartományt használják. Ez a tartomány az alkalmazás közzétételi profilján és az alkalmazás portálpanelén jelenik meg (**Áttekintés** > **Alapvető szolgáltatások** és **Tulajdonságok**). Ha a tartományi utótag az ILB ASE rendelkezik  *&lt;ASE neve&gt;. appserviceenvironment.net*, és az alkalmazás neve *mytest*, használjon *mytest.&lt; ASE neve&gt;. appserviceenvironment.net* az FTP-Kiszolgálójának és *mytest.scm.contoso.net* webes üzembe helyezéshez.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Konfigurálja az ILB ASE WAF eszközhöz ##

Az ILB ASE környezetnek, hogy csak az alkalmazásokat, hogy az internetről szeretne, és tartsa a többi csak elérhető a virtuális hálózat egy webes alkalmazás tűzfal (WAF) eszköz egyesíthetők. Ez lehetővé teszi többek között a biztonságos Többrétegű alkalmazások készítését.

Konfigurálja az ILB ASE WAF eszközhöz kapcsolatos további tudnivalókért lásd: [webalkalmazási tűzfal konfigurálása az App Service environment][ASEWAF]. Ez a cikk leírja, hogyan használhatja együtt a Barracuda virtuális berendezést az ASE környezettel. Másik lehetőségként az Azure Application Gateway is használható. Az Application Gateway az alapvető OWASP-szabályokat használja a mögötte elhelyezett alkalmazások biztonságossá tételére. Az Application Gatewayről további információt az [Azure webalkalmazási tűzfal bevezető][AppGW] cikke tartalmaz.

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASE előtt 2019. május

ILB ASE előtt 2019. május szükséges, hogy a tartományi utótag beállítva ASE létrehozása során elvégzett. Akkor szükséges, hogy töltsön fel egy alapértelmezett tanúsítványt, amely a tartományi utótag alapján történt. Ezenkívül régebbi ILB ASE környezetben nem végezhet egyszeri bejelentkezést a Kudu konzolhoz alkalmazásokkal az ILB ASE. When configuring DNS for an older ILB ASE, you need to set the wildcard A record in a zone that matches to your domain suffix. 

## <a name="get-started"></a>Bevezetés ##

* Az ASE használatával kapcsolatos első lépésekről [Az App Service Environment bemutatása][Intro] témakörben olvashat. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
