---
title: ILB ASE létrehozása ARM-mal
description: Ismerje meg, hogyan hozhat létre egy App Service-környezetet egy belső terheléselosztóval (ILB ASE) az Azure Resource Manager-sablonok használatával. Teljesen elkülönítheti alkalmazásait az internettől.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: b7fa447e8564fcbf77702f1d3d474cceb48705c5
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114634"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Belső terheléselosztó alkalmazásszolgáltatás-környezet létrehozása és használata 

Az Azure App Service Environment az Azure App Service egy alhálózat egy Azure virtuális hálózat (VNet) üzembe helyezését. Az App Service Environment (ASE) üzembe helyezésének két módja van: 

- Egy virtuális IP-cím vagy külső IP-cím, azaz külső ASE használatával.
- Egy virtuális IP-cím vagy belső IP-cím, azaz ILB ASE használatával, amelyet azért neveznek így, mert a belső végpont egy belső terheléselosztó (ILB). 

Ebből a cikkből megtudhatja, hogyan hozhat létre egy ILB ASE környezetet. Az ASE áttekintését az [App Service-környezetek bemutatása című témakörben találja.][Intro] A külső ASE létrehozásának folyamatát a [külső ASE létrehozását][MakeExternalASE] ismertető témakor tartalmazza.

## <a name="overview"></a>Áttekintés 

Az ASE környezetet közvetlenül az internethez csatlakozó végponttal vagy a virtuális hálózatán belüli IP-címmel is telepítheti. Ahhoz, hogy egy IP-címet hozzárendeljen egy virtuális hálózati címhez, az ASE környezetet ILB terheléskiegyenlítővel kell üzembe helyezni. Amikor telepíti az ASE egy ILB, meg kell adnia az ASE nevét. The name of your ASE is used in the domain suffix for the apps in your ASE.  Az ILB ASE tartományutótagja &lt;AZ ASE neve&gt;.appserviceenvironment.net. Az ILB ASE-ben készült alkalmazások nem kerülnek a nyilvános DNS-be. 

Az ILB ASE korábbi verzióinak meg kellett adnia egy tartományutótagot és egy alapértelmezett tanúsítványt a HTTPS-kapcsolatokhoz. A tartományutótag már nem történik meg az ILB ASE létrehozásakor, és az alapértelmezett tanúsítvány is már nem kerül összegyűjtésre. Amikor most hoz létre egy ILB ASE-t, az alapértelmezett tanúsítványt a Microsoft biztosítja, és a böngésző megbízható. Továbbra is beállíthatja az egyéni tartományneveket az ASE-ben lévő alkalmazásokban, és tanúsítványokat állíthat be ezeken az egyéni tartományneveken. 

Az ILB ASE segítségével többek között a következőket teheti:

-   Az intranetes alkalmazásokat biztonságosan üzemeltetheti a felhőben, amelyeket egy helyről webhelyre vagy ExpressRoute-on keresztül érhet el.
-   Alkalmazások védelme WAF-eszközzel
-   A nyilvános DNS-kiszolgálókon nem szereplő alkalmazásokat üzemeltethet a felhőben.
-   Internettől elzárt háttéralkalmazásokat hozhat létre, amelyekkel az előtéri alkalmazások biztonságosan integrálódhatnak.

### <a name="disabled-functionality"></a>Letiltott funkciók ###

Néhány dolog, amit nem tehet meg ILB ASE használata esetén:

-   IP-alapú SSL használata.
-   IP-címek hozzárendelése az egyes alkalmazásokhoz.
-   Tanúsítvány vásárlása és használata egy alkalmazáshoz az Azure Portalon keresztül. A tanúsítványokat közvetlenül hitelesítésszolgáltatótól szerezheti be, és azokat használhatja az alkalmazásaihoz. Az Azure Portalon keresztül nem szerezheti be őket.

## <a name="create-an-ilb-ase"></a>ILB ASE létrehozása ##

Az ILB ASE létrehozása:

1. Az Azure Portalon válassza az > Erőforrás létrehozása**Web** > **App Service Environment** **lehetőséget.**

