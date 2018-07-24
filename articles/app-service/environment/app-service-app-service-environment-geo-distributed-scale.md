---
title: Földrajzi alapú méretezés App Service-környezetekkel
description: Ismerje meg, hogyan horizontális skálázása a Traffic Manager és az App Service Environment-környezetek a földrajzi elosztás használó alkalmazásokat.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.openlocfilehash: bc85139dfa3589baf6505fac2269f8755dcaddc8
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213248"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Földrajzi alapú méretezés App Service-környezetekkel
## <a name="overview"></a>Áttekintés
Rendkívül nagy skálázást igénylő alkalmazás-forgatókönyvek lépheti túl a számítási erőforrás-kapacitás üzembe helyezése egyetlen alkalmazás számára elérhető.  Szavazás alkalmazásokat, sportesemények és közvetített Szórakozás események példák összes rendkívül nagy méretezést igénylő forgatókönyvek. Vízszintes horizontális felskálázás alkalmazások esetében kerül sor egy adott régión belül, valamint a régióban, annak érdekében rendkívüli terhelést jelent, hogy több alkalmazást üzembe helyezés az nagy méretű követelmények is kielégíthetők.

App Service Environment-környezetek horizontális felskálázás ideális platformmá is.  Egyszer App Service-környezet konfigurációs beállítás ki van választva, hogy egy ismert kérelmek arányának támogatja, a fejlesztők telepíthet további App Service-környezetek "cookie-k vágó" el a kívánt maximális betöltési kapacitás elérése érdekében.

Tegyük fel például egy alkalmazást futtat az App Service Environment-környezet konfigurációjának kezeléséhez 20K kérelmek / másodperc (RPS) tesztelték.  Ha a kívánt maximális betöltési kapacitás 100 ezer RPS, majd öt (5) App Service-környezetek létrehozhatók és annak érdekében, hogy az alkalmazás kezelni tudja a maximális tervezett terhelés konfigurálva.

Ügyfelek általában egy egyéni (vagy személyes) tartományt használó alkalmazások eléréséhez, mivel a fejlesztők alkalmazás kérelmek szét az App Service Environment-környezet példányainak az összes olyan módon kell.  Remek lehetőséget ehhez az, hogy az egyéni tartomány feloldják egy [Azure Traffic Manager-profil][AzureTrafficManagerProfile].  A Traffic Manager-profil beállítható úgy, hogy az egyes App Service Environment-környezetek összes mutassanak.  A TRAFFIC Manager automatikusan fogja kezelni a definíciófrissítés-terjesztés ügyfelek összes az App Service-környezetek, a terheléselosztás a Traffic Manager-profil beállításai alapján.  A módszer jól használható, függetlenül attól, hogy az App Service Environment-környezetek összes egyetlen Azure-régióban üzembe helyezve, vagy világszerte több Azure-régióban üzembe helyezett.

Továbbá mivel az ügyfelek alkalmazások hozzáférnie a személyes tartomány, a felhasználókat, nélkül is működőképesek maradhatnak az App Service-környezetekben futó alkalmazások száma.  Ennek eredményeképpen a fejlesztők gyorsan és egyszerűen hozzáadhat, és eltávolíthat, App Service-környezetekben megfigyelt forgalmi terhelés alapján.

A fogalmi diagramja az alábbi vízszintesen horizontálisan felskálázott három App Service Environment-környezetek egy adott régión belül több alkalmazás mutatja be.

![Fogalmi architektúra][ConceptualArchitecture] 

Ez a témakör további részében a mintaalkalmazást, több App Service Environment-környezetek használatával az elosztott topológiákban beállításához lépéseit ismerteti.

## <a name="planning-the-topology"></a>A topológia tervezése
Egy elosztott alkalmazás üzembe helyezésének előkészítése kialakítására, mielőtt segít előre néhány információt az adatokat.

