---
title: Ismerje meg az Azure Service Fabric terminológiája |} A Microsoft Docs
description: A Service Fabric terminológiája áttekintése. Legfontosabb terminológia fogalmak és a többi a dokumentációban használt fogalmak tárgyalja.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/17/2018
ms.author: ryanwi
ms.openlocfilehash: fda6af0f253457aaf3aef1e8444850592255b318
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113673"
---
# <a name="service-fabric-terminology-overview"></a>A Service Fabric a terminológia áttekintése
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését.  Is [gazdagépet a Service Fabric-fürtök bárhol](service-fabric-deploy-anywhere.md): Azure-ban, egy helyszíni adatközpontban vagy bármely más szolgáltatónál.  Service Fabric az orchestrator-megoldás a [Azure Service Fabric-háló](/azure/service-fabric-mesh). Bármely keretrendszer használatával a szolgáltatások írását, és válassza ki, az alkalmazás futtatásához több környezetben lehetőségek közül. Ez a cikk részletesen megérteni a kifejezések a dokumentáció a Service Fabric által használt terminológiával.

## <a name="infrastructure-concepts"></a>Infrastruktúra-fogalmak
**Fürt**: Mikroszolgáltatásokat helyezhet üzembe és felügyelhet, amelybe virtuális vagy fizikai gépek hálózaton keresztül csatlakozó készlete.  A fürtök több ezer gépre skálázhatók.

**Csomópont**: Egy számítógép vagy virtuális Gépet, amely egy fürt része nevezzük egy *csomópont*. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). Csomópontok jellemzőkkel, például elhelyezési tulajdonságokkal rendelkeznek. Minden számítógép vagy virtuális gép rendelkezik egy automatikusan induló Windows szolgáltatás `FabricHost.exe`, amelyek rendszerindítási követően elindul, és elindítja a két végrehajtható fájlok: `Fabric.exe` és `FabricGateway.exe`. E két végrehajtható fájlok a csomóponton alkotják. Tesztelési forgatókönyvek, több példányát futtatja egy egyetlen gépen vagy a virtuális gép több csomópont is üzemeltethet `Fabric.exe` és `FabricGateway.exe`.

## <a name="application-and-service-concepts"></a>Alkalmazás és a szolgáltatással kapcsolatos fogalmak

**Service Fabric-alkalmazását rácsvonal**: Service Fabric-háló alkalmazások által az erőforrás-modellje (YAML, JSON, és erőforrásfájlok) leírását, és telepíthető bármilyen környezetben, ahol a Service Fabric fut az.

**Service Fabric natív alkalmazás**: Service Fabric natív alkalmazások ismerteti a natív alkalmazás modell (XML-alapú alkalmazásra és szolgáltatásjegyzékre).  Service Fabric natív alkalmazások a Service Fabric-háló nem futtatható.

### <a name="service-fabric-mesh-application-concepts"></a>Service Fabric-háló alkalmazás fogalmak

**Alkalmazás**: Egy alkalmazás az üzembe helyezés, a verziókezelés és a egy háló alkalmazás teljes élettartama egysége. Mindegyik alkalmazáspéldány életciklusának egymástól függetlenül is felügyelhetők.  Alkalmazások egy vagy több szolgáltatás kódcsomagok és beállítások állnak. Egy alkalmazás használatával az Azure Resource modell (RM) séma van megadva.  Szolgáltatások, az alkalmazás-erőforrás az erőforrás-kezelő sablon tulajdonságait ismerteti.  Az alkalmazás által hivatkozott hálózatok és az alkalmazás által használt köteteket.  Egy alkalmazás létrehozásakor az alkalmazás, szolgáltatás(ok), hálózati és kötet(ek) modellezése eltér a Service Fabric erőforrás modell használatával.

**Szolgáltatás**: Egy szolgáltatás, az alkalmazások mikroszolgáltatások jelöli, és a egy teljes és a különálló funkciót hajt végre. Mindegyik szolgáltatás egy vagy több, minden a tároló rendszerképét a kódcsomag társított futtatásához szükséges leíró kódcsomagok tevődik össze.  A szolgáltatások az alkalmazások számát felfelé és lefelé skálázhatók.

