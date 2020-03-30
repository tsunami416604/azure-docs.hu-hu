---
title: Földrajzi elosztott skála
description: Ismerje meg, hogyan skálázható horizontálisan az alkalmazások földrajzi terjesztéssel a Traffic Manager és az App Service-környezetek használatával.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 7ab04e23b838f2dfd39b73476db7492947d62e6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688817"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Földrajzi alapú méretezés App Service-környezetekkel
## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Alkalmazási forgatókönyvek, amelyek nagyon nagy léptékű meghaladhatja a számítási erőforrás-kapacitás érhető el egy alkalmazás egyetlen központi telepítéséhez elérhető.  A szavazási alkalmazások, a sportesemények és a televíziós szórakoztató események mind olyan forgatókönyvek példái, amelyek rendkívül nagy léptékűek. A nagy léptékű követelmények az alkalmazások horizontális skálázásával teljesíthetők, és egy adott régióban, valamint a régiók között több alkalmazásközponti telepítés is lehetővé teszi a szélsőséges terhelési igények kezelését.

Az App Service-környezetek ideális platformot jelentenek a horizontális horizontális horizontális kiskálázáshoz.  Miután kiválasztott egy App Service-környezet konfigurációját, amely támogatja az ismert kérelmek arányát, a fejlesztők további App Service-környezeteket telepíthetnek "cookie cutter" módon a kívánt maximális terhelési kapacitás elérése érdekében.

Tegyük fel például, hogy egy App Service-környezet konfigurációján futó alkalmazást teszteltek 20K kérelmek másodpercenkénti (RPS) kezelésére.  Ha a kívánt maximális terhelési kapacitás 100K RPS, akkor öt (5) App Service-környezetek hozhatók létre és konfigurálhatók annak biztosítására, hogy az alkalmazás képes kezelni a maximális kivetített terhelést.

Mivel az ügyfelek általában egyéni (vagy hiú) tartomány használatával férnek hozzá az alkalmazásokhoz, a fejlesztőknek szükségük van egy módra az alkalmazáskérelmek elosztásához az Összes App Service-környezet példányban.  Ennek nagyszerű módja az egyéni tartomány feloldása [egy Azure Traffic Manager-profil][AzureTrafficManagerProfile]használatával.  A Traffic Manager-profil beállítható úgy, hogy az egyes alkalmazásszolgáltatási környezetekben mutasson.  A Traffic Manager automatikusan kezeli az ügyfelek elosztását az összes App Service-környezetben a Traffic Manager-profil terheléselosztási beállításai alapján.  Ez a megközelítés attól függetlenül működik, hogy az összes App Service-környezet egyetlen Azure-régióban van-e telepítve, vagy világszerte több Azure-régióban.

Továbbá, mivel az ügyfelek a hiúsági tartományon keresztül férnek hozzá az alkalmazásokhoz, az ügyfelek nincsenek tisztában az alkalmazást futtató App Service-környezetek számával.  Ennek eredményeképpen a fejlesztők gyorsan és egyszerűen hozzáadhatják és eltávolíthatják az App Service-környezeteket a megfigyelt forgalmi terhelés alapján.

Az alábbi koncepcionális diagram egy alkalmazást ábrázol, amely vízszintesen horizontálisan három App Service-környezetre skálázva egy adott régión belül.

![Koncepcionális architektúra][ConceptualArchitecture] 

A témakör további lépései a mintaalkalmazás több App Service-környezethasználatával történő beállításával kapcsolatos lépéseken.

## <a name="planning-the-topology"></a>A topológia megtervezése
Az elosztott alkalmazáslábnyom kiépítése előtt segít, ha néhány adatinformáció előre meglesz.

