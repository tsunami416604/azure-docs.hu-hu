---
title: "Létrehozhat és használhat belső terheléselosztót az Azure App Service-környezet"
description: "Megtudhatja, hogyan létrehozása és egy internet-egymástól el vannak különítve Azure App Service-környezet használata"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: cc7bdd7860506c20187dc913b72111824d1737ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Létrehozhat és használhat belső terheléselosztót az App Service-környezet #

 Az Azure App Service Environment-környezet az Azure App Service egy Azure virtuális hálózatot (VNet) lévő alhálózatot történő központi telepítését. Két módon telepítheti az App Service-környezetek (ASE): 

- A virtuális IP-címre a külső IP-cím, egy külső ASE gyakran nevezik.
- A virtuális IP-címre a belső IP-címet gyakran nevezik egy ILB ASE, mert a belső végpont nem egy belső terheléselosztón (ILB). 

Ez a cikk bemutatja, hogyan hozzon létre egy ILB ASE. Az a ASE az áttekintést lásd: [Bevezetés az App Service-környezetek][Intro]. Egy külső ASE létrehozásával kapcsolatban lásd: [hozzon létre egy külső ASE][MakeExternalASE].

## <a name="overview"></a>Áttekintés ##

A virtuális hálózat telepítheti egy ASE az internetről elérhető végpontok vagy IP-címet. Az IP-címének beállítása a virtuális hálózat címre, a ASE kell telepíteni az egy Példánynak. Ha az egy ILB a ASE telepít, meg kell adnia:

-   A saját tartomány használni, amikor a alkalmazásai létrehozására.
-   A HTTPS-hez használt tanúsítvány.
-   A tartomány DNS-kezelés.

Ismét műveleteket végezheti el, mint:

-   Intranetes alkalmazások üzemeltetését biztonságosan a felhőben, amelyek egy-webhelyek vagy Azure ExpressRoute VPN keresztül érhető el.
-   Állomás alkalmazások a felhőben a nyilvános DNS-kiszolgálók nem jelennek meg.
-   Hozzon létre internet elszigetelt háttér-alkalmazásokat, az előtér-alkalmazások biztonságosan integrálható.

### <a name="disabled-functionality"></a>Letiltott funkció ###

Néhány dolgot, amely egy ILB ASE használatakor nem hajtható végre:

-   IP-alapú SSL használatára.
-   IP-címek hozzárendelése a megadott alkalmazások.
-   Vásároljon, és egy tanúsítványt használni az alkalmazások az Azure portálon keresztül. Tanúsítványok beszerzése hitelesítésszolgáltatótól származó közvetlenül, és használhatja azokat az alkalmazásokat. Nem lehet beszerezni azokat, az Azure portálon keresztül.

## <a name="create-an-ilb-ase"></a>Hozzon létre egy ILB ASE ##

Egy ILB ASE létrehozása:

1. Válassza ki az Azure-portálon **új** > **Web + mobil** > **App Service Environment-környezet**.

2. Válassza ki előfizetését.

3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.

4. Válassza ki, vagy hozzon létre egy Vnetet.

5. Ha egy meglévő virtuális hálózatot választ ki, hozzon létre egy alhálózatot a ASE tárolásához szeretné. Feltétlenül állítson be egy alhálózati méretének elég nagy legyen a ASE jövőbeli növekedésének. Azt javasoljuk, hogy a méretet `/25`, amely 128-címekkel rendelkezik, és kezelni tud a maximális méretű ASE. A minimális méret, kiválaszthatja a `/28`. Után szükség van az infrastruktúra, ez a méret legfeljebb 11-példányok is méretezhető.

    * Keresse meg az alapértelmezett legfeljebb 100 példányok túl az App Service-csomagokról.

    * Méretezhető, 100 közelében, de gyorsabb előtér-méretezés.

6. Válassza ki **virtuális hálózati/hely** > **virtuális hálózati konfiguráció**. Állítsa be a **VIP típus** való **belső**.

7. Adja meg a tartomány nevét. Ebben a tartományban, a egy, a ASE létrehozott alkalmazások használt. Nincsenek bizonyos korlátozások vonatkoznak. Nem lehet:

    * nettó   

    * azurewebsites.NET

    * p.azurewebsites.NET

    * &lt;asename környezet&gt;. p.azurewebsites.net

   Az egyéni tartománynév az alkalmazások és a tartománynév, a ASE által használt nem lehetnek átfedésben. Egy ILB ASE a tartomány nevét a _contoso.com_, nem használható egyéni tartománynevek az alkalmazásokhoz, például:

    * www.contoso.com

    * ABCD.def.contoso.com

    * ABCD.contoso.com

   Ha ismeri az alkalmazások egyéni tartománynevek, válassza ki a tartományt a a ILB ASE, amelyek nem rendelkeznek ezen egyéni tartománynevekkel ütközés. Ebben a példában, használhatja a következőhöz hasonlóan *contoso-internal.com* a tartomány a hajlamosnak mert, amelyek nem ütköznek egyéni tartománynevek végződő *. contoso.com*.