**Hálózati**: Hálózati erőforrást hoz létre az alkalmazások számára a magánhálózaton, és független az alkalmazások vagy szolgáltatások, amelyek hozzá. A különböző alkalmazások több szolgáltatás ugyanazon a hálózaton része lehet. Hálózatok üzembe helyezhető alkalmazások által hivatkozott erőforrások.

**Kódcsomag**: Minden a tároló rendszerképét a kódcsomag, többek között az alábbi társított futtatásához szükséges kódot csomagok írják le:

* Tároló nevét, verzióját és a beállításjegyzék
* Az egyes tárolók szükséges CPU és memória-erőforrások
* Hálózati végpont
* Kötetek a tárolóban, egy önálló kötetre erőforrásra hivatkozik.

Egy alkalmazás-erőforrást részeként megadott kódcsomagok üzembe és aktiválása együtt, csoportként.

**Kötet**: Kötetek, amely csatlakozik a tárolópéldányok állapotban maradnak használó belső könyvtárak. Az Azure Files kötet illesztőprogram egy Azure-fájlmegosztási egy tárolóhoz csatlakoztatja, és bármely API-val, amely támogatja a file storage megbízható adattárolást biztosít. Kötetek üzembe helyezhető alkalmazások által hivatkozott erőforrások.

### <a name="service-fabric-native-application-concepts"></a>Service Fabric natív alkalmazás fogalmak

**Alkalmazás**: Egy alkalmazás egy bizonyos funkciója vagy funkciói végző alkotó szolgáltatások gyűjteménye. Mindegyik alkalmazáspéldány életciklusának egymástól függetlenül is felügyelhetők.

**Szolgáltatás**: Egy szolgáltatás egy teljes és a különálló funkciót látnak el, és elindíthatja és más szolgáltatások függetlenül futnak. Egy szolgáltatás, konfigurációs, és adatai tevődik össze. Minden egyes szolgáltatás kód a végrehajtható bináris fájlok áll, konfigurálása a futási időben töltődnek Szolgáltatásbeállítások áll, és tetszőleges statikus adatokat a szolgáltatás által használt adatokat tartalmaz.

**Az alkalmazástípus**: A neve/verziója szolgáltatás típusú rendeljük hozzá. Meg van határozva egy `ApplicationManifest.xml` fájlt, és a egy alkalmazás-csomag könyvtárában ágyazva. A könyvtár majd át lesznek másolva a Service Fabric-fürt lemezképtárolójába. Létrehozhat egy elnevezett alkalmazást a fürtön belül tento typ aplikace majd.

Olvassa el a [alkalmazásmodell](service-fabric-application-model.md) cikkben további információt.

**Alkalmazáscsomag**: Az alkalmazástípus tartalmazó lemez könyvtár `ApplicationManifest.xml` fájlt. A szolgáltatáscsomagok az egyes szolgáltatástípusokról, hogy az alkalmazás típusának hivatkozik. Service Fabric-fürt lemezképtárolójába lesz másolva a fájlokat az alkalmazás-csomag könyvtárában. Például egy e-mailek alkalmazástípus alkalmazáscsomaggal egy üzenetsor-service-csomag egy előtér-service-csomag és egy adatbázis-service-csomag hivatkozásokat tartalmazhatnak.

**Alkalmazás nevű**: Miután egy alkalmazáscsomagot a lemezképtárolójába másolja, hoz létre az alkalmazást a fürtön belül egy példányát. Csoport neve vagy a verzió használatával adja meg az alkalmazáscsomag alkalmazástípust példányt hoz létre. Írja be mindegyik alkalmazáspéldány hozzá van rendelve egy egységes erőforrás-azonosítója (URI) nevet, például a következőhöz: `"fabric:/MyNamedApp"`. A fürtön belül több elnevezett alkalmazást hozhat létre egy egyetlen alkalmazás írja be. Elnevezett alkalmazásokat is létrehozhat különböző alkalmazástípusokat. Minden elnevezett alkalmazás egymástól függetlenül, felügyelt és verziószámmal.

