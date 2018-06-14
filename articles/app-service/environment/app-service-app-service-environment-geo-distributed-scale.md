---
title: Földrajzi alapú méretezés App Service-környezetekkel
description: Megtudhatja, hogyan vízszintesen skálázása a Traffic Manager és az App Service Environment-környezetek földrajzi terjesztési használó alkalmazásokat.
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
ms.openlocfilehash: 21f747239e565aba79a84c8e946a71e306b64968
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836832"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Földrajzi alapú méretezés App Service-környezetekkel
## <a name="overview"></a>Áttekintés
Nagyon nagy méretű igénylő alkalmazás-forgatókönyvekre lépheti túl a számítási erőforrás-kapacitás egy központi telepítésnél, egy alkalmazás számára elérhető.  Szavazás alkalmazások, ilyen események és közvetített Szórakozás események példák minden forgatókönyv esetén van szükség a rendkívül nagy méretű. Nagy méretű követelmények által vízszintesen kiterjesztése alkalmazások esetén végrehajtott egyetlen régión belül, valamint a különböző régiókban, rendkívül Adatbetöltési követelményeinek kezeléséhez több alkalmazás központi telepítéssel rendelkező érheti el.

App Service-környezetek vízszintes kibővítési ideális platform.  Egyszer egy App Service-környezet konfigurációs van beállítva, amely képes támogatni az olyan ismert lekérdezési gyakorisága, fejlesztők telepíthet további App Service Environment-környezetek "cookie-k vágó" módon a kívánt csúcs terhelés kapacitás elérése érdekében.

Tegyük fel például egy alkalmazást az App Service Environment-környezet konfigurációjának futó tesztelték 20K kérelmek / másodperc (RPS) kezelésére.  Ha a kívánt csúcs terhelés kapacitás 100K RPS, majd öt (5) App Service-környezetek hozható létre és beállítani az alkalmazás kezeli a maximális tervezett terhelés biztosítása érdekében.

Ügyfelek általában egy egyéni (vagy személyes) tartományt használó alkalmazások hozzáférhetnek, mivel a fejlesztők szükségük van egy módszerre, alkalmazás kérelmek szét minden App Service Environment-környezet előfordulása.  Az egyéni tartomány használatával megoldásához kiváló módja annak, hogy ehhez egy [Azure Traffic Manager-profil][AzureTrafficManagerProfile].  A Traffic Manager-profil beállítható úgy, hogy az egyes App Service Environment-környezetek összes helyen.  A TRAFFIC Manager automatikusan fogja kezelni a terjesztés az ügyfelek összes az App Service-környezetek, a terheléselosztás a Traffic Manager-profil beállításai alapján.  Ez a megközelítés működik, függetlenül attól, hogy összes az App Service Environment-környezetek telepítése egy Azure-régió, vagy több Azure-régiók közötti világszerte telepített.

Ezenkívül ügyfelek alkalmazások hozzáférhetnek a személyes tartomány keresztül, mert az ügyfelek nem tudnak a futó alkalmazások App Service-környezetek száma.  Ennek eredményeképpen a fejlesztők is gyorsan és egyszerűen hozzáadása és eltávolítása, App Service Environment-környezetek megfigyelt adatforgalom jelentette teher alapján.

A fogalmi az alábbi ábrán az alkalmazások között három App Service Environment-környezetek egyetlen régión belül vízszintesen horizontálisan ábrázol.

![Fogalmi architektúra][ConceptualArchitecture] 

Ez a témakör további része végigvezeti egy elosztott topológia több App Service Environment-környezetek használatával mintaalkalmazás beállításához lépéseit.

## <a name="planning-the-topology"></a>A topológia tervezése
Mielőtt egy elosztott alkalmazás erőforrásigényét létrehozására, van néhány adatra információk időben segíti.