* **Az alkalmazás egyéni tartomány:** Mi az az egyéni tartománynév használó ügyfelek számára a hozzáférést az alkalmazáshoz?  A mintaalkalmazás az egyéni tartománynév van *www.scalableasedemo.com*
* **Traffic Manager-tartományra:** egy tartománynevet kell választani, amikor létrehozza az [Azure Traffic Manager-profil][AzureTrafficManagerProfile].  Ezt a nevet a rendszer kombinálja a *trafficmanager.net* regisztrálni egy tartományban bejegyzést a Traffic Manager által felügyelt utótag.  A mintaalkalmazás a név kiválasztott van *méretezhető ase bemutató*.  Ennek eredményeképpen a teljes tartománynévnek a Traffic Manager által felügyelt rendszer *méretezhető ase demo.trafficmanager.net*.
* **Az alkalmazás üzembe helyezésének előkészítése méretezési stratégia:** az alkalmazás erőforrás-igényű között szétosztani több App Service-környezetek egy adott régióban?  Több régióban?  A vegyes plaformspecifikus mindkét megközelítés?  A döntést kell alapulnia elvárásainak, ahonnan az ügyfél forgalom lesz származnak, valamint arról, hogy a többi egy alkalmazást támogató háttér-infrastruktúra is méretezhető.  Például egy 100 %-os állapot nélküli alkalmazással egy alkalmazást rugalmasan méretezhetők több App Service Environment-környezetek kombinációját használó Azure-régiónként, szorozva a több Azure-régióban üzembe helyezett App Service-környezetek.  A 15 + nyilvános Azure-régióban elérhető közül választhat ügyfelei valóban hozhat létre egy világméretű kapacitású alkalmazás üzembe helyezésének előkészítése.  Az ebben a cikkben használt mintaalkalmazás három App Service Environment-környezetek létrehozott egy Azure-régióban (USA déli középső Régiója).
* **Az App Service Environment-környezetek elnevezési:** minden App Service Environment-környezet megköveteli egy egyedi nevet.  Egy vagy két App Service Environment-környezetek túl hasznos van egy elnevezési konvenciója segítségével azonosíthatja az egyes App Service Environment-környezet.  A mintaalkalmazás egy egyszerű elnevezési konvenciót lett megadva.  A neve, a három App Service Environment-környezetek *fe1ase*, *fe2ase*, és *fe3ase*.
* **Az alkalmazások elnevezési:** az alkalmazás több példánya telepítve lesz, mivel egy nevet az üzembe helyezett alkalmazás minden példánya esetében van szükség.  App Service Environment-környezetek egy kis ismert, de igen kényelmes funkciója, hogy használható-e az alkalmazás névvel több App Service Environment-környezetek között.  Mivel minden App Service Environment-környezet rendelkezik egy egyedi tartományutótagot, a fejlesztők választhat újrafelhasználását pontos ugyanazon alkalmazás neve minden környezetben.  Egy fejlesztő például rendelkezhet elnevezése a következő alkalmazásokat: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*stb.  A mintaalkalmazás azonban minden alkalmazáspéldány is rendelkezik egy egyedi nevet.  A használt alkalmazás-példány neve *webfrontend1*, *webfrontend2*, és *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>A Traffic Manager-profil beállítása
Ha egy alkalmazás több példánya telepítve vannak, több App Service Environment-környezetek, az egyes alkalmazás-példányok regisztrálható a Traffic Managerrel.  A mintaalkalmazás egy Traffic Manager profil van szükség a *méretezhető ase demo.trafficmanager.net* ügyfelek, amelyek is irányíthatja a következő telepített alkalmazás-példányra:

* **webfrontend1.fe1ase.p.azurewebsites.NET:** a mintaalkalmazás az első App Service-környezetben telepített példányát.
* **webfrontend2.fe2ase.p.azurewebsites.NET:** a mintaalkalmazást, a második App Service-környezetben telepített példányát.
* **webfrontend3.fe3ase.p.azurewebsites.NET:** a mintaalkalmazást a harmadik App Service-környezetben telepített példányát.

Több Azure App Service végpontot, az összes futó regisztrálja a legegyszerűbb módja a **ugyanazon** az PowerShell-lel az Azure-régióban, [Azure Resource Manager Traffic Manager-támogatás] [ ARMTrafficManager].  

Az első lépés az Azure Traffic Manager-profilok létrehozásához.  Az alábbi kód bemutatja, hogyan jött létre a profilt a mintaalkalmazást:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Figyelje meg a *RelativeDnsName* paraméter beállítása *méretezhető ase bemutató*.  Ez a módját a tartománynév *méretezhető ase demo.trafficmanager.net* létrehozott és a egy Traffic Manager-profil társított.

A *TrafficRoutingMethod* paraméter határozza meg a terheléselosztási házirend Traffic Manager ügyfél terhelés elosztását az elérhető végpontok összes hogyan fogja használni.  Ebben a példában a *súlyozott* módszer választása.  Folyamatban van elosztva a regisztrált alkalmazás végpontok valamennyi végponthoz társított relatív súlya alapján ügyfelek kéréseire eredményez. 