**Szolgáltatás típusa**: A neve/verziója egy szolgáltatás kódcsomagok, adatok csomagokat, és konfigurációs csomagokat. A szolgáltatás típusának van definiálva a `ServiceManifest.xml` fájlt, és a egy szolgáltatási csomag könyvtárában ágyazva. A szolgáltatási csomag könyvtárában majd hivatkozik egy alkalmazáscsomagot `ApplicationManifest.xml` fájlt. A fürtön belüli után egy elnevezett alkalmazást hozhat létre egy elnevezett szolgáltatás az alkalmazástípus szolgáltatás típusok közül. A szolgáltatás típusának `ServiceManifest.xml` fájl írja le a szolgáltatást.

Olvassa el a [alkalmazásmodell](service-fabric-application-model.md) cikkben további információt.

A szolgáltatások két típusa van:

* **Állapot nélküli**: Állapotmentes szolgáltatást akkor használja, ha a szolgáltatás állandó állapotát a rendszer egy külső storage szolgáltatásban, például az Azure Storage, Azure SQL Database vagy Azure Cosmos DB tárolja. Állapotmentes szolgáltatás használja, ha a szolgáltatás nem állandó tárolóban. Például Számológép szolgáltatáshoz, ahol a szolgáltatás érték lett átadva, a számítások elvégzésének, amely ezeket az értékeket használja, és ezután egy eredményt adja vissza.
* **Állapotalapú**: Az állapotalapú szolgáltatások akkor használja, ha azt szeretné, hogy a Reliable Collections vagy a Reliable Actors programozási modelleket a szolgáltatási állapot kezelése a Service Fabric. Amikor létrehoz egy elnevezett szolgáltatás, adja meg, hány partíciók skálázhatóságra az állapot eloszlatva szeretné. Is megadhatja, hány alkalommal való replikálása csomópontokat, a megbízhatóság az állapota. Minden elnevezett szolgáltatásnak van egy elsődleges replikával és több másodlagos replikát. Az elsődleges replika ír az elnevezett szolgáltatás állapotának módosításával. A Service Fabric Ez az állapot ezután replikálja a másodlagos replikákon szinkronban tartani az állapot. A Service Fabric automatikusan észleli, ha egy elsődleges replika meghibásodik, és elősegíti a egy meglévő másodlagos replika elsődleges replikára. A Service Fabric ekkor létrehoz egy új másodlagos replikára.  

**Replikák és példányok** kódot (és az állapotalapú szolgáltatások esetében állapot) üzembe helyezett szolgáltatás hivatkozik, és futtatásához. Lásd: [replikák és példányok](service-fabric-concepts-replica-lifecycle.md).

**Az újrakonfigurálás** a folyamat bármely módosítása a replika egy szolgáltatás hivatkozik. Lásd: [újrakonfigurálás](service-fabric-concepts-reconfiguration.md).

**Szolgáltatási csomag**: A szolgáltatás típusának tartalmazó lemez könyvtár `ServiceManifest.xml` fájlt. Ez a fájl a kódban, a statikus adatok, és a konfigurációs csomagokat a service Type hivatkozik. A fájlok a szolgáltatási csomag könyvtárában az alkalmazástípus által hivatkozott `ApplicationManifest.xml` fájlt. Például egy szolgáltatáscsomag utalhat a kódban, a statikus adatok, és a egy adatbázis-szolgáltatás alkotó konfigurációs csomagokat.