8. Válassza ki **OK**, majd válassza ki **létrehozása**.

    ![ASE létrehozása][1]

Az a **virtuális hálózati** panelen van egy **virtuális hálózati konfiguráció** lehetőséget. Egy külső VIP vagy egy belső VIP kiválasztásához használható. Az alapértelmezett érték **külső**. Ha **külső**, a ASE egy internetről elérhető VIP használja. Ha **belső**, a ASE egy ILB a Vneten belül egy IP-cím van konfigurálva.

Miután kiválasztotta a **belső**, a rendszer eltávolítja több IP-címek hozzáadása a ASE lehetőséget. Ehelyett meg kell adnia a ASE tartományát. Service-környezetben egy külső virtuális IP-címre a ASE nevét, hogy ASE létrehozott alkalmazások szolgál a tartományban.

Ha **VIP típus** való **belső**, a ASE neve szolgál a tartomány nem a ASE. Explicit módon adja meg a tartományt. Ha a tartomány *contoso.corp.net* és abban a ASE nevű alkalmazást hoz létre *timereporting*, az alkalmazás URL-je timereporting.contoso.corp.net.


## <a name="create-an-app-in-an-ilb-ase"></a>Hozzon létre egy alkalmazást-Példánynak környezetben ##

Létrehozott egy app Service-környezetben általában ugyanúgy-Példánynak környezetben létrehoz egy alkalmazást.

1. Válassza ki az Azure-portálon **új** > **Web + mobil** > **webes** vagy **Mobile** vagy **API Alkalmazás**.

2. Adja meg az alkalmazás nevét.

3. Válassza ki az előfizetést.

4. Válasszon ki vagy hozzon létre egy erőforráscsoportot.

5. Válassza ki, vagy hozzon létre egy App Service-csomag. Ha szeretne létrehozni egy új App Service-csomagot, jelölje ki a ASE helyeként. Válassza ki az App Service-csomag létrehozni kívánt munkavégző készletét. Az App Service-csomag létrehozásakor válassza ki a ASE helyét és a munkavégző készletét. Ha az alkalmazás nevét adja meg, az alkalmazás neve alatt a tartomány lép a tartomány által a ASE.

6. Kattintson a **Létrehozás** gombra. Ha azt szeretné, hogy az alkalmazás megjelenik az irányítópulton, válassza a **rögzítés az irányítópulton** jelölőnégyzetet.

    ![App Service-csomag létrehozása][2]

    A **alkalmazásnév**, a tartomány nevét a tartomány a hajlamosnak megfelelően frissül.

## <a name="post-ilb-ase-creation-validation"></a>POST-Példánynak ASE létrehozásának ellenőrzése ##

Egy ILB ASE mint a nem - Példánynak ASE némileg eltérő. Már beállításértékeket, mint a saját DNS-kiszolgáló kezelése kell. Meg kell adnia a saját tanúsítvány HTTPS-kapcsolatoknál is.

Miután létrehozta a ASE, a tartomány nevét a megadott tartomány jeleníti meg. Új elem megjelenik a **beállítás** nevű menü **ILB tanúsítvány**. A ASE olyan tanúsítvány, amely nem adja meg a ILB ASE tartományt hozza létre. A ASE tanúsítványt használja, ha a böngésző megtudhatja, hogy érvénytelen. Ez a tanúsítvány megkönnyíti a HTTPS tesztelése, de kell töltse fel a saját tanúsítványban ILB ASE tartományához van kötve. Ez a lépés nem szükséges, függetlenül attól, hogy a tanúsítvány önaláírt vagy a hitelesítésszolgáltatótól beszerzett.

![ILB ASE tartománynév][3]

A ILB ASE érvényes SSL-tanúsítvány szükséges. Belső hitelesítésszolgáltatók használja, vásárolhat egy tanúsítványt egy külső kibocsátó, vagy használhat önaláírt tanúsítványt. Az SSL-tanúsítvány forrását, függetlenül is megfelelően konfigurálni kell a következő tanúsítvány attribútumok:

