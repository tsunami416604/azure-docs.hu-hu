---
title: Földrajzilag elosztott skála
description: Megtudhatja, hogyan méretezheti horizontálisan az alkalmazásokat Traffic Manager és App Service környezetekkel való geo-terjesztéssel.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 7ab04e23b838f2dfd39b73476db7492947d62e6e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688817"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Földrajzilag elosztott méretezés App Service Environment-környezetekkel
## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A nagyon nagy léptéket igénylő alkalmazási forgatókönyvek meghaladják az alkalmazás egyetlen központi telepítéséhez elérhető számítási erőforrások kapacitását.  A szavazati alkalmazások, a sportesemények és a televíziós szórakoztatás eseményei a rendkívül nagy léptéket igénylő forgatókönyvekre mutatnak. A nagy léptékű követelmények az alkalmazások horizontális felskálázásával hajthatók végre, több alkalmazás üzembe helyezése egyetlen régióban, valamint régiók között, a szélsőséges terhelési követelmények kezelése érdekében.

App Service környezetek ideális platform horizontális felskálázáshoz.  Ha olyan App Service Environment konfiguráció van kiválasztva, amely támogatja az ismert kérelmek arányát, a fejlesztők további App Service környezeteket helyezhetnek üzembe "cookie-vágó" módon a kívánt maximális terhelési kapacitás eléréséhez.

Tegyük fel például, hogy egy App Service Environment konfiguráción futó alkalmazást teszteltek a másodpercenkénti 20000 kérelmek kezelésére (RPS).  Ha a kívánt maximális terhelés 100 000 RPS, akkor öt (5) App Service környezet hozható létre és konfigurálható annak biztosítása érdekében, hogy az alkalmazás kezelni tudja a maximális tervezett terhelést.

Mivel az ügyfelek általában egyéni (vagy Vanity) tartománnyal férnek hozzá az alkalmazásokhoz, a fejlesztőknek az összes App Service Environment-példányon el kell osztaniuk az alkalmazásra vonatkozó kérelmeket.  Ennek az az előnye, hogy az egyéni tartományt az [Azure Traffic Manager-profil][AzureTrafficManagerProfile]használatával oldja fel.  A Traffic Manager profil úgy konfigurálható, hogy az összes egyéni App Service környezetben mutasson.  A Traffic Manager automatikusan kezeli az ügyfelek elosztását az összes App Service környezetben az Traffic Manager profil terheléselosztási beállításai alapján.  Ez a módszer attól függetlenül működik, hogy az összes App Service környezet egyetlen Azure-régióban van-e telepítve, vagy világszerte több Azure-régión belül van-e telepítve.

Emellett mivel az ügyfelek a hiúság tartományon keresztül érik el az alkalmazásokat, az ügyfelek nem ismerik az alkalmazást futtató App Service környezetek számát.  Ennek eredményeképpen a fejlesztők gyorsan és egyszerűen hozzáadhatnak és eltávolíthatnak App Service környezeteket a megfigyelt forgalmi terhelés alapján.

Az alábbi elméleti ábrán egy adott régión belül három App Service környezetben horizontálisan skálázható alkalmazás látható.

![Fogalmi architektúra][ConceptualArchitecture] 

A témakör további részében ismertetjük a több App Service környezet használatával elosztott topológia beállításának lépéseit.

## <a name="planning-the-topology"></a>A topológia megtervezése
Mielőtt kiépít egy elosztott alkalmazás-lábnyomot, a rendszer segít néhány adat megkezdése előtt.