**Szolgáltatás nevű**: Miután létrehozott egy elnevezett alkalmazást, létrehozhat egyet a szolgáltatás közül a fürtön belül egy példányát. A szolgáltatás típusa az neve/verziója szerint kell megadni. Minden szolgáltatáspéldány típus URI nevét az elnevezett alkalmazást URI alatt hatóköre van hozzárendelve. Például, ha létrehoz egy "MyDatabase" nevű, "MyNamedApp" nevű application szolgáltatás, az URI-t néz ki: `"fabric:/MyNamedApp/MyDatabase"`. Egy elnevezett alkalmazáson belül több névvel ellátott szolgáltatást hozhat létre. Minden elnevezett szolgáltatás is rendelkezik, saját partícióséma és a példány, vagy megszámlálják az replika.

**Kódcsomag**: A szolgáltatás típusának végrehajtható fájlok, általában az EXE/DLL-fájlok tartalmazó lemez könyvtár. A kód csomag könyvtárában található fájlokat a szolgáltatás típusa által hivatkozott `ServiceManifest.xml` fájlt. Amikor létrehoz egy elnevezett szolgáltatás, a kódcsomag másolódik a csomópont egy vagy több kiválasztott elnevezett szolgáltatás futtatásához. Ezután a kód elindul. Kód csomag végrehajtható fájlok két típusa van:

* **Futtatható vendégalkalmazás**: Végrehajtható fájlok futtató – a gazdagép operációs rendszere (Windows vagy Linux) található. Ezek a futtatható fájlok nem kapcsolja össze, vagy nem hivatkozhat a Service Fabric futtatókörnyezet fájlokat, és nem ezért nem használhatja bármely Service Fabric programozási modelleket. Ezek a futtatható fájlok nem tudják használni az egyes Service Fabric-szolgáltatások, például az elnevezési szolgáltatás a végpont felderítése. Futtatható vendégalkalmazás nem készíthető jelentés az adott minden szolgáltatáspéldány terhelési mérőszámok.
* **Gazdagép végrehajtható fájlok szolgáltatás**: Végrehajtható fájlok, amelyek a Service Fabric programozási modelleket létrehozhatja, a Service Fabric-futtatókörnyezet fájlok, a Service Fabric-szolgáltatások engedélyezését. Például elnevezett szolgáltatáspéldányokban végpontok regisztrálhatja a Service Fabric elnevezési szolgáltatásban, és terhelési mérőszámok is jelentheti.

**Adat-csomag**: A szolgáltatás típusának statikus, csak olvasható adatok fájlokat, és általában fényképet, hang és videó fájlokat tartalmazó lemez könyvtár. A fájlokat az adatcsomag-könyvtár a szolgáltatás típusa által hivatkozott `ServiceManifest.xml` fájlt. Amikor létrehoz egy elnevezett szolgáltatás, az adatcsomag másolódik a csomópont egy vagy több kiválasztott elnevezett szolgáltatás futtatásához. A kód elindul, és most már elérheti az adatfájlokat.

**Konfigurációs csomag**: A szolgáltatás típusának statikus, csak olvasható konfigurációs fájlokat, általában a szöveges fájlokat tartalmazó lemez könyvtárba. A konfigurációs csomag könyvtárában található fájlokat a szolgáltatás típusa által hivatkozott `ServiceManifest.xml` fájlt. Egy elnevezett szolgáltatás létrehozásakor a fájlokat a konfigurációs csomag másolt egy vagy több csomópontot kijelölve elnevezett szolgáltatás futtatásához. Ezután a kód futtatásához, és most már hozzáféréssel a konfigurációs fájlok is elindul.

**Tárolók**: Alapértelmezés szerint a Service Fabric üzembe helyezi, és aktiválja a szolgáltatást folyamatokat is. A Service Fabric is szolgáltatást is üzembe helyezhet tárolórendszerképeket a. Tárolók egy virtualizációs technológia, amely Virtualizálja az alapul szolgáló operációs rendszer alkalmazások. Egy alkalmazás és a futtatókörnyezet, a függőségeket és a könyvtárakat futtassa a tárolókon belül. A tároló teljes, az operációs rendszer szerkezeteket tároló saját elkülönített nézete privát hozzáféréssel rendelkezik. Service Fabric támogatja a Docker-tárolók a Linux és Windows Server-tárolókat. További információkért olvassa el [Service Fabric és a tárolók](service-fabric-containers-overview.md).