* **Tulajdonos**: Ez az attribútum *.your-gyökér-tartományi-itt értékre kell állítani.
* **Tulajdonos alternatív neve**: ennek az attribútumnak kell tartalmaznia a **.your-gyökér-tartományi-Itt* és **.scm.your-gyökér-tartományi-Itt*. Minden alkalmazáshoz társított SCM/Kudu webhely SSL-kapcsolatot az űrlap cím használata *your-app-name.scm.your-root-domain-here*.

A konvertálás/mentése az SSL-tanúsítvány egy .pfx fájlba. A .pfx fájl tartalmazza az összes köztes kell, és a legfelső szintű tanúsítványok. A biztonság jelszóval.

Ha szeretne létrehozni egy önaláírt tanúsítványt, a PowerShell-parancsok itt is használhatja. A ILB ASE tartománynév helyett használjon *internal.contoso.com*: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

A tanúsítvány, amely PowerShell-parancsokkal generál böngészők által megjelölt, mert a tanúsítványt a hitelesítésszolgáltatótól, amely megbízhatósági lánc a böngésző nem hozta létre. Ahhoz, hogy egy tanúsítványt, amely megbízik a böngésző, be kell szereznie, a böngésző lánc megbízhatósági kereskedelmi hitelesítésszolgáltatótól származó. 

![Állítsa be a ILB tanúsítványt][4]

Töltse fel a saját tanúsítványait, és-hozzáférés tesztelése:

1. A ASE létrehozása után nyissa meg a ASE felhasználói felületén. Válassza ki **ASE** > **beállítások** > **ILB tanúsítvány**.

2. Állítsa be a ILB tanúsítványt, válassza ki a tanúsítvány .pfx fájlt, és adja meg a jelszót. Ez a lépés bizonyos idő feldolgozni. Egy üzenet jelenik meg, amely meghatározza, hogy, hogy folyamatban van egy feltöltési művelet.

3. A ILB cím lekérése a ASE. Válassza ki **ASE** > **tulajdonságok** > **virtuális IP-cím**.

4. Webalkalmazás létrehozása a ASE a ASE létrehozása után.

5. Virtuális gép létrehozása, ha még nincs fiókja, hogy a Vneten belül.

    > [!NOTE] 
    > Ne kísérelje meg a virtuális gép ugyanazon az alhálózaton, mint a ASE hozható létre, mert sikertelen, vagy problémákhoz.
    >
    >

6. Állítsa be a DNS a ASE tartomány. A tartomány a DNS-ben helyettesítő karakter használható. Ehhez az egyszerű tesztek, VIP IP-címre a webalkalmazás nevének beállítása a virtuális gépen a hosts fájl szerkesztése:

    a. Ha a ASE tartomány neve _. ilbase.com_ hoz létre, hogy a webes alkalmazás neve és _mytestapp_, a címzett _mytestapp.ilbase.com_. Ezután _mytestapp.ilbase.com_ a ILB címhez. (A Windows, a hosts fájl jelenleg _C:\Windows\System32\drivers\etc\_.)

    b. Webes telepítés közzététel vagy a speciális konzoljához való hozzáférés tesztelése, hozzon létre egy rekordot _mytestapp.scm.ilbase.com_.

7. Használja ezt a virtuális Gépet egy böngészőt, és navigáljon a http://mytestapp.ilbase.com. (Vagy bármilyen a webes alkalmazás neve a tartomány van.)

