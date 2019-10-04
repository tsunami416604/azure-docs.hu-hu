---
title: Belső terheléselosztó létrehozása App Service Environment-Azure-val
description: Részletes információk az internettől elzárt Azure App Service Environment létrehozásáról.
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 08a18dc115990ad7d44a8b20412e07995c9af390
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069506"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Belső Load Balancer létrehozása és használata App Service Environment 

A Azure App Service Environment a Azure App Service üzembe helyezése egy Azure-beli virtuális hálózat (VNet) alhálózatában. Az App Service Environment (ASE) üzembe helyezésének két módja van: 

- Egy virtuális IP-cím vagy külső IP-cím, azaz külső ASE használatával.
- Egy virtuális IP-cím vagy belső IP-cím, azaz ILB ASE használatával, amelyet azért neveznek így, mert a belső végpont egy belső terheléselosztó (ILB). 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy ILB ASE környezetet. A bevezetőt a következő témakörben tekintheti át: [app Service környezetek bemutatása][Intro]. A külső kiegészítő szolgáltatás létrehozásával kapcsolatos információkért lásd: [külső][MakeExternalASE]betanító létrehozása.

## <a name="overview"></a>Áttekintés 

Az ASE környezetet közvetlenül az internethez csatlakozó végponttal vagy a virtuális hálózatán belüli IP-címmel is telepítheti. Ahhoz, hogy egy IP-címet hozzárendeljen egy virtuális hálózati címhez, az ASE környezetet ILB terheléskiegyenlítővel kell üzembe helyezni. Ha a ILB-mel telepíti a bevezetést, meg kell adnia a beadási pont nevét. A Bea benyújtó alkalmazásának tartomány-utótagjában a központjának nevét használjuk.  A ILB &lt;&gt;-alapú adatszolgáltatáshoz tartozó tartományi utótag a következő: "appservicewebsites.net". Az ILB-előkészítőben létrehozott alkalmazások nem kerülnek be a nyilvános DNS-be. 

A ILB beadásának korábbi verzióiban meg kell adnia egy tartományi utótagot és egy alapértelmezett tanúsítványt a HTTPS-kapcsolatokhoz. A tartomány utótagját a rendszer már nem gyűjti a ILB-ben, és egy alapértelmezett tanúsítványt is már nem gyűjt. Ha most létrehoz egy ILB-beadást, az alapértelmezett tanúsítványt a Microsoft látja el, és a böngésző megbízhatónak tekinti. Továbbra is beállíthatja az egyéni tartományneveket a beadásban lévő alkalmazásokban, és beállíthat tanúsítványokat az egyéni tartománynevek számára. 

A ILB-t a következőképpen teheti meg:

-   A felhőben biztonságosan üzemeltetheti intranetes alkalmazásait, amely helyek közötti vagy ExpressRoute keresztül érhető el.
-   Alkalmazások WAF-eszközzel történő védelemmel való ellátása
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

4. Adja meg a App Service Environment nevét.