**Partícióséma**: Egy elnevezett szolgáltatás létrehozásakor meg kell adnia egy partícióséma. Szolgáltatások állapota jelentős mennyiségű partíciókon, amely a fürtcsomópontok között osztja el a az állapot felosztani az adatokat. Az adatok partíciók közötti felosztásával az elnevezett szolgáltatás állapota is méretezhető. Egy partíción belül elnevezett állapotmentes szolgáltatások példányban, mivel az állapotalapú szolgáltatások nevű replikával rendelkeznek. Általában az elnevezett állapotmentes szolgáltatások csak egyetlen partícióval rendelkezik, mert az nem belső állapot. Adja meg a partíció-példányok rendelkezésre állását. Ha egy példány nem sikerül, más példányra továbbra is a szokott, és ekkor a Service Fabric létrehoz egy új példányt. Az állapotalapú szolgáltatások nevű karbantartása, a replikák és minden egyes partíción belül van a saját replika, hogy szinkronban tartani az állapot. Kell egy replika meghibásodik, a Service Fabric a meglévő replikák új replikát hoz létre.

Olvassa el a [Partition Service Fabric reliable services](service-fabric-concepts-partitioning.md) cikkben további információt.

## <a name="system-services"></a>Rendszer-szolgáltatások
Nincsenek rendszerszolgáltatások minden fürt jönnek létre, amelyek a Service Fabric platform képességeit.

**A Naming Service**: Minden Service Fabric-fürt rendelkezik egy elnevezési szolgáltatás, amely egy olyan helyre, a fürt oldja fel a szolgáltatásneveket. Kezelheti a szolgáltatásnevek és a tulajdonságok, például egy internetes tartomány tartománynévrendszer (DNS) a fürt. Az ügyfelek biztonságosan kommunikálnak a fürt bármely csomópontjának feloldani a szolgáltatás nevét és helyét az elnevezési szolgáltatás használatával. Alkalmazások a fürtön belüli áthelyezéséhez. Például ez lehet hibák, a terheléselosztás erőforrás vagy a fürt átméretezése miatt. Szolgáltatások és az ügyfelek számára, hogy az aktuális hálózati helyét fejleszthet. Az ügyfelek a tényleges gép IP-cím és port, ahol éppen fut szerezze be.

Olvasási [kommunikáció a szolgáltatások](service-fabric-connect-and-communicate-with-services.md) további információ az ügyfél és a szolgáltatás kommunikációs API-k, amelyek együttműködnek az elnevezési szolgáltatásban.

**Kép Store szolgáltatás**: Minden Service Fabric-fürt rendelkezik egy kép Store szolgáltatás, ahol őrzi meg a központilag telepített, verziójú alkalmazáscsomagok. Alkalmazáscsomag átmásolása a lemezkép Store, és regisztrálhatja a az alkalmazás típusát, a virtuálisalkalmazás-csomag található. Után az alkalmazás típusát ki van építve, hozzon létre egy elnevezett alkalmazást belőle. Után minden elnevezett alkalmazást, hogy törölték a Store kép szolgáltatásból alkalmazástípust regisztrációját is.

Olvasási [az ImageStoreConnectionString beállítás](service-fabric-image-store-connection-string.md) Store kép szolgáltatásról további információt.

Olvassa el a [alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md) cikk további tájékoztatást a Store kép Service-alkalmazások üzembe helyezéséhez.

**Feladatátvevőfürt-kezelő szolgáltatás**: Minden Service Fabric-fürt rendelkezik egy Feladatátvevőfürt-kezelő szolgáltatás, amely az a következő műveleteket:
   - Magas rendelkezésre állás és konzisztencia-szolgáltatások kapcsolódó funkciókat hajtja végre.
   - Alkalmazás és a fürt frissítéseket koordinálja.
   - Egyéb rendszerösszetevők kommunikál.