* **Egyéni tartomány az alkalmazáshoz:**  Mi az az Egyéni tartománynév, amelyet az ügyfelek használni fognak az alkalmazás eléréséhez?  A minta alkalmazás esetében az Egyéni tartománynév `www.scalableasedemo.com`
* **Traffic Manager tartomány:**  [Azure Traffic Manager-profil][AzureTrafficManagerProfile]létrehozásakor ki kell választani a tartománynevet.  Ezt a nevet a rendszer a *trafficmanager.net* utótaggal kombinálva regisztrálja Traffic Manager által felügyelt tartományi bejegyzést.  A minta alkalmazás esetében a választott név a *skálázható – a bemutató*.  Ennek eredményeképpen a Traffic Manager által felügyelt teljes tartománynév *Scalable-ASE-demo.trafficmanager.net*.
* **Az alkalmazás helyigényének méretezésére szolgáló stratégia:**  Az alkalmazási lábnyom több App Service környezetbe kerül elosztásra egyetlen régióban?  Több régió?  Mindkét megközelítés kombinációja és megfeleltetése?  A döntésnek azon elvárások alapján kell megjelennie, amelyekkel az ügyfelek forgalmát, valamint azt, hogy az alkalmazás milyen mértékben képes a háttér-infrastruktúra támogatására.  Például egy 100%-os állapot nélküli alkalmazás esetében az alkalmazások nagy mértékben méretezhetők az Azure-régiók több App Service környezetének kombinációjával, és a több Azure-régióban üzembe helyezett App Service környezetek szorzatával.  A 15 és a nyilvános Azure-régiók közül választhatnak, így az ügyfelek valóban globális platformot építhetnek ki az alkalmazások számára.  A cikkben használt minta alkalmazáshoz három App Service környezet lett létrehozva egyetlen Azure-régióban (USA déli középső régiója).
* **A app Service környezetek elnevezési konvenciója:**  Minden App Service Environment egyedi nevet igényel.  Egy vagy két App Service környezeten túl hasznos elnevezési konvenció az egyes App Service Environmentok azonosításához.  A minta alkalmazáshoz egyszerű elnevezési konvenció volt használatban.  A három App Service környezet neve *fe1ase*, *fe2ase*és *fe3ase*.
* **Az alkalmazások elnevezési konvenciója:**  Mivel az alkalmazás több példánya is telepítve lesz, a központilag telepített alkalmazás minden példányához nevet kell megadni.  App Service környezetek egyik kevéssé ismert funkciója, hogy ugyanazt az alkalmazást több App Service környezetben is használhatja.  Mivel minden App Service Environment egyedi tartományi utótaggal rendelkezik, a fejlesztők úgy dönthetnek, hogy az egyes környezetekben ugyanazt az alkalmazást használják újra.  Előfordulhat például, hogy egy fejlesztőnek a következőképpen kell megneveznie az alkalmazásokat: *MyApp.Foo1.p.azurewebsites.net*, *MyApp.Foo2.p.azurewebsites.net*, *MyApp.Foo3.p.azurewebsites.net*stb.  A minta alkalmazás esetében, bár minden alkalmazás példánya egyedi névvel is rendelkezik.  Az *webfrontend1*, a *webfrontend2*és a *webfrontend3*használt alkalmazás-példányok nevei.

## <a name="setting-up-the-traffic-manager-profile"></a>A Traffic Manager profil beállítása
Ha egy alkalmazás több példánya is telepítve van több App Service környezetbe, az egyes alkalmazás-példányok regisztrálva lehetnek a Traffic Managerban.  A minta alkalmazáshoz Traffic Manager profil szükséges ahhoz, hogy a *Scalable-ASE-demo.trafficmanager.net* az ügyfeleket a következő telepített alkalmazás-példányok bármelyikéhez tudja irányítani:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Az első App Service Environment üzembe helyezett minta alkalmazás példánya.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  A második App Service Environment üzembe helyezett minta alkalmazás példánya.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  A harmadik App Service Environment üzembe helyezett minta alkalmazás példánya.

Több Azure App Service-végpont regisztrálásának legegyszerűbb módja, amely **ugyanabban** az Azure-régióban fut, a Powershell- [Azure Resource Manager Traffic Manager támogatással][ARMTrafficManager].  

Első lépésként létre kell hoznia egy Azure Traffic Manager-profilt.  Az alábbi kód azt mutatja be, hogyan jött létre a profil a minta alkalmazáshoz:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Figyelje meg, hogyan lett beállítva a *RelativeDnsName* paraméter skálázható-beadási *-bemutatóra*.  Így jön létre a *Scalable-ASE-demo.trafficmanager.net* tartománynév, és társítva van egy Traffic Manager profilhoz.

A *TrafficRoutingMethod* paraméter határozza meg a terheléselosztási házirendet, Traffic Manager a segítségével határozza meg, hogy a rendszer hogyan terjessze az ügyfelek terhelését az összes rendelkezésre álló végponton.  Ebben a példában a *súlyozott* metódus lett kiválasztva.  Ez azt eredményezi, hogy az ügyfelek kérései a regisztrált alkalmazási végpontok között oszlanak meg az egyes végpontokhoz társított relatív súlyok alapján. 

