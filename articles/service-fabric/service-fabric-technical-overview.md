---
title: Ismerje meg az Azure Service Fabric terminológiáját
description: Ismerje meg a legfontosabb Service Fabric terminológia és a dokumentáció többi részében használt fogalmak.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: a9266c2a8d2ad179cfdb12e367a14f37d1abc9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258238"
---
# <a name="service-fabric-terminology-overview"></a>A Service Fabric terminológia áttekintése

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését.  A [Service Fabric-fürtök bárhol üzemeltetheti:](service-fabric-deploy-anywhere.md)Azure, egy helyszíni adatközpontban, vagy bármely felhőszolgáltató.  A Service Fabric az az orchestrator, amely az [Azure Service Fabric Hálót működteti.](/azure/service-fabric-mesh) Bármely keretrendszer segítségével a szolgáltatások írása, és válassza ki, hogy hol futtassa az alkalmazást több környezeti lehetőségek. Ez a cikk részletezi a Szolgáltatás fabric által a dokumentációban használt kifejezések megértéséhez használt terminológia.

## <a name="infrastructure-concepts"></a>Infrastrukturális koncepciók

**Fürt:** Virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatásokat telepítik és kezelik.  A fürtök több ezer gépre skálázhatók.

**Csomópont**: A fürt részét vevő gépet vagy virtuális gépet *csomópontnak nevezzük.* Minden csomóponthoz csomópontnév (karakterlánc) van rendelve. A csomópontok nak vannak jellemzőik, például elhelyezési tulajdonságok. Minden gép vagy virtuális gép rendelkezik `FabricHost.exe`egy automatikus indítású Windows-szolgáltatással, amely `Fabric.exe` `FabricGateway.exe`indításkor elindul, majd két végrehajtható fájlt indít el: és . Ez a két végrehajtható fájl alkotja a csomópontot. Tesztelési forgatókönyvek esetén több csomópontot is üzemeltethet egyetlen gépen vagy `Fabric.exe` virtuális `FabricGateway.exe`gépen, ha több példányt futtat a és a.

## <a name="application-and-service-concepts"></a>Alkalmazási és szolgáltatási koncepciók

**Service Fabric Mesh Application**: A Service Fabric mesh alkalmazások leírása az erőforrásmodell (YAML és JSON erőforrásfájlok) és telepíthető bármely környezetben, ahol a Service Fabric fut.

**Service Fabric natív alkalmazás:** Service Fabric natív alkalmazások a natív alkalmazásmodell (XML-alapú alkalmazás és szolgáltatásjegyzékek) írja le.  A Service Fabric natív alkalmazásai nem futtathatók a Service Fabric Mesh alkalmazásban.

### <a name="service-fabric-mesh-application-concepts"></a>A Service Fabric Mesh alkalmazás fogalmai

**Alkalmazás**: Az alkalmazás a hálóalkalmazások központi telepítésének, verziószámozásának és élettartamának egysége. Az egyes alkalmazáspéldányok életciklusa egymástól függetlenül kezelhető.  Az alkalmazások egy vagy több szolgáltatáskódcsomagból és -beállításból állnak. Egy alkalmazás az Azure Resource Model (RM) séma használatával van definiálva.  A szolgáltatások az erőforrás-védelmi sablonban az alkalmazáserőforrás tulajdonságaiként vannak leírva.  Az alkalmazás által használt hálózatokra és kötetekre az alkalmazás hivatkozik.  Egy alkalmazás létrehozásakor az alkalmazás, a szolgáltatás(ok), a hálózat és a kötet(ek) a Service Fabric erőforrásmodell használatával modelleződnek.

**Szolgáltatás**: Egy alkalmazás szolgáltatása mikroszolgáltatást jelöl, és teljes és önálló függvényt hajt végre. Minden szolgáltatás egy vagy több kódcsomagból áll, amelyek leírják a kódcsomaghoz társított tárolórendszerkép futtatásához szükséges mindent.  Az alkalmazásokban lévő szolgáltatások száma fel- és leskálázható.