* **Az alkalmazáshoz tartozó egyéni tartomány:** Mi az, hogy az egyéni tartománynév használó ügyfelek az alkalmazás eléréséhez?  A mintaalkalmazás az egyéni tartománynév megadása *www.scalableasedemo.com*
* **Traffic Manager-tartományra:** egy tartománynevet kell létrehozásakor kell választani egy [Azure Traffic Manager-profil][AzureTrafficManagerProfile].  Ez a név a rendszer kombinálja a *trafficmanager.net* jelző utótaghoz, így a Traffic Manager által kezelt tartomány bejegyzést.  A mintaalkalmazás, a név választott van *méretezhető ase bemutató*.  Ennek eredményeképpen a teljes tartománynevet a Traffic Manager által kezelt rendszer *méretezhető ase demo.trafficmanager.net*.
* **Stratégiájának a app kezdjen méretezéshez:** lesz a alkalmazás kezdjen szétosztását több App Service Environment-környezetek egy régió között?  Több régióba?  Vegyes-és-egyezés a mindkét megközelítés?  A döntéshez az azzal kapcsolatos elvárások, ahol a felhasználói forgalom állapottesztjei származnak, valamint milyen jól méretezhető a többi egy alkalmazást támogató háttér-infrastruktúra.  Például a 100 %-os állapot nélküli alkalmazások, az alkalmazások is nagymértékben méretezhető több App Service Environment-környezetek kombinációját használva az Azure-régió, és a több Azure-régiók telepített App Service-környezetek /.  A 15 + nyilvános Azure-régiók elérhető lehetőségek közül választhat az ügyfelek valóban hozhat létre egy világszerte kapacitású alkalmazás erőforrásigényét.  Az ebben a cikkben használt mintaalkalmazás három App Service Environment-környezetek (déli középső Régiójában) egyetlen Azure régióban létrehozott.
* **Az App Service Environment-környezetek elnevezési konvenció:** minden App Service Environment-környezet megköveteli egy egyedi nevet.  Egy vagy két App Service Environment-környezetek túl már van egy elnevezési konvenciója, amellyel azonosítható, minden egyes App Service Environment-környezet számára.  A mintaalkalmazás egyszerű elnevezési lett megadva.  A három App Service Environment-környezetek neve *fe1ase*, *fe2ase*, és *fe3ase*.
* **Az alkalmazások elnevezési konvenció:** az alkalmazás több példányát telepíti, mert egy nevet a telepített alkalmazás egyes példányainak van szükség.  Egy kevés ismert azonban nagyon kényelmes App Service Environment-környezetek jellemzője, hogy használható-e az azonos alkalmazásnév több App Service Environment-környezetek között.  Mivel minden egyes App Service Environment-környezet csak egy egyedi tartományutótagot, a fejlesztők használhat újra a pontos néven app minden környezetben.  Egy fejlesztő például rendelkezhetnek elnevezése a következő alkalmazásokat: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*stb.  A mintaalkalmazás azonban minden app-példány is van egy egyedi nevet.  A használt alkalmazás-példány neve *webfrontend1*, *webfrontend2*, és *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>A Traffic Manager-profil beállítása
Ha egy alkalmazás több példánya több App Service Environment-környezetek vannak telepítve, az egyes alkalmazásokra példányok regisztrálható a Traffic Managerrel.  A mintaalkalmazás egy Traffic Manager profil van szükség a *méretezhető ase demo.trafficmanager.net* , amely irányíthatja a felhasználók számára a következő telepített alkalmazás példányra:

* **webfrontend1.fe1ase.p.azurewebsites.NET:** a mintaalkalmazás az első App Service Environment-környezet telepített példánya.
* **webfrontend2.fe2ase.p.azurewebsites.NET:** a mintaalkalmazást, a második App Service Environment-környezet telepített példánya.
* **webfrontend3.fe3ase.p.azurewebsites.NET:** a mintaalkalmazást, a harmadik App Service Environment-környezet telepített példánya.

A legegyszerűbben úgy lehet regisztrálni a több Azure App Service végpontot, az összes futó a **azonos** Azure-régió, a PowerShell használatával az [Azure Resource Manager Traffic Manager-támogatás][ARMTrafficManager].  

Az első lépés, ha az Azure Traffic Manager-profil.  A az alábbi kód bemutatja, hogyan lett létrehozva a profil a mintaalkalmazást:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Értesítés az *RelativeDnsName* paraméter lett állítva: *méretezhető ase bemutató*.  Ez hogyan tartománynév *méretezhető ase demo.trafficmanager.net* létrejön, és a Traffic Manager-profil társított.

A *TrafficRoutingMethod* paraméter határozza meg a terheléselosztási házirend Traffic Manager terhelés elosztva a rendelkezésre álló végpontok hogyan fogja használni.  Ebben a példában a *Weighted* módszer választása.  Ennek eredményeképpen az ügyfelek kéréseire alatt elosztva a regisztrált alkalmazáshoz végpontok a relatív minden végponthoz társított súlyok alapján. 