* **Egyéni tartomány az alkalmazáshoz:**  Mi az az egyéni tartománynév, amelyet az ügyfelek az alkalmazás eléréséhez használnak?  A mintaalkalmazás esetében az egyéni tartománynév`www.scalableasedemo.com`
* **Traffic Manager tartomány:**  [Az Azure Traffic Manager-profil][AzureTrafficManagerProfile]létrehozásakor tartománynevet kell választani.  Ez a név a *trafficmanager.net* utótaggal kombinálva regisztrálegy, a Traffic Manager által kezelt tartománybejegyzést.  A mintaalkalmazás esetében a választott név *méretezhető-ase-demo.*  Ennek eredményeképpen a Traffic Manager által kezelt teljes tartománynév *scalable-ase-demo.trafficmanager.net*.
* **Stratégia az alkalmazás lábnyomának méretezésére:**  Az alkalmazás alapigénye egy adott régióban több App Service-környezetközött lesz elosztva?  Több régió?  A mix-and-match mindkét megközelítés?  A döntésnek az ügyfélforgalom származási helyével kapcsolatos elvárásokon kell alapulnia, valamint azon, hogy az alkalmazás támogató háttérinfrastruktúrájának többi része mennyire méretezhető.  Egy 100%-ban állapotmentes alkalmazással például egy alkalmazás nagy mértékben skálázható az Azure-régiónként több App Service-környezet kombinációjával, megszorozva a több Azure-régióban telepített App Service-környezetekkel.  A több mint 15 nyilvános Azure-régióval az ügyfelek valóban létrehozhatnak egy világméretű, nagy léptékű alkalmazáslábnyomot.  A cikkhez használt mintaalkalmazáshoz három App Service-környezet jött létre egyetlen Azure-régióban (USA déli középső régiójában).
* **Az App Service-környezetek elnevezési konvenciója:**  Minden App Service-környezethez egyedi név szükséges.  Egy vagy két App Service-környezeten túl hasznos, ha rendelkezik egy elnevezési konvencióval az egyes App Service-környezetek azonosításához.  A mintaalkalmazáshoz egy egyszerű elnevezési konvenciót használt.  A három App Service-környezet *neve: fe1ase*, *fe2ase*és *fe3ase*.
* **Az alkalmazások elnevezési konvenciója:**  Mivel az alkalmazás több példánya lesz telepítve, a telepített alkalmazás minden egyes példányához név szükséges.  Az App Service-környezetek egyik kevéssé ismert, de nagyon kényelmes funkciója, hogy ugyanaz az alkalmazásnév több App Service-környezetben is használható.  Mivel minden Egyes App Service-környezet egyedi tartományutótaggal rendelkezik, a fejlesztők dönthetnek úgy, hogy minden környezetben újra ugyanazt az alkalmazásnevet használják.  Például egy fejlesztő a következő képpen nevezheti el az alkalmazásokat: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*stb.  A mintaalkalmazáshoz, bár minden alkalmazáspéldánynak egyedi neve is van.  A használt alkalmazáspéldánynevek a *következők: webfrontend1*, *webfrontend2*és *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>A Traffic Manager-profil beállítása
Ha egy alkalmazás több példányát több App Service-környezetben telepíti, az egyes alkalmazáspéldányok regisztrálhatók a Traffic Manager.  A mintaalkalmazáshoz egy Traffic Manager-profilra van szükség *scalable-ase-demo.trafficmanager.net,* amely az ügyfeleket az alábbi telepített alkalmazáspéldányok bármelyikére irányíthatja:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Az első App Service-környezetben telepített mintaalkalmazás példánya.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  A mintaalkalmazás egy példánya a második App Service-környezetben üzembe helyezve.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  A mintaalkalmazás példánya a harmadik App Service-környezetben üzembe helyezve.

A [powershell-i Azure Resource Manager Traffic Manager-támogatással][ARMTrafficManager]a legegyszerűbben több Azure App Service-végpont regisztrálásának legegyszerűbb módja, amelyek **mindegyike ugyanabban** az Azure-régióban fut.  

Az első lépés egy Azure Traffic Manager-profil létrehozása.  Az alábbi kód bemutatja, hogyan jött létre a profil a mintaalkalmazáshoz:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Figyelje meg, hogyan lett a *RelativeDnsName* paraméter *méretezhető-ase-demo értékre*lett állítva.  Így jön létre a scalable-ase-demo.trafficmanager.net *tartománynév,* és hogyan társítva van egy Traffic Manager-profilhoz.

A *TrafficRoutingMethod* paraméter határozza meg a terheléselosztási házirend Traffic Manager fogja használni, hogyan kell elosztani az ügyfél terhelésaz összes rendelkezésre álló végpontok között.  Ebben a példában a *Súlyozott* módszer lett kiválasztva.  Ez azt eredményezi, hogy az ügyfélkérelmek az egyes végpontokhoz társított relatív súlyok alapján elosztva jelennek meg az összes regisztrált alkalmazásvégpontközött. 