**Hálózat**: A hálózati erőforrás magánhálózatot hoz létre az alkalmazások számára, és független az arra hivatkozó alkalmazásoktól vagy szolgáltatásoktól. Különböző alkalmazásokból származó több szolgáltatás is ugyanannak a hálózatnak a része lehet. A hálózatok olyan telepíthető erőforrások, amelyekre az alkalmazások hivatkoznak.

**Kódcsomag**: A kódcsomagok leírnak mindent, ami a kódcsomaghoz társított tárolórendszerkép futtatásához szükséges, beleértve a következőket:

* Tároló neve, verziója és rendszerleíró adatbázisa
* Az egyes tárolókhoz szükséges PROCESSZOR- és memóriaerőforrások
* Hálózati végpontok
* A tárolóba csatlakoztatandó kötetek, amelyek egy külön köteterőforrásra hivatkoznak.

Az alkalmazáserőforrás részeként definiált összes kódcsomag telepítve van, és csoportként együtt van aktiválva.

**Kötet**: Kötetek olyan könyvtárak, amelyek a tárolópéldányokon belül vannak csatlakoztatva, amelyek segítségével megőrizheti az állapotot. Az Azure Files kötet-illesztőprogram egy Azure Files-megosztást csatlakoztat egy tárolóhoz, és megbízható adattárolást biztosít bármely OLYAN API-n keresztül, amely támogatja a fájltárolást. A kötetek olyan telepíthető erőforrások, amelyekre az alkalmazások hivatkoznak.

### <a name="service-fabric-native-application-concepts"></a>Service Fabric natív alkalmazás fogalmak

**Alkalmazás**: Az alkalmazás egy bizonyos funkciót vagy függvényeket ellátó rendszerelem-szolgáltatások gyűjteménye. Az egyes alkalmazáspéldányok életciklusa egymástól függetlenül kezelhető.

**Szolgáltatás**: A szolgáltatás teljes és önálló funkciót lát el, és más szolgáltatásoktól függetlenül indíthat és futtatható. A szolgáltatás kódból, konfigurációból és adatokból áll. Minden szolgáltatás esetében a kód a végrehajtható bináris fájlokból áll, a konfiguráció futásidőben betölthető szolgáltatásbeállításokból áll, és az adatok tetszőleges statikus adatokból állnak, amelyeket a szolgáltatás használ fel.

**Alkalmazástípus**: A szolgáltatástípusok gyűjteményéhez rendelt név/verzió. Egy `ApplicationManifest.xml` fájlban van definiálva, és egy alkalmazáscsomag könyvtárába ágyazódik. A könyvtár ezután másolja a Service Fabric fürt lemezképtároló. Ezután létrehozhat egy elnevezett alkalmazást ebből az alkalmazástípusból a fürtön belül.

További információkért olvassa el az [alkalmazásmodellről](service-fabric-application-model.md) szóló cikket.

**Alkalmazáscsomag**: Az alkalmazástípus fájlját `ApplicationManifest.xml` tartalmazó lemezkönyvtár. Az alkalmazástípust kiíró minden egyes szolgáltatástípushoz hivatkozik a szolgáltatáscsomagokra. Az alkalmazáscsomag könyvtárában lévő fájlok másolása a Service Fabric-fürt lemezképtárolójára történik. Egy e-mail alkalmazástípus alkalmazáscsomagja például tartalmazhat egy várólista-szolgáltatás csomagra, egy előszolgáltatási csomagra és egy adatbázis-szolgáltatás csomagra mutató hivatkozásokat.

**Elnevezett alkalmazás**: Miután egy alkalmazáscsomagot másol a lemezképtárolóba, létrehozza az alkalmazás egy példányát a fürtön belül. Az alkalmazáscsomag alkalmazástípusának megadásakor egy példányt a neve vagy verziója alapján hoz létre. Minden alkalmazástípus-példányhoz egy egységes erőforrásazonosító (URI) `"fabric:/MyNamedApp"`név van rendelve, amely a következőképpen néz ki: . Egy fürtön belül egyetlen alkalmazástípusból több elnevezett alkalmazást is létrehozhat. Névvel ellátott alkalmazásokat is létrehozhat különböző alkalmazástípusokból. Minden egyes elnevezett alkalmazás kezelése és verzióverziója egymástól függetlenül történik.