A létrehozott profil minden alkalmazáspéldányban profilhoz van adva a natív Azure végpontként.  Az alábbi kódot, lekéri az előtérbeli webes alkalmazásokra mutató hivatkozás, és hozzáadja minden alkalmazás úton Traffic Manager-végpontként a *targetresourceid azonosítója* paraméter.

    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Figyelje meg, hogyan van egy hívás *Add-AzureTrafficManagerEndpointConfig* minden egyes alkalmazás-példányhoz.  A *targetresourceid azonosítója* paramétere minden Powershell-parancs egy telepített alkalmazás három példányt hivatkozik.  A Traffic Manager-profil betöltése elosztva a profillal regisztrált összes három végponton.

A három végpontok ugyanarra az értékre (10) használata a *súly* paraméter.  Az eredmény elterjedésének Traffic Manager-ügyfelek kéréseire minden három app példányára viszonylag egyenletes. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Az alkalmazás egyéni tartományt, a Traffic Manager-tartományra mutat.
Az utolsó lépés szükséges, hogy az egyéni tartomány, az alkalmazást a Traffic Manager-tartományra mutasson.  A mintaalkalmazás Ez azt jelenti, hogy mutató *www.scalableasedemo.com* : *méretezhető ase demo.trafficmanager.net*.  Ebben a lépésben kell elvégezni a tartomány tartományregisztrálóval, amely kezeli az egyéni tartomány.  

A regisztráló tartományi felügyeleti eszközökkel, egy olyan CNAME REKORDOT kell létrehozni, amely az egyéni tartomány, a Traffic Manager-tartományra mutat rögzíti.  A következő ábrán látható egy példa a CNAME-konfiguráció néz:

![Egyéni tartomány CNAME][CNAMEforCustomDomain] 

Bár nem ebben a témakörben ismertetett, ne feledje, hogy az egyes alkalmazásokra feltünteti az egyéni tartományt is regisztrálva, rendelkeznie kell.  Ellenkező esetben egy kérelem teszi az alkalmazás példánya, és az alkalmazás nem rendelkezik az egyéni tartomány regisztrál az alkalmazással, ha a kérelem sikertelen lesz.  

Ebben a példában az egyéni tartomány pedig *www.scalableasedemo.com*, és minden alkalmazáspéldány a vele társított egyéni tartománnyal rendelkezik.

![Egyéni tartomány][CustomDomain] 

Egy összefoglalása az Azure App Service-alkalmazásokhoz történő regisztrálásának egyéni tartományt, tekintse meg az alábbi cikket a [egyéni tartományok regisztrálása][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Az Elosztott topológia kipróbálása
End a Traffic Manager és a DNS-konfiguráció eredménye, hogy vonatkozó kérések *www.scalableasedemo.com* halad keresztül az alábbi sorrendben:

1. Egy böngésző vagy eszköz DNS-címkeresést tesz *www.scalableasedemo.com*
2. A CNAME bejegyzése a tartományregisztráló azt eredményezi, a DNS-ben az Azure Traffic Manager kell átirányítani.
3. DNS-címkeresést történik *méretezhető ase demo.trafficmanager.net* szemben az Azure Traffic Manager DNS-kiszolgálók egyikét.
4. A terheléselosztási házirend alapján (a *TrafficRoutingMethod* a Traffic Manager-profil létrehozásakor korábban használt paraméter), a Traffic Manager válassza ki a beállított végpontjaikra lesz, és térjen vissza az adott végpontra teljes Tartománynevét a böngésző vagy az eszköz.
5. Mivel a teljes Tartománynevet a végpont egy App Service Environment-környezet futó alkalmazás példány URL-címét, a böngésző vagy az eszköz felhasználói jóváhagyást kér a Microsoft Azure DNS-kiszolgáló IP-cím legyen feloldani a teljes Tartománynevet. 
6. A böngésző vagy az eszköz a HTTP/S kérelem elküld az IP-címre.  
7. A kérelem egy időben érkeznek, az App Service Environment-környezetek egyikén futó alkalmazás példányai közül.

A konzol a következő ábrán egy DNS-címkeresés a mintaalkalmazás egyéni tartomány sikeresen feloldása egy alkalmazás-példányon futó egyet a három minta App Service Environment-környezetek (ebben az esetben a második, a három App Service Environment-környezetek):

![DNS-címkeresés][DNSLookup] 

## <a name="additional-links-and-information"></a>További hivatkozások és információk
A Powershell-dokumentációja [Azure Resource Manager Traffic Manager-támogatás][ARMTrafficManager].  

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