5. Válassza ki a belső virtuális IP-típust.

    ![ASE létrehozása](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. Hálózatkezelés kiválasztása

7. Válasszon ki vagy hozzon létre egy Virtual Network. Ha itt hoz létre új VNet, akkor a rendszer a 192.168.250.0/23 címtartomány alapján határozza meg. Ha a VNet egy másik címtartományból vagy egy másik erőforráscsoporthoz szeretné létrehozni, mint a szolgáltató, használja az Azure Virtual Network létrehozási portált. 

8. Válasszon ki vagy hozzon létre egy üres alhálózatot. Ha ki szeretne választani egy alhálózatot, üresnek kell lennie, és nincs delegálva. Az alhálózat mérete nem módosítható a bekapcsolási szolgáltatás létrehozása után. Mi a `/24` méretet javasoljuk, amely 256 címet tartalmaz, és képes kezelni egy maximális méretű ASE környezetet és tetszőleges skálázási igényeket. 

    ![Bekapcsolási hálózat][1]

7. Válassza **a felülvizsgálat és létrehozás, majd a** **Létrehozás**lehetőséget.

## <a name="create-an-app-in-an-ilb-ase"></a>Alkalmazás létrehozása az ILB ASE környezetben ##

Az ILB ASE környezetben a sima ASE környezettel megegyező módon hozhat létre alkalmazást.

1. A Azure Portal válassza az **erőforrás** > létrehozása**webes** > **webes alkalmazás**lehetőséget.

1. Adja meg az alkalmazás nevét.

1. Válassza ki az előfizetést.

1. Válasszon ki vagy hozzon létre egy erőforráscsoportot.

1. Válassza ki a közzétételi, futtatókörnyezeti verem és operációs rendszer elemet.

1. Válassza ki azt a helyet, ahol a hely egy meglévő ILB.  Az alkalmazás létrehozása során új, elkülönített App Service terv kiválasztásával is létrehozhatja az alkalmazást. Ha új beadást szeretne létrehozni, válassza ki azt a régiót, amelyben létre kívánja hozni a központot.

1. Válassza ki vagy hozzon létre egy App Service-csomagot. 

1. Válassza a **felülvizsgálat és létrehozás** lehetőséget, majd válassza a **Létrehozás** lehetőséget, ha elkészült.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>WebJobs-feladatok, a Functions és az ILB ASE 

Az ILB ASE támogatja a Functionst és a WebJobs-feladatokat is, de ahhoz, hogy a portál is működjön velük, rendelkeznie kell hálózati hozzáféréssel egy SCM helyhez.  Ez azt jelenti, hogy a böngészőnek olyan gazdagépen kell lennie, amely csatlakozik a virtuális hálózathoz, vagy azon belül van. Ha a ILB-előkészítés olyan tartománynevet tartalmaz, amely nem a *appserviceenvironment.net*végződik, akkor a böngészővel meg kell bíznia az SCM-hely által használt HTTPS-tanúsítványt.

## <a name="dns-configuration"></a>DNS-konfiguráció 

Külső virtuális IP-cím használata esetén az Azure kezeli a DNS-t. Az ASE környezetben létrehozott összes alkalmazás automatikusan hozzáadódik Azure DNS-hez, amely egy nyilvános DNS. Az ILB ASE környezetben Önnek kell kezelnie a saját DNS-ét. A ILB-előállítók által használt tartomány utótagja a kiegészítő csomag nevétől függ. A tartomány utótagja  *&lt;a appserviceenvironment.net&gt;neve.* A ILB IP-címe a portálon az **IP-címek**területen található. 

A DNS konfigurálása:

- hozzon létre egy zónát a benyújtó  *&lt;&gt;neve számára. appserviceenvironment.net*
- hozzon létre egy olyan rekordot az adott zónában, amely * a ILB IP-címére mutat. 
- hozzon létre egy zónát a központhoz tartozó  *&lt;név&gt;. appserviceenvironment.net* neve: SCM
- hozzon létre egy rekordot az SCM-zónában, amely a ILB IP-címére mutat.

## <a name="publish-with-an-ilb-ase"></a>Közzététel ILB ASE környezetben

Minden létrehozott alkalmazásnak két végpontja van. Egy ILB *&lt;&gt;-ben az alkalmazás neve szerepel.&lt; ILB&gt;* és *az alkalmazás&lt;neve&lt; .SCM&gt;. ILB&gt;* . 

Az SCM helynév a Kudu konzolhoz irányítja, az Azure Portal **Speciális portál** részére. A Kudu konzol a környezeti változók megtekintését, a lemez vizsgálatát, a konzol használatát és még sok más funkciót kínál. További információ: kudu- [konzol a Azure app Servicehoz][Kudu]. 

A GitHubhoz és az Azure DevOpshoz hasonló internetes alapú CI-rendszerek akkor is működnek az ILB ASE környezettel, ha a buildügynök elérhető az internetről, és ugyanazon a hálózaton található, mint az ILB ASE. Tehát az Azure DevOps esetében, ha a buildügynök ugyanazon a VNET-en lett létrehozva, mint az ILB ASE (lehet más alhálózat), képes lesz lekérni a kódot az Azure DevOps-gitről és telepíteni az ILB ASE-re. Ha nem szeretne létrehozni saját buildügynököt, egy lekérési modellt használó CI-rendszert kell használnia helyette, például a Dropboxot.

Az ILB ASE alkalmazásainak közzétételi végpontjai az ILB ASE létrehozásakor megadott tartományt használják. Ez a tartomány az alkalmazás közzétételi profilján és az alkalmazás portálpanelén jelenik meg (**Áttekintés** > **Alapvető szolgáltatások** és **Tulajdonságok**). Ha van egy ILB, amely a (z  *&lt;&gt;) "appserviceenvironment.net*" nevű tartománynév-utótagot és egy *mytest*nevű alkalmazást használ, használja a *mytest.&lt; Bevezetési név&gt;. appserviceenvironment.net* FTP-hez és *mytest.SCM.contoso.net* webes telepítéshez.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>ILB-beadás konfigurálása WAF-eszközzel ##

A webalkalmazási tűzfal (WAF) és a ILB-elősegítő eszközök összevonásával csak az interneten található alkalmazásokat teheti elérhetővé, és a többiet csak a VNet érheti el. Ez lehetővé teszi, hogy többek között biztonságos többrétegű alkalmazásokat építsen ki.

Ha többet szeretne megtudni arról, hogyan konfigurálhatja a ILB a WAF eszközzel, tekintse meg a webalkalmazási [tűzfal konfigurálása a app Service][ASEWAF]-környezettel című témakört. Ez a cikk leírja, hogyan használhatja együtt a Barracuda virtuális berendezést az ASE környezettel. Másik lehetőségként az Azure Application Gateway is használható. Az Application Gateway az alapvető OWASP-szabályokat használja a mögötte elhelyezett alkalmazások biztonságossá tételére. Az Application Gatewayról további információt [Az Azure webalkalmazási tűzfal bemutatása][AppGW]című témakörben talál.

## <a name="ilb-ases-made-before-may-2019"></a>ILB ASE a május 2019. előtt

Az 2019. május előtt elkészített ILB-ASE a tartomány utótagjának a központilag történő létrehozása során történő beállításához szükséges. Emellett az adott tartományi utótagon alapuló alapértelmezett tanúsítvány feltöltésére is szükség van. Emellett egy régebbi ILB-benyújtó használatával nem végezheti el az egyszeri bejelentkezést a kudu-konzolon a ILB-elősegítő alkalmazásokkal. Ha a DNS-t egy régebbi ILB-alapú központhoz konfigurálja, akkor egy olyan rekordot kell beállítania egy olyan zónában, amely megfelel a tartomány utótagjának. 

## <a name="get-started"></a>Bevezetés ##

* A ASE használatának megkezdéséhez tekintse [meg a app Service környezetek bemutatása][Intro]című témakört. 

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