**Szolgáltatás típusa**: A szolgáltatás kódcsomagjaihoz, adatcsomagjaihoz és konfigurációs csomagjaihoz rendelt név/verzió. A szolgáltatástípus a `ServiceManifest.xml` fájlban van definiálva, és egy szolgáltatáscsomag-könyvtárba van ágyazva. A szolgáltatáscsomag könyvtárára ezután egy alkalmazáscsomag fájlja hivatkozik. `ApplicationManifest.xml` A fürtön belül egy elnevezett alkalmazás létrehozása után létrehozhat egy elnevezett szolgáltatást az alkalmazástípus egyik szolgáltatástípusából. A szolgáltatástípus `ServiceManifest.xml` fájlja leírja a szolgáltatást.

További információkért olvassa el az [alkalmazásmodellről](service-fabric-application-model.md) szóló cikket.

Kétféle szolgáltatás létezik:

* **Állapot nélküli:** Használjon állapotnélküli szolgáltatást, ha a szolgáltatás állandó állapota egy külső tárolási szolgáltatásban van tárolva, például az Azure Storage, az Azure SQL Database vagy az Azure Cosmos DB. Használjon állapotmentes szolgáltatást, ha a szolgáltatás nem rendelkezik állandó tárolóval. Például egy számológép szolgáltatás, ahol értékeket adnak át a szolgáltatásnak, egy számítás takar, amely ezeket az értékeket használja, majd egy eredményt ad vissza.
* **Állapotalapú:** Állapotalapú szolgáltatás használata, ha azt szeretné, hogy a Service Fabric a szolgáltatás állapotának kezelése a megbízható gyűjtemények vagy a Reliable Actors programozási modellek használatával. Ha névvel ellátott szolgáltatást hoz létre, adja meg, hogy hány partícióra szeretné terjeszteni az állapotot a méretezhetőség érdekében. Azt is adja meg, hogy hányszor replikálja az állapotot a csomópontok között, a megbízhatóság érdekében. Minden elnevezett szolgáltatás egyetlen elsődleges replika és több másodlagos replika rendelkezik. Az elsődleges replika írásakor módosítja a megnevezett szolgáltatás állapotát. A Service Fabric ezután replikálja ezt az állapotot az összes másodlagos replikák az állapot szinkronban tartása érdekében. A Service Fabric automatikusan észleli, ha egy elsődleges replika meghibásodik, és egy meglévő másodlagos replika elsődleges replika. A Service Fabric ezután létrehoz egy új másodlagos replika.  

**Replikák vagy példányok** egy üzembe helyezett és futó szolgáltatás kódjaira (és állapotalapú szolgáltatások állapotára hivatkoznak). Lásd: [Replikák és példányok](service-fabric-concepts-replica-lifecycle.md).

**Az újrakonfigurálás** a szolgáltatás replikakészletében bekövetkező bármely módosítás folyamatára vonatkozik. Lásd: [Újrakonfigurálás](service-fabric-concepts-reconfiguration.md).

**Szolgáltatáscsomag**: A szolgáltatástípus fájlját `ServiceManifest.xml` tartalmazó lemezkönyvtár. Ez a fájl a szolgáltatástípus kódjára, statikus adataira és konfigurációs csomagjaira hivatkozik. A szolgáltatáscsomag könyvtárában lévő fájlokra az alkalmazástípus fájlja `ApplicationManifest.xml` hivatkozik. Egy szolgáltatáscsomag például hivatkozhat az adatbázis-szolgáltatást felálló kódra, statikus adatokra és konfigurációs csomagokra.