2. Válassza ki előfizetését.

3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.

4. Adja meg az App Service-környezet nevét.

5. Válassza ki a belső virtuális IP-típusát.

    ![ASE létrehozása](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> Az App Service-környezet neve legfeljebb 37 karakter lehet.

6. Hálózat kiválasztása

7. Virtuális hálózat kiválasztása vagy létrehozása. Ha itt új virtuális hálózatot hoz létre, az 192.168.250.0/23 címtartományban lesz definiálva. Ha egy másik címtartományú virtuális hálózatot vagy az ASE-nél eltérő erőforráscsoportban szeretne létrehozni, használja az Azure virtuális hálózat létrehozási portálját. 

8. Jelöljön ki vagy hozzon létre egy üres alhálózatot. Ha alhálózatot szeretne kijelölni, annak üresnek és delegálásnak kell lennie. Az alhálózat mérete nem módosítható az ASE létrehozása után. Mi a `/24` méretet javasoljuk, amely 256 címet tartalmaz, és képes kezelni egy maximális méretű ASE környezetet és tetszőleges skálázási igényeket. 

    ![ASE-hálózat][1]

7. Válassza **a Véleményezés és létrehozás,** majd a **Létrehozás lehetőséget.**


## <a name="create-an-app-in-an-ilb-ase"></a>Alkalmazás létrehozása az ILB ASE környezetben ##

Az ILB ASE környezetben a sima ASE környezettel megegyező módon hozhat létre alkalmazást.

1. Az Azure portalon válassza az > Erőforrás létrehozása**webalkalmazás** > **Web App** **lehetőséget.**

1. Adja meg az alkalmazás nevét.

1. Válassza ki az előfizetést.

1. Válasszon ki vagy hozzon létre egy erőforráscsoportot.

1. Válassza ki a Közzététel, a Futásidejű verem és az operációs rendszer lehetőséget.

1. Válassza ki azt a helyet, ahol a hely egy meglévő ILB ASE.  Új ASE-t is létrehozhat az alkalmazás létrehozása során egy elkülönített app service-csomag kiválasztásával. Ha új ASE-t szeretne létrehozni, válassza ki azt a régiót, amelyben létre szeretné hozni az ASE-t.

1. Válassza ki vagy hozzon létre egy App Service-csomagot. 

1. Válassza **a Véleményezés és létrehozás** lehetőséget, majd a **Létrehozás lehetőséget,** ha készen áll.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>WebJobs-feladatok, a Functions és az ILB ASE 

Az ILB ASE támogatja a Functionst és a WebJobs-feladatokat is, de ahhoz, hogy a portál is működjön velük, rendelkeznie kell hálózati hozzáféréssel egy SCM helyhez.  Ez azt jelenti, hogy a böngészőnek olyan gazdagépen kell lennie, amely csatlakozik a virtuális hálózathoz, vagy azon belül van. Ha az ILB ASE tartományneve nem *appserviceenvironment.net*végződik, akkor be kell szereznie a böngészőt, hogy megbízzon az scm-webhely által használt HTTPS-tanúsítványban.

## <a name="dns-configuration"></a>DNS-konfiguráció 

Külső virtuális IP-cím használata esetén az Azure kezeli a DNS-t. Az ASE környezetben létrehozott összes alkalmazás automatikusan hozzáadódik Azure DNS-hez, amely egy nyilvános DNS. Az ILB ASE környezetben Önnek kell kezelnie a saját DNS-ét. Az ILB ASE-hez használt tartományutótag az ASE nevétől függ. A tartományutótag * &lt;AZ ASE&gt;neve .appserviceenvironment.net*. Az ILB IP-címe a portálon található az **IP-címek**alatt. 

A DNS konfigurálása:

- zóna létrehozása az * &lt;ASE&gt;névhez .appserviceenvironment.net*
- hozzon létre egy A rekordot abban a zónában, amely * az ILB IP-címére mutat
- hozzon létre egy A rekordot abban a zónában, amely a @ pontot az ILB IP-címére
- zóna létrehozása az * &lt;ASE&gt;névben .appserviceenvironment.net* nevű scm
- hozzon létre egy A rekordot az scm zónában, amely * az ILB IP-címére mutat

## <a name="publish-with-an-ilb-ase"></a>Közzététel ILB ASE környezetben

Minden létrehozott alkalmazásnak két végpontja van. Az ILB ASE-ben * &lt;alkalmazásneve&gt;van.&lt; &gt; ILB ASE-tartomány* és * &lt;alkalmazásnév&gt;.scm.&lt; ILB ASE-tartomány&gt;*. 

Az SCM helynév a Kudu konzolhoz irányítja, az Azure Portal **Speciális portál** részére. A Kudu konzol a környezeti változók megtekintését, a lemez vizsgálatát, a konzol használatát és még sok más funkciót kínál. További információ: [Kudu konzol az Azure App Service-ben][Kudu]. 

A GitHubhoz és az Azure DevOpshoz hasonló internetes alapú CI-rendszerek akkor is működnek az ILB ASE környezettel, ha a buildügynök elérhető az internetről, és ugyanazon a hálózaton található, mint az ILB ASE. Tehát az Azure DevOps esetében, ha a buildügynök ugyanazon a VNET-en lett létrehozva, mint az ILB ASE (lehet más alhálózat), képes lesz lekérni a kódot az Azure DevOps-gitről és telepíteni az ILB ASE-re. Ha nem szeretne létrehozni saját buildügynököt, egy lekérési modellt használó CI-rendszert kell használnia helyette, például a Dropboxot.

Az ILB ASE alkalmazásainak közzétételi végpontjai az ILB ASE létrehozásakor megadott tartományt használják. Ez a tartomány megjelenik az alkalmazás közzétételi profiljában és az alkalmazás portálpaneljén (**Overview** > **Essentials** és **Is Properties**). Ha rendelkezik egy ILB ASE a tartomány utótag * &lt;ASE neve&gt;.appserviceenvironment.net*, és egy app nevű *mytest*, használja *&lt; mytest. ASE&gt;neve .appserviceenvironment.net* FTP és *mytest.scm.contoso.net* a webes telepítéshez.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>ILB ASE konfigurálása WAF-eszközzel ##

A webalkalmazás-tűzfal (WAF) eszköz kombinálásával az ILB ASE csak a kívánt alkalmazásokat tegye elérhetővé az interneten, a többit pedig csak a virtuális hálózatból. Ez lehetővé teszi többek között biztonságos többrétegű alkalmazások készítését.

Ha többet szeretne tudni arról, hogyan konfigurálhatja az ILB ASE-t WAF-eszközzel, olvassa el a [Webalkalmazás tűzfalának konfigurálása az App Service-környezettel című témakört.][ASEWAF] Ez a cikk leírja, hogyan használhatja együtt a Barracuda virtuális berendezést az ASE környezettel. Másik lehetőségként az Azure Application Gateway is használható. Az Application Gateway az alapvető OWASP-szabályokat használja a mögötte elhelyezett alkalmazások biztonságossá tételére. Az Application Gatewayről további információt az [Azure webalkalmazási tűzfal bevezető][AppGW] cikke tartalmaz.

## <a name="ilb-ases-made-before-may-2019"></a>2019 májusa előtt gyártott ILB-as-ek

A 2019 májusa előtt létrehozott ILB-ase-k megkövetelik, hogy az ASE létrehozása során állítsa be a tartományutótagot. Azt is megkövetelték, hogy töltsön fel egy alapértelmezett tanúsítványt, amely az adott tartományutótagon alapult. Emellett egy régebbi ILB ASE nem hajthatja végre az egyszeri bejelentkezést a Kudu konzolra az adott ILB ASE-ben lévő alkalmazásokkal. Ha egy régebbi ILB ASE-hez konfigurálja a DNS-t, be kell állítania az A helyettesítő karakter rekordját egy olyan zónában, amely megfelel a tartomány utótagjának. 

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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