A létrehozott profillal a rendszer minden egyes alkalmazás-példányt natív Azure-végpontként ad hozzá a profilhoz.  Az alábbi kód beolvassa az egyes előtér-webalkalmazásokra mutató hivatkozást, majd az egyes alkalmazásokat Traffic Manager végpontként adja hozzá a *targetresourceid azonosítója* paraméterrel.

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Figyelje meg, hogy az egyes alkalmazás-példányok esetében van *-e AzureTrafficManagerEndpointConfig-* hívás.  Az egyes PowerShell-parancsok *targetresourceid azonosítója* paramétere a három telepített alkalmazás egyik példányára hivatkozik.  A Traffic Manager profil a profilban regisztrált mindhárom végpont terhelését fogja osztani.

Mindhárom végpont ugyanazt az értéket (10) használja a *súlyozási* paraméterhez.  Ez azt eredményezi, hogy Traffic Manager az ügyfelek kérelmeit az összes három alkalmazás-példány között viszonylag egyenletesen terjeszti. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Az alkalmazás egyéni tartományának mutatása a Traffic Manager tartományban
Az utolsó lépés ahhoz szükséges, hogy az alkalmazás egyéni tartományát a Traffic Manager tartományban mutasson.  A minta alkalmazás esetében ez azt jelenti, hogy a `scalable-ase-demo.trafficmanager.net``www.scalableasedemo.com` mutat.  Ezt a lépést az egyéni tartományt felügyelő tartományregisztráló használatával kell végrehajtani.  

A regisztrátor tartományi felügyeleti eszközeinek használatával létre kell hoznia egy CNAME rekordot, amely az Traffic Manager tartományban lévő egyéni tartományt mutat.  Az alábbi képen látható egy példa arra, hogy a CNAME konfiguráció hogyan néz ki:

![CNAME egyéni tartományhoz][CNAMEforCustomDomain] 

Bár ez a témakör nem foglalkozik, ne feledje, hogy minden egyes alkalmazás-példánynak rendelkeznie kell az egyéni tartománnyal is.  Ellenkező esetben, ha egy kérelem egy alkalmazás-példányra vonatkozik, és az alkalmazás nem rendelkezik az alkalmazásban regisztrált egyéni tartománnyal, a kérelem sikertelen lesz.  

Ebben a példában az egyéni tartomány `www.scalableasedemo.com`, és minden alkalmazás-példányhoz hozzá van rendelve egy egyéni tartomány.

![Egyéni tartomány][CustomDomain] 

Az egyéni tartományok Azure App Service alkalmazásokkal való regisztrálásának bekapcsolásához tekintse meg a következő cikket az [Egyéni tartományok regisztrálásáról][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Az elosztott topológia kipróbálása
A Traffic Manager és a DNS-konfiguráció végeredménye az, hogy a `www.scalableasedemo.com`-kérelmek a következő műveletsorral fognak folyni:

1. Egy böngésző vagy eszköz DNS-keresést végez a `www.scalableasedemo.com`
2. A tartományregisztrálónál lévő CNAME bejegyzés a DNS-címkeresés átirányítását okozza az Azure Traffic Manager.
3. A rendszer DNS-keresést végez az Azure Traffic Manager DNS-kiszolgálók egyikének *Scalable-ASE-demo.trafficmanager.net* .
4. A terheléselosztási házirend (a Traffic Manager profil létrehozásakor korábban használt *TrafficRoutingMethod* paraméter) alapján Traffic Manager kiválasztja az egyik konfigurált végpontot, és a VÉGPONT teljes tartománynevét visszaadja a böngészőnek vagy az eszköznek.
5. Mivel a végpont teljes tartományneve egy App Service Environment futó alkalmazás-példány URL-címe, a böngésző vagy az eszköz kérni fogja a Microsoft Azure DNS-kiszolgálót, hogy oldja fel a teljes tartománynevet az IP-címre. 
6. A böngésző vagy az eszköz elküldi a HTTP/S kérelmet az IP-címnek.  
7. A kérelem a App Service-környezetek egyikén futó alkalmazás-példányok egyikén fog megérkezni.

Az alábbi kép egy DNS-keresést mutat be a minta alkalmazás egyéni tartománya számára, amely a három minta App Service környezet egyikén futó alkalmazás-példányra való feloldást eredményezett (ebben az esetben a három App Service környezet második része):

![DNS-keresés][DNSLookup] 

## <a name="additional-links-and-information"></a>További hivatkozások és információk
A PowerShell-Azure Resource Manager dokumentációja [Traffic Manager-támogatás][ARMTrafficManager].  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