**Service Manager javítást igényel**: Ez az egy választható rendszerszolgáltatás, amely lehetővé teszi, hogy a javítási műveleteket végezni egy fürtön, amely biztonságos, eligazítót és transzparens módon. A javításkezelő használatban van:
   - Az Azure karbantartásához kijavítja [Silver és Gold tartóssági](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Azure Service Fabric-fürtök.
   - A javítási műveleteket végző [Patch Orchestration Application](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Üzembe helyezés és az alkalmazás modellek 

A szolgáltatások üzembe helyezéséhez meg kell ismertetik, hogyan kell futtatni. Service Fabric támogatja a három különböző üzemi modellek:

### <a name="resource-model-preview"></a>Erőforrás-modellje (előzetes verzió)
Service Fabric-erőforrások, amelyeket a Service Fabric; külön-külön telepíthető beleértve az alkalmazások, szolgáltatások, hálózatok és köteteket. Erőforrások JSON-fájlok, amelyen keresztül a fürt azon végpontján használatával vannak meghatározva.  A Service Fabric tervezhetők az Azure erőforrás-modellje séma használnak. Egy YAML-fájl séma is segítségével könnyebben hozhat létre a definíciós fájlokat. Erőforrások helyezhetők, bárhol a Service Fabric futtatja. Az erőforrás-modellje ismertetik a Service Fabric-alkalmazások legegyszerűbb módja. Egyszerű üzembe helyezését és felügyeletét a tárolóalapú szolgáltatás a fő célja van. További tudnivalókért olvassa el a [Bevezetés a Service Fabric Erőforrásmodell](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Natív modell
A natív alkalmazás modellt biztosít az alkalmazások teljes alacsony szintű hozzáféréssel rendelkező Service Fabric. A regisztrált típusok jegyzékfájlok XML az alkalmazások és szolgáltatások vannak meghatározva.

A natív modell támogatja a Reliable Services és Reliable Actors-keretrendszerek esetén biztosít hozzáférést a Service Fabric-futtatókörnyezet API-k és a fürt kezelése a C# és Java API-k. A natív modell támogatja a tetszőleges tárolók és a végrehajtható fájlokat is. A natív modell nem támogatott a [Service Fabric-háló környezet](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**A Reliable Services**: API-állapot nélküli és állapotalapú szolgáltatások létrehozásához. Állapotalapú szolgáltatások állapotuk a Reliable Collections például egy szótár, illetve a várólista tárolásához. Különböző kommunikációs sablonjaitokat, például a webes API-t és a Windows Communication Foundation (WCF) is csatlakoztatható.

**A Reliable Actors**: API-t a virtuális Actors programozási modellel állapot nélküli és állapotalapú objektumokat hozhat létre. Ez a modell akkor hasznos, ha a számítási vagy állam független egység számos lehetősége van. Ez a modell fordulókra szálkezelési modelljét, használ, így a legjobb, mert egy egyedi szereplő összes kimenő kérelem befejezéséig nem tudja feldolgozni egyéb bejövő kérelmek más actors vagy a szolgáltatások meghívó kód elkerülése érdekében.

A meglévő alkalmazások Service Fabric is futtathatja:

**Tárolók**:  A Service Fabric támogatja a Hyper-V elkülönítési módban a Windows Server 2016-ban a Linux és Windows Server tárolókat a Docker-tárolók telepítését támogatja. A Service fabric [alkalmazásmodell](service-fabric-application-model.md), egy tároló-alkalmazásgazda, mely több szolgáltatásban replikák kerülnek jelöli. A Service Fabric futtathat tárolókat, és a forgatókönyv hasonlít a Vendég végrehajtható forgatókönyv, ahol becsomagolását, hogy egy meglévő alkalmazást a tárolón belül. Emellett akkor is [futtassa a Service Fabric tárolókon belüli szolgáltatások](service-fabric-services-inside-containers.md) is.

**Futtatható vendégalkalmazás**: Bármilyen, Node.js, Java, C++ kódot az Azure Service Fabric szolgáltatásként is futtathatja. Ilyen típusú szolgáltatások Vendég végrehajtható fájlok, amelyeket állapotmentes szolgáltatások kell kezelni, a Service Fabric hivatkozik. Előnye az, hogy a Vendég végrehajtható, a Service Fabric-fürtön futó magas rendelkezésre állás, a Szolgáltatásállapot-figyelést, alkalmazáséletciklus-kezelésre, nagy kapacitású és felderíthetőség tartalmazza.

Olvassa el a [a szolgáltatás programozási modell választása](service-fabric-choose-framework.md) cikkben további információt.

### <a name="docker-compose"></a>Docker Compose 
[A docker Compose](https://docs.docker.com/compose/) része a Docker-projekt. A Service Fabric biztosít korlátozottan támogatja az [üzembe helyezése a Docker Compose modellt használó alkalmazások](service-fabric-docker-compose.md).

## <a name="environments"></a>Környezetek

Service Fabric, amely egy nyílt forráskódú platform technológia, amely számos különböző szolgáltatások és termékek alapján. A Microsoft az alábbi lehetőségeket kínálja:

 - **Az Azure Service Fabric háló**: Egy teljes körűen felügyelt szolgáltatás, a Microsoft Azure Service Fabric-alkalmazások futtatásához.
 - **Az Azure Service Fabric**: Az Azure-ban üzemeltetett Service Fabric-fürt ajánlat. A Service Fabric és az Azure-infrastruktúrát, és a Service Fabric-fürtök frissítése és a konfigurációkezelés integrációjával biztosítja.
 - **Önálló Service Fabric**: A telepítés és konfigurálás eszközöket [bárhol a Service Fabric-fürtök üzembe helyezése](/azure/service-fabric/service-fabric-deploy-anywhere) (helyszíni vagy bármely más szolgáltatónál). Az Azure nem kezeli.
 - **Service Fabric fejlesztési fürtöt**: Helyi fejlesztési élményt nyújt a Windows, Linux vagy Mac a Service Fabric-alkalmazások fejlesztését.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Környezet, keretrendszer és üzembe helyezési modell támogatási mátrix
Különböző környezetek eltérő szintű támogatást keretrendszerek és üzembe helyezési modellel rendelkezik. A következő táblázat ismerteti a támogatott keretrendszer és az üzembe helyezési modellt kombinációját.

| Az alkalmazás | Leírtak szerint | Az Azure Service Fabric háló | Az Azure Service Fabric-fürtök (bármely operációs rendszeren)| Helyi fürt | Önálló fürt |
|---|---|---|---|---|---|
| Service Fabric-háló alkalmazások | Resource Model (YAML & JSON) | Támogatott |Nem támogatott | Windows – támogatott, Linux és Mac-nem támogatott | Windows – nem támogatott |
|Service Fabric natív alkalmazások | Native Application Model (XML) | Nem támogatott| Támogatott|Támogatott|Windows – támogatott|

A következő táblázat ismerteti a különböző alkalmazás-modellek és azokat az eszközöket, hogy a Service Fabric elleni őket létezik.

| Az alkalmazás | Leírtak szerint | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric-háló alkalmazások | Resource Model (YAML & JSON) | VS 2017 |Nem támogatott |Nem támogatott | Támogatott – csak a háló környezet | Nem támogatott|
|Service Fabric natív alkalmazások | Native Application Model (XML) | VS 2017 és a VS 2015| Támogatott|Támogatott|Támogatott|Támogatott|

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
További információ a Service Fabric:

* [Service Fabric áttekintése](service-fabric-overview.md)
* [Miért érdemes a mikroszolgáltatás-alapú megközelítést választani alkalmazások létrehozásához?](service-fabric-overview-microservices.md)
* [Alkalmazáshasználati helyzetek](service-fabric-application-scenarios.md)

További információ a Service Fabric-háló:

* [Service Fabric háló áttekintése](/azure/service-fabric-mesh/service-fabric-mesh-overview)