**Elnevezett szolgáltatás**: Miután létrehozott egy elnevezett alkalmazást, létrehozhategy példányt az egyik szolgáltatástípusából a fürtön belül. A szolgáltatás típusát a neve/verziója alapján adhatja meg. Minden szolgáltatástípus-példányhoz hozzá van rendelve egy URI-névhatókör a megnevezett alkalmazás URI-ja alatt. Ha például egy "MyNamedApp" nevű alkalmazáson belül "MyDatabase" nevű szolgáltatást `"fabric:/MyNamedApp/MyDatabase"`hoz létre, az URI így néz ki: . Egy elnevezett alkalmazáson belül több elnevezett szolgáltatást is létrehozhat. Minden elnevezett szolgáltatás rendelkezhet saját partíciósémával és példány- vagy replikaszámmal.

**Kódcsomag**: A szolgáltatástípus végrehajtható fájljait, általában EXE/DLL fájlokat tartalmazó lemezkönyvtár. A kódcsomag könyvtárában lévő fájlokra a szolgáltatástípus fájlja `ServiceManifest.xml` hivatkozik. Névvel ellátott szolgáltatás létrehozásakor a kódcsomag a névvel ellátott szolgáltatás futtatásához kijelölt csomópontra vagy csomópontokra kerül. Ezután a kód elindul. A kódcsomag végrehajtható fájljainak két típusa van:

* **Vendég végrehajtható fájlok**: A gazdaoperációs rendszeren (Windows vagy Linux) futó végrehajtható fájlok. Ezek a végrehajtható fájlok nem hivatkoznak a Service Fabric futásidejű fájljaira, és ezért nem használnak semmilyen Service Fabric programozási modellt. Ezek a végrehajtható fájlok nem tudják használni a Service Fabric egyes szolgáltatásait, például a végpontfelderítés elnevezési szolgáltatását. A vendég végrehajtható fájljai nem jelenthetik az egyes szolgáltatáspéldányokra jellemző betöltési metrikákat.
* **Service host executables**: A Service Fabric programozási modelleket használó végrehajtható fájlok a Service Fabric futásidejű fájljaihoz való csatolással, a Service Fabric-szolgáltatások engedélyezésével. Például egy elnevezett szolgáltatáspéldány is regisztrálhat végpontokat a Service Fabric elnevezési szolgáltatás, és jelentheti a betöltési metrikák.

**Adatcsomag**: Olyan lemezkönyvtár, amely a szolgáltatástípus statikus, csak olvasható adatfájljait, általában fénykép-, hang- és videofájljait tartalmazza. Az adatcsomag könyvtárában lévő fájlokra a szolgáltatástípus fájlja hivatkozik. `ServiceManifest.xml` Névvel ellátott szolgáltatás létrehozásakor az adatcsomag a névvel ellátott szolgáltatás futtatásához kijelölt csomópontra vagy csomópontokra kerül. A kód elindul, és most már hozzáférhet az adatfájlokhoz.

**Konfigurációs csomag**: Olyan lemezkönyvtár, amely a szolgáltatástípus statikus, csak olvasható konfigurációs fájljait, általában szöveges fájljait tartalmazza. A konfigurációs csomag könyvtárában lévő fájlokra a `ServiceManifest.xml` szolgáltatástípus fájlja hivatkozik. Névvel ellátott szolgáltatás létrehozásakor a konfigurációs csomagban lévő fájlok a megnevezett szolgáltatás futtatásához kijelölt egy vagy több csomópontra kerülnek. Ezután a kód elindul, és most hozzáférhet a konfigurációs fájlokhoz.

**Tárolók**: Alapértelmezés szerint a Service Fabric folyamatokként telepíti és aktiválja a szolgáltatásokat. A Service Fabric is üzembe helyezheti a szolgáltatásokat a tárolórendszerképeken. A tárolók olyan virtualizációs technológia, amely kivonta az alapul szolgáló operációs rendszert az alkalmazásokból. Egy alkalmazás és a futásidejű, függőségek és rendszerkönyvtárak egy tárolóban futnak. A tároló teljes körű, privát hozzáféréssel rendelkezik a tároló saját elkülönített nézetéhez az operációs rendszer konstrukcióiról. A Service Fabric támogatja a Windows Server-tárolókat és a Docker-tárolókat Linuxon. További információért olvassa el [a Service Fabric és a tárolók című.](service-fabric-containers-overview.md)