8. Használja ezt a virtuális Gépet egy böngészőt, és navigáljon a https://mytestapp.ilbase.com. Ha egy önaláírt tanúsítványt használ, fogadja el a biztonsági hiánya.

    Az IP-címet a ILB megtalálható-e **IP-címek**. Ez a lista is rendelkezik a külső VIP és bejövő felügyeleti adatforgalomhoz használt IP-címét.

    ![ILB IP-cím][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>Webes feladatok, funkciók és a ILB ASE ##

Egy ILB ASE funkciók és a webes feladatok is támogatottak, de azokat a portálhoz, az SCM helyre hálózati hozzáféréssel kell rendelkeznie.  Ez azt jelenti, hogy a böngésző vagy olyan gazdagépre, amely a áll, vagy a virtuális hálózathoz kell lennie.  

Ha az Azure Functions egy ILB ASE használja, akkor előfordulhat, hogy hiba jelenik, amely szerint a "jelenleg nem tudjuk a funkciók most beolvasása. Próbálkozzon újra később." Ez a hiba akkor fordul elő, mert a függvények felhasználói felület kihasználja az SCM hely HTTPS-KAPCSOLATON keresztül, és a legfelső szintű tanúsítvány nem a böngésző lánc megbízhatósági. Webes feladatok hasonló problémát észlelt. A probléma elkerülése érdekében tegye a következők egyikét:

- A tanúsítvány felvétele a megbízható tanúsítványok tárolójába. Ez feloldja széle és az Internet Explorerben.
- Chrome használja, és látogasson el az SCM először, fogadja el a nem megbízható tanúsítvány, és folytassa a a portálon.
- A böngésző megbízhatósági lánc a kereskedelmi tanúsítványt használjon.  Ez a lehetőség ajánlott.  

## <a name="dns-configuration"></a>DNS-konfiguráció ##

Ha egy külső VIP használja, a DNS Azure kezeli. Bármely alkalmazás a ASE létre automatikusan az Azure DNS-Ez egy nyilvános DNS-ben. A saját DNS-Példánynak környezetben kell kezelni. Például egy adott tartomány _contoso.net_, kell létrehoznia a DNS A rekordokat a a ILB címet mutató DNS-ben:

- *. contoso.net
- *. scm.contoso.net

A ILB ASE tartomány kívül a ASE több dolgot használata esetén szükség lehet a DNS kezelése /-alkalmazás-neve alapján. Ez a módszer van kihívást, mert később szüksége lesz a DNS-kiszolgáló vegyen fel minden új alkalmazásnév létrehozásakor. Ezért azt javasoljuk, hogy dedikált tartományt használ.

## <a name="publish-with-an-ilb-ase"></a>Egy ILB mértékéig közzététele ##

Minden alkalmazást, amely jön létre nincsenek két végpontot. -Példánynak környezetben van  *&lt;alkalmazás neve >.&lt; ILB ASE tartományi >* és  *&lt;alkalmazás neve > .scm.&lt; ILB ASE tartományi >*. 

Az SCM helynév megnyitná a Kudu konzol hívása a **speciális portal**, az Azure portálon. A Kudu konzol segítségével megtekintheti a környezeti változók, megtekinti a lemez, használja a konzolon, és még sok más. További információkért lásd: [Kudu konzol az Azure App Service-][Kudu]. 

A több-bérlős App Service és a külső Service-környezetben van az egyszeri bejelentkezés az Azure-portál és a Kudu konzol között. A ILB ASE azonban meg kell a közzétételi hitelesítő adatai segítségével bejelentkezhetnek a Kudu konzolba.

Internetes CI rendszereknek, például a Githubon és a Visual Studio Team Services, egy ILB mértékéig nem működnek, mert a közzétételi végpont nem érhető el az internet. Ehelyett kell használnia, amely lekéréses modellt használ, például a Dropbox CI rendszer.

A közzétételi-Példánynak környezetben alkalmazások végpontjainak használja a tartományban, amelyhez a Példánynak ASE hozták létre. Ez a tartomány megjelenik az alkalmazás közzétételi profil és az alkalmazás portálpaneljéhez (**áttekintése** > **Essentials** és is **tulajdonságok**). Ha rendelkezik egy, a altartomány ILB ASE *contoso.net* és az alkalmazás neve *mytest*, használjon *mytest.contoso.net* az FTP és *mytest.scm.contoso.net* központi telepítésére.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>Egy ILB ASE gömbcsatlakozók WAF-eszközzel rendelkező ##

Az Azure App Service számos biztonsági intézkedéseket, hogy megóvja a rendszert biztosít. Pedig segít meghatározni, hogy megtámadott lett-e az alkalmazást. A legjobb védelmet webalkalmazás arra üzemeltetési platformtól, például az Azure App Service-ben, a webes alkalmazás tűzfalat (waf-ot). Mivel a ILB ASE egy elszigetelt hálózati alkalmazás végponttal rendelkezik, célszerű a használatra.

A ILB ASE WAF-eszközzel rendelkező konfigurálásával kapcsolatos további tudnivalókért lásd: [webalkalmazási tűzfal konfigurálása az App Service-környezet][ASEWAF]. Ez a cikk bemutatja, hogyan használható a Barracuda virtuális készülék a mértékéig. Egy másik lehetőség, hogy Azure Application Gateway használja. Alkalmazásátjáró OWASP core szabályok használja a biztonságos olyan alkalmazást, akkor mögé. Alkalmazásátjáró kapcsolatos további információkért lásd: [bemutatása az Azure webalkalmazási tűzfal][AppGW].

## <a name="get-started"></a>Bevezetés ##

* ASEs megkezdéséhez, lásd: [Bevezetés az App Service-környezetek][Intro].
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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