A profil létrehozása, minden egyes alkalmazáspéldány hozzáadódik a profilnatív Azure-végpontként.  Az alábbi kód lekéri az egyes előtér-webalkalmazásokra mutató hivatkozást, majd a *TargetResourceId* paraméteren keresztül hozzáadja az egyes alkalmazásokat Traffic Manager-végpontként.

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Figyelje meg, hogyan van egy hívás *az Add-AzureTrafficManagerEndpointConfig* minden egyes alkalmazáspéldányhoz.  A *TargetResourceId* paraméter minden Powershell-parancsban a három telepített alkalmazáspéldány egyikére hivatkozik.  A Traffic Manager-profil a terhelést a profilban regisztrált mindhárom végpontra osztja el.

Mindhárom végpont ugyanazt az értéket (10) használja a *Súly* paraméterhez.  Ennek eredményeként a Traffic Manager viszonylag egyenletesen osztja el az ügyfélkérelmeket mindhárom alkalmazáspéldányközött. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Az alkalmazás egyéni tartományának rámutatása a Forgalomkezelő tartományban
Az utolsó szükséges lépés az, hogy pont az egyéni tartomány az alkalmazás a Traffic Manager tartományban.  A mintaalkalmazás esetében `www.scalableasedemo.com` `scalable-ase-demo.trafficmanager.net`ez azt jelenti, hogy a-ra mutat.  Ezt a lépést az egyéni tartományt kezelő tartományregisztrálóval kell elvégezni.  

A regisztráló tartománykezelő eszközeivel létre kell hozni egy CNAME rekordokat, amelyek az egyéni tartományt a Forgalomkezelő tartományban rögzítik.  Az alábbi képen egy példa látható arra, hogy hogyan néz ki ez a CNAME konfiguráció:

![CNAME egyéni tartományhoz][CNAMEforCustomDomain] 

Bár ez a témakör nem foglalkozik, ne feledje, hogy minden egyes alkalmazáspéldánynak az egyéni tartomány regisztrálásához is rendelkeznie kell.  Ellenkező esetben, ha egy kérelem egy alkalmazáspéldányhoz érkezik, és az alkalmazás nem rendelkezik az egyéni tartomány regisztrálva az alkalmazással, a kérés sikertelen lesz.  

Ebben a példában `www.scalableasedemo.com`az egyéni tartomány , és minden alkalmazáspéldányhoz tartozik az egyéni tartomány.

![Egyéni tartomány][CustomDomain] 

Az egyéni tartományok Azure App Service-alkalmazásokkal való regisztrálásának újrabedugnia az [egyéni tartományok regisztrálásáról][RegisterCustomDomain]szóló alábbi cikkben olvashat.

## <a name="trying-out-the-distributed-topology"></a>Az elosztott topológia kipróbálása
A Traffic Manager és a DNS-konfiguráció `www.scalableasedemo.com` végeredménye az, hogy a kérelmek a következő sorrendben haladnak:

1. A böngésző vagy eszköz DNS-keresőt`www.scalableasedemo.com`
2. A tartományregisztráló CNAME bejegyzése hatására a DNS-keresése átlesz irányítva az Azure Traffic Managerbe.
3. A DNS-keresése az *Azure* Traffic Manager egyik DNS-kiszolgálója scalable-ase-demo.trafficmanager.net.
4. A terheléselosztási házirend (a TrafficManager-profil létrehozásakor korábban használt *TrafficRoutingMethod* paraméter) alapján a Traffic Manager kiválasztja a konfigurált végpontok egyikét, és visszaadja a végpont teljes tartományszámát a böngészőnek vagy az eszköznek.
5. Mivel a végpont teljes tartományneve egy App Service-környezetben futó alkalmazáspéldány URL-címe, a böngésző vagy az eszköz megfogja kérni a Microsoft Azure DNS-kiszolgálót, hogy oldja fel a teljes tartománynnát egy IP-címre. 
6. A böngésző vagy az eszköz elküldi a HTTP/S-kérelmet az IP-címre.  
7. A kérelem az egyik app-példányhoz érkezik, amely az App Service-környezetek egyikén fut.

Az alábbi konzolkép egy DNS-keresőt jelenít meg a mintaalkalmazás egyéni tartományához, amely sikeresen feloldva egy alkalmazáspéldányra, amely a három minta App Service-környezet (ebben az esetben a három App Service-környezet közül a második):

![DNS-keresése][DNSLookup] 

## <a name="additional-links-and-information"></a>További linkek és információk
Dokumentáció a Powershell [Azure Resource Manager Traffic Manager támogatás.][ARMTrafficManager]  

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