**Partícióséma**: Ha létrehoz egy elnevezett szolgáltatást, meg ad egy partíciósémát. Szolgáltatások jelentős mennyiségű állapot felosztása az adatok partíciók között, amely elosztja az állapot ot a fürt csomópontjai között. Az adatok partíciók közötti felosztásával a megnevezett szolgáltatás állapota skálázható. Egy partíción belül állapot nélküli elnevezett szolgáltatások példányok, míg állapotalapú elnevezett szolgáltatások replikák. Általában az állapotnélküli elnevezett szolgáltatások csak egy partícióval rendelkeznek, mert nincs belső állapotuk. A partíciópéldányok biztosítják a rendelkezésre állást. Ha egy példány meghibásodik, a többi példány továbbra is normálisan működik, majd a Service Fabric létrehoz egy új példányt. Állapotalapú elnevezett szolgáltatások állapotuk a replikákon belül, és minden partíció saját replikakészlet, így az állapot szinkronban marad. Ha egy replika sikertelen, a Service Fabric új replika a meglévő replika.

További információkért olvassa el a [Partition Service Fabric megbízható szolgáltatások](service-fabric-concepts-partitioning.md) ról szóló cikket.

## <a name="system-services"></a>Rendszerszolgáltatások

Vannak olyan rendszerszolgáltatások, amelyek minden fürtben létrejönnek, amelyek a Service Fabric platformképességeit biztosítják.

**Elnevezési szolgáltatás:** Minden szolgáltatásháló-fürt rendelkezik egy elnevezési szolgáltatással, amely feloldja a szolgáltatásneveket a fürt egy helyére. A szolgáltatás neveket és tulajdonságokat, például a fürt internetes tartománynév-rendszerét (DNS) kezelheti. Az ügyfelek biztonságosan kommunikálnak a fürt bármely csomócsomójával az elnevezési szolgáltatás használatával a szolgáltatás nevének és helyének feloldásához. Az alkalmazások a fürtön belül mozognak. Ennek oka lehet például a hibák, az erőforrás-kiegyensúlyozás vagy a fürt átméretezése. Olyan szolgáltatásokat és ügyfeleket fejleszthet, amelyek feloldják az aktuális hálózati helyet. Az ügyfelek beszerzik a számítógép tényleges IP-címét és portját, ahol jelenleg fut.

Olvassa el [a Kommunikáció a szolgáltatásokkal](service-fabric-connect-and-communicate-with-services.md) című olvasni című olvasni való kommunikáció című olvasni való további információkért az ügyfél- és szolgáltatáskommunikációs API-król, amelyek az elnevezési szolgáltatással működnek.

**Image Store szolgáltatás:** Minden Service Fabric-fürt rendelkezik egy image store szolgáltatás, ahol telepített, verziózott alkalmazáscsomagok vannak tárolva. Másolja az alkalmazáscsomagot az Image Store-ba, majd regisztrálja az alkalmazáscsomagban található alkalmazástípust. Az alkalmazástípus kiépítése után egy elnevezett alkalmazást hoz létre belőle. Az összes elnevezett alkalmazás törlése után törölheti az alkalmazástípus regisztrációját az Image Store szolgáltatásból.

Olvassa [el Az ImageStoreConnectionString beállítás ismertetése](service-fabric-image-store-connection-string.md) című olvasni való további információkért az Image Store szolgáltatásról.

Olvassa el az [alkalmazás telepítése](service-fabric-deploy-remove-applications.md) cikket az alkalmazások a Lemezképtár szolgáltatásban történő központi telepítéséről.

**Feladatátvétel-kezelő szolgáltatás:** Minden szolgáltatásháló-fürt rendelkezik egy feladatátvevő kezelő szolgáltatással, amely a következő műveletekért felelős:

 - A szolgáltatások magas rendelkezésre állásával és konzisztenciájával kapcsolatos funkciókat hajt végre.
 - Vezényli az alkalmazás- és fürtfrissítéseket.
 - Más rendszerösszetevőkkel kommunikál.