A létrehozott profilt, az összes alkalmazáspéldány profilhoz lesz hozzáadva a natív Azure-végpontként.  Az alábbi kód lekéri az egyes előtér-webalkalmazást egy hivatkozást, majd hozzáadja a minden alkalmazás, egy Traffic Manager-végpont úton a *targetresourceid azonosítója* paraméter.

    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Figyelje meg, hogy nincs egyetlen hívásával *Add-AzureTrafficManagerEndpointConfig* minden egyes alkalmazás-példány esetében.  A *targetresourceid azonosítója* paraméter az egyes Powershell-parancs egy telepített alkalmazás három példányt hivatkozik.  A Traffic Manager-profil betöltése elosztva a profilban regisztrált összes három végpontot.

A három végpontok ugyanarra az értékre (10) használja a *súly* paraméter.  Ennek eredményeképpen a Traffic Manager terjesztésével ügyfelek kéréseire három alkalmazáspéldány között viszonylag egyenletes. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Az alkalmazás egyéni tartományának, a Traffic Manager-tartományra mutat.
Az utolsó lépés szükséges, hogy az egyéni tartomány az alkalmazás, a Traffic Manager-tartományra mutasson.  A mintaalkalmazás Ez azt jelenti, hogy mutat *www.scalableasedemo.com* , *méretezhető ase demo.trafficmanager.net*.  Ebben a lépésben kell elvégezni, amely felügyeli az egyéni tartomány a tartományregisztrálónál.  

A regisztráló tartományi felügyeleti eszközök használatával, egy olyan CNAME REKORDOT kell létrehozni, amely az egyéni tartományt, a Traffic Manager-tartományra mutat rögzíti.  A következő ábrán látható egy példa a CNAME-konfiguráció néz ki:

![Az egyéni tartományi CNAME][CNAMEforCustomDomain] 

Bár ez a témakör nem tárgyalja, ne feledje, hogy minden egyes alkalmazás-példány az egyéni tartományt is regisztrálni, hogy kell-e.  Ellenkező esetben egy kérelem teszi, hogy az alkalmazáspéldány, és az alkalmazás nem rendelkezik az egyéni tartomány az alkalmazás regisztrálva, a kérelem sikertelen lesz.  

Ebben a példában az egyéni tartomány van *www.scalableasedemo.com*, és mindegyik alkalmazáspéldány van társítva az egyéni tartomány.

![Egyéni tartomány][CustomDomain] 

Az egyéni tartomány regisztrálása az Azure App Service-alkalmazások röviden összefoglaljuk, tekintse meg a következő cikkben [regisztrálása egyéni tartományok][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Próbálja ki az Elosztott topológia
A végeredmény a Traffic Manager és a DNS-konfigurációs, amely a kérelmek *www.scalableasedemo.com* lesz továbbítva, a következő lépéseket:

1. Adott böngésző vagy eszköz egy DNS-címkeresés teszi *www.scalableasedemo.com*
2. A CNAME bejegyzést a tartomány-regisztrálónál, a rendszer átirányítja az Azure Traffic Manager DNS-címkeresés okoz.
3. Kérés érkezett egy DNS-címkeresés *méretezhető ase demo.trafficmanager.net* ellen az Azure Traffic Manager DNS-kiszolgálók egyikét.
4. A terheléselosztási házirend alapján (a *TrafficRoutingMethod* a Traffic Manager-profil létrehozásakor korábban használt paraméter), a Traffic Manager fog válasszon egyet a konfigurált végpontokra, és adja vissza, hogy a végpont a teljes Tartománynevét a böngésző vagy eszköz.
5. Mivel a teljes Tartománynevét, a végpont URL-címét az alkalmazáspéldány futó App Service-környezet, a böngésző vagy eszköz kérni fogja a Microsoft Azure DNS-kiszolgáló IP-cím feloldani a teljes Tartománynevet. 
6. A böngésző vagy eszköz a HTTP-vagy Https-kérést küld az IP-címet.  
7. A kérelem érkezik, az App Service Environment-környezetek egyik alkalmazás példányai közül.

A konzol a következő ábrán egy DNS-címkeresés a mintaalkalmazás egyéni tartomány sikeresen oldja fel a három minta App Service-környezetek (ebben az esetben a második, a három App Service Environment-környezetek) egyikén futó alkalmazás példányát:

![A DNS-címkeresés][DNSLookup] 

## <a name="additional-links-and-information"></a>További hivatkozások és információk
A PowerShell dokumentációs [Azure Resource Manager Traffic Manager-támogatás][ARMTrafficManager].  

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