**Repair Manager szolgáltatás:** Ez egy opcionális rendszerszolgáltatás, amely lehetővé teszi a javítási műveletek et a fürtön biztonságos, automatizálható és átlátható módon. A javításkezelő a következő helyeken használatos:

   - Azure-karbantartási javítások végrehajtása [silver és gold tartóssági](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Azure Service Fabric-fürtökön.
   - Javítási műveletek végrehajtása a [Patch Orchestration Application alkalmazáshoz](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Központi telepítési és alkalmazásmodellek

A szolgáltatások üzembe helyezéséhez le kell írnia, hogyan kell futtatni. A Service Fabric három különböző üzembe helyezési modellt támogat:

### <a name="resource-model-preview"></a>Erőforrásmodell (előzetes verzió)

A Service Fabric-erőforrások olyan ok, amely külön-külön telepíthető a Service Fabric számára; beleértve az alkalmazásokat, szolgáltatásokat, hálózatokat és köteteket. Az erőforrások meghatározása JSON-fájl lal van definiálva, amely fürtvégpontra telepíthető.  A Service Fabric Mesh, az Azure Resource Model séma használatos. A YAML-fájlséma a definíciós fájlok könnyebb megszerzőségére is használható. Az erőforrások bárhol üzembe helyezhetők, ahol a Service Fabric fut. Az erőforrás-modell a legegyszerűbb módja a Service Fabric-alkalmazások leírásának. Fő hangsúly az egyszerű üzembe helyezés és a konténeres szolgáltatások kezelése. További információ: [Bevezetés a Service Fabric erőforrásmodellbe](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)című olvasni.

### <a name="native-model"></a>Natív modell

A natív alkalmazásmodell teljes alacsony szintű hozzáférést biztosít az alkalmazások számára a Service Fabric-hez. Az alkalmazások és szolgáltatások az XML-jegyzékfájlokban regisztrált típusokként vannak definiálva.

A natív modell támogatja a Reliable Services és a Reliable Actors keretrendszereket, amely hozzáférést biztosít a Service Fabric futásidejű API-k és a fürtfelügyeleti API-k C# és Java. A natív modell is támogatja az tetszőleges tárolók és végrehajtható fájlokat. A natív modell nem támogatott a [Service Fabric Mesh környezetben.](/azure/service-fabric-mesh/service-fabric-mesh-overview)

**Megbízható szolgáltatások:** Állapottalan és állapotalapú szolgáltatások létrehozásához. Az állapotalapú szolgáltatások megbízható gyűjtemények, például egy szótár ban vagy egy várólistában tárolják állapotukat. Különböző kommunikációs halmokat is csatlakoztathat, például a webes API-t és a Windows kommunikációs alaprendszert (WCF).

**Megbízható szereplők:** Egy API-t állapotnélküli és állapotalapú objektumok a virtuális szereplő programozási modellen keresztül. Ez a modell akkor hasznos, ha sok független számítási vagy állapotegysége van. Ez a modell egy többalapú szálas szálasítási modellt használ, ezért a legjobb elkerülni a más szereplőket vagy szolgáltatásokat hívó kódot, mert az egyes aktor nem tudja feldolgozni a többi bejövő kérelmet, amíg az összes kimenő kérelmet be nem fejezik.

A meglévő alkalmazásokat is futtathatja a Service Fabricen:

**Tárolók:** A Service Fabric támogatja a Docker-tárolók linuxos és Windows Server-tárolókon történő telepítését A Windows Server 2016 rendszeren, valamint támogatja a Hyper-V elkülönítési módot. A Service Fabric [alkalmazásmodellben](service-fabric-application-model.md)egy tároló egy alkalmazásgazda, amelyben több szolgáltatás replikák vannak elhelyezve. A Service Fabric bármely tárolót futtathat, és a forgatókönyv hasonló a vendég végrehajtható forgatókönyvhöz, ahol egy meglévő alkalmazást csomagol egy tárolóban. Emellett [a Service Fabric-szolgáltatások tárolókban](service-fabric-services-inside-containers.md) is futtathatók.

**Vendég végrehajtható fájlok:** Bármilyen típusú kódot futtathat, például node.js, Python, Java vagy C++ az Azure Service Fabric szolgáltatásként. A Service Fabric az ilyen típusú szolgáltatások at vendég végrehajtható fájlként, amelyek állapotnélküli szolgáltatásokként kezelik. A Service Fabric-fürtben futtatható vendég végrehajtható fájlok előnyei közé tartozik a magas rendelkezésre állás, az állapotfigyelés, az alkalmazás életciklus-kezelése, a nagy sűrűség és a felderíthetőség.

További információért olvassa el a [Programmodell kiválasztása a szolgáltatási](service-fabric-choose-framework.md) cikkhez című cikket.

### <a name="docker-compose"></a>Docker-írás 

[A Docker Compose](https://docs.docker.com/compose/) a Docker-projekt része. A Service Fabric korlátozott támogatást nyújt [a Docker Compose modellt használó alkalmazások üzembe helyezéséhez.](service-fabric-docker-compose.md)

## <a name="environments"></a>Környezetek

A Service Fabric egy nyílt forráskódú platformtechnológia, amelyen számos különböző szolgáltatás és termék alapul. A Microsoft a következő lehetőségeket biztosítja:

 - **Azure Service Fabric Mesh**: Teljes körűen felügyelt szolgáltatás a Service Fabric-alkalmazások futtatásához a Microsoft Azure-ban.
 - **Azure Service Fabric:** Az Azure üzemeltetett Service Fabric-fürtajánlat. Integrációt biztosít a Service Fabric és az Azure-infrastruktúra között, valamint a Service Fabric-fürtök frissítése és konfigurációs kezelése között.
 - **Service Fabric önálló:** A telepítési és konfigurációs eszközök telepítése [Service Fabric-fürtök bárhol](/azure/service-fabric/service-fabric-deploy-anywhere) (a helyszínen vagy bármely felhőszolgáltató) üzembe helyezéséhez. Nem az Azure kezeli.
 - **Service Fabric fejlesztői fürt:** Helyi fejlesztési élményt nyújt Windows, Linux vagy Mac a Service Fabric-alkalmazások fejlesztéséhez.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Környezet, keretrendszer és telepítési modell támogatási mátrixa

A különböző környezetek különböző szintű támogatást nyújtanak a keretrendszerekhez és a telepítési modellekhez. Az alábbi táblázat ismerteti a támogatott keretrendszer és üzembe helyezési modell kombinációit.

| Az alkalmazás típusa | Leírta: | Azure service fabric háló | Azure Service Fabric-fürtök (bármely operációs rendszer)| Helyi fürt | Önálló fürt |
|---|---|---|---|---|---|
| Szolgáltatásháló-alkalmazások | Erőforrásmodell (YAML & JSON) | Támogatott |Nem támogatott | Windows- támogatott, Linux és Mac- nem támogatott | Windows- nem támogatott |
|Szolgáltatásháló natív alkalmazásai | Natív alkalmazásmodell (XML) | Nem támogatott| Támogatott|Támogatott|Windows- támogatott|

Az alábbi táblázat ismerteti a különböző alkalmazásmodellek és a Service Fabric számára létező eszközök.

| Az alkalmazás típusa | Leírta: | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Szolgáltatásháló-alkalmazások | Erőforrásmodell (YAML & JSON) | VS 2017 |Nem támogatott |Nem támogatott | Támogatott - csak hálós környezet | Nem támogatott|
|Szolgáltatásháló natív alkalmazásai | Natív alkalmazásmodell (XML) | VS 2017 és VS 2015| Támogatott|Támogatott|Támogatott|Támogatott|

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Fabric:To learn more about Service Fabric:

* [A Service Fabric áttekintése](service-fabric-overview.md)
* [Miért érdemes a mikroszolgáltatás-alapú megközelítést választani alkalmazások létrehozásához?](service-fabric-overview-microservices.md)
* [Alkalmazáshasználati helyzetek](service-fabric-application-scenarios.md)

További információ a Service Fabric Mesh-ről:

* [A szolgáltatásháló áttekintése](/azure/service-fabric-mesh/service-fabric-mesh-overview)
