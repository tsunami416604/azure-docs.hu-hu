---
title: Az Azure Service Fabric terminológiájának megismerése | Microsoft Docs
description: A Service Fabric terminológiai áttekintése. A dokumentáció további részében használt legfontosabb terminológiai fogalmakat és kifejezéseket tárgyalja.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/17/2018
ms.author: masnider
ms.openlocfilehash: 82672114722db843fcb5d0bdff28cf14cddb1aef
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68811923"
---
# <a name="service-fabric-terminology-overview"></a>Service Fabric terminológia áttekintése
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését.  A [Service Fabric fürtöket bárhová](service-fabric-deploy-anywhere.md)is üzemeltetheti: Azure, helyszíni adatközpontban vagy bármely felhőalapú szolgáltatón.  Service Fabric a Orchestrator, amely az [Azure Service Fabric Mesh](/azure/service-fabric-mesh)szolgáltatást használja. Bármilyen keretrendszert felhasználhat a szolgáltatások írásához, és kiválaszthatja, hogy az alkalmazás hol fusson több környezeti lehetőség közül. Ez a cikk az Service Fabric által használt terminológiát részletezi a dokumentációban használt kifejezések megismerése érdekében.

## <a name="infrastructure-concepts"></a>Infrastruktúra-fogalmak
**Fürt**: Olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait.  A fürtök több ezer gépre skálázhatók.

**Csomópont**: Egy fürt részét képező gépet vagy virtuális gépet csomópontnak nevezzük. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). A csomópontok jellemzői, például elhelyezési tulajdonságok. Minden számítógép vagy virtuális gép rendelkezik egy automatikus indítású Windows- `FabricHost.exe`szolgáltatással, amely elindítja a rendszert a rendszerindítás után, majd `Fabric.exe` elindítja `FabricGateway.exe`a két végrehajtható fájl: és. Ez a két végrehajtható fájl alkotja a csomópontot. Tesztelési helyzetekben több csomópontot is tárolhat egyetlen gépen vagy virtuális gépen a `Fabric.exe` és `FabricGateway.exe`a több példányának futtatásával.

## <a name="application-and-service-concepts"></a>Alkalmazások és szolgáltatások – fogalmak

**Service Fabric Mesh-alkalmazás**: Service Fabric Mesh-alkalmazásokat az erőforrás-modell (YAML és JSON-erőforrás fájlok) írják le, és bármely olyan környezetben üzembe helyezhetők, ahol Service Fabric fut.

**Natív alkalmazás Service Fabric**: Service Fabric natív alkalmazásokat a natív alkalmazás modelljében (XML-alapú alkalmazás-és szolgáltatás-jegyzékfájlok) ismertetjük.  Service Fabric natív alkalmazások nem futtathatók Service Fabric hálóban.

### <a name="service-fabric-mesh-application-concepts"></a>Service Fabric Mesh alkalmazással kapcsolatos fogalmak

**Alkalmazás**: Az alkalmazás a háló alkalmazás üzembe helyezésének, verziószámozásának és élettartamának egysége. Az egyes alkalmazás-példányok életciklusa egymástól függetlenül kezelhető.  Az alkalmazások egy vagy több szolgáltatási kódú csomagból és beállításból állnak. Az alkalmazás az Azure Resource Model (RM) séma használatával van definiálva.  A szolgáltatások az alkalmazás-erőforrás tulajdonságaiként szerepelnek egy RM-sablonban.  Az alkalmazás által használt hálózatokat és köteteket az alkalmazás hivatkozik.  Egy alkalmazás létrehozásakor az alkalmazás, a szolgáltatás, a hálózat és a kötet (ek) modellezése a Service Fabric Resource Model használatával történik.

**Szolgáltatás**: Az alkalmazás egyik szolgáltatása a szolgáltatást jelöli, és teljes és önálló funkciót hajt végre. Minden szolgáltatás egy vagy több olyan kódból áll, amely leírja mindazt, ami a programkódhoz tartozó tároló rendszerképének futtatásához szükséges.  Az alkalmazásokban lévő szolgáltatások száma növelhető és leállítható.

**Hálózat**: A hálózati erőforrások létrehoznak egy magánhálózati hálózatot az alkalmazásaihoz, és függetlenek a rá hivatkozó alkalmazásoktól és szolgáltatásoktól. A különböző alkalmazások több szolgáltatása is lehet ugyanazon hálózat része. A hálózatok olyan telepíthető erőforrások, amelyekre az alkalmazások hivatkoznak.

**Kód csomagja**: A kód csomagjai ismertetik a programkódhoz társított tároló rendszerkép futtatásához szükséges összes műveletet, beleértve a következőket:

* Tároló neve, verziója és beállításjegyzéke
* Az egyes tárolók számára szükséges CPU-és memória-erőforrások
* Hálózati végpontok
* A tárolóban csatlakoztatni kívánt kötetek, amelyek egy különálló kötet erőforrására hivatkoznak.

Az alkalmazás-erőforrás részeként definiált összes kód csomag egy csoportként van telepítve és aktiválva.

**Kötet**: A kötetek olyan könyvtárak, amelyek az állapot megőrzéséhez használható tároló-példányokon belül vannak csatlakoztatva. A Azure Files kötet-illesztőprogram egy Azure Files-megosztást csatlakoztat egy tárolóhoz, és megbízható adattárolást biztosít bármely olyan API-n keresztül, amely támogatja a file Storage-t. A kötetek olyan telepíthető erőforrások, amelyekre az alkalmazások hivatkoznak.

### <a name="service-fabric-native-application-concepts"></a>Natív alkalmazás-fogalmak Service Fabric

**Alkalmazás**: Az alkalmazások olyan összetevő-szolgáltatások gyűjteményei, amelyek egy adott függvényt vagy függvényt hajtanak végre. Az egyes alkalmazás-példányok életciklusa egymástól függetlenül kezelhető.

**Szolgáltatás**: A szolgáltatás teljes és önálló funkciót hajt végre, és a többi szolgáltatástól függetlenül is elindítható és futtatható. A szolgáltatás kód, konfiguráció és adatmennyiségből áll. Minden egyes szolgáltatás esetében a kód a végrehajtható fájlokból tevődik össze, a konfiguráció pedig olyan szolgáltatás-beállításokból áll, amelyek futáskor tölthetők be, és az adatmennyiség a szolgáltatás által felhasználható tetszőleges statikus információból áll.

**Alkalmazás típusa**: A szolgáltatás típusú gyűjteményhez rendelt név/verzió. Egy `ApplicationManifest.xml` fájlban van definiálva, és beágyazva van egy alkalmazáscsomag-könyvtárba. Ezután a rendszer átmásolja a könyvtárat a Service Fabric-fürt rendszerkép-tárolójába. Ezután létrehozhat egy elnevezett alkalmazást ebből az alkalmazásból a fürtön belül.

További információért olvassa el az [alkalmazás modelljéről](service-fabric-application-model.md) szóló cikket.

**Alkalmazáscsomag**: Az alkalmazás típusának `ApplicationManifest.xml` fájlját tartalmazó lemez könyvtára. Az egyes szolgáltatástípus-típusokhoz tartozó szolgáltatási csomagokra hivatkozik. Az alkalmazáscsomag könyvtárában lévő fájlokat a rendszer átmásolja Service Fabric fürt rendszerkép-tárolójába. Egy e-mail-alkalmazás-típushoz tartozó alkalmazáscsomag például tartalmazhat egy üzenetsor-szolgáltatási csomagra, egy előtér-szolgáltatási csomagra és egy adatbázis-szolgáltatási csomagra mutató hivatkozásokat.

**Elnevezett alkalmazás**: Egy alkalmazáscsomag a rendszerkép-tárolóba való másolása után létrehozza az alkalmazás egy példányát a fürtön belül. Hozzon létre egy példányt, amikor megadja az alkalmazáscsomag alkalmazásának típusát a nevét vagy verzióját használva. Minden Application Type-példányhoz egy egységes erőforrás-azonosító (URI) neve van rendelve, `"fabric:/MyNamedApp"`amely a következőhöz hasonló:. Egy fürtön belül több névvel ellátott alkalmazást is létrehozhat egyetlen alkalmazás típusból. Elnevezett alkalmazásokat is létrehozhat különböző típusú alkalmazásokból. Az elnevezett alkalmazások felügyelete és verziója egymástól függetlenül történik.

**Szolgáltatás típusa**: A szolgáltatás kódjához, adatcsomagjaihoz és konfigurációs csomagjaihoz rendelt név/verzió. A szolgáltatás típusa a `ServiceManifest.xml` fájlban van definiálva, és a szervizcsomag könyvtárába van beágyazva. Ezt követően egy alkalmazáscsomag `ApplicationManifest.xml` fájlja hivatkozik a szolgáltatáscsomag könyvtárára. A fürtön belül egy elnevezett alkalmazás létrehozása után létrehozhat egy elnevezett szolgáltatást az alkalmazás egyik szolgáltatástípus-típusáról. A szolgáltatás típusa `ServiceManifest.xml` fájlja ismerteti a szolgáltatást.

További információért olvassa el az [alkalmazás modelljéről](service-fabric-application-model.md) szóló cikket.

A szolgáltatások két típusa létezik:

* **Állapot nélküli**: Állapot nélküli szolgáltatás használata, ha a szolgáltatás állandó állapota egy külső tárolási szolgáltatásban (például Azure Storage, Azure SQL Database vagy Azure Cosmos DB) van tárolva. Állapot nélküli szolgáltatás használata, ha a szolgáltatás nem rendelkezik állandó tárterülettel. Például egy olyan számológép-szolgáltatás esetében, amely értékeket ad át a szolgáltatásnak, egy olyan számítást hajt végre, amely ezeket az értékeket használja, majd visszaadja az eredményt.
* **Állapot-nyilvántartó**: Akkor használjon állapot-nyilvántartó szolgáltatást, ha azt szeretné, hogy a Service Fabric felügyelje a szolgáltatás állapotát a megbízható gyűjtemények vagy Reliable Actors programozási modellek segítségével. Névvel ellátott szolgáltatás létrehozásakor adja meg, hogy hány partíciót kíván átosztani a méretezhetőség érdekében. Azt is adja meg, hogy hányszor replikálja az állapotot a csomópontok között a megbízhatóság érdekében. Mindegyik elnevezett szolgáltatás egyetlen elsődleges replikával és több másodlagos replikával rendelkezik. Az elnevezett szolgáltatás állapotát az elsődleges replikára való íráskor kell módosítania. Service Fabric ezután replikálja ezt az állapotot az összes másodlagos replikára, hogy az állapota szinkronban maradjon. Service Fabric automatikusan észleli, ha egy elsődleges replika meghibásodik, és egy meglévő másodlagos replikát előléptet egy elsődleges replikára. Service Fabric Ezután létrehoz egy új másodlagos replikát.  

A **replikák vagy példányok** egy központilag telepített és futtatott szolgáltatás kódja (és állapot-nyilvántartó szolgáltatások állapota). Lásd: [replikák és példányok](service-fabric-concepts-replica-lifecycle.md).

Az újrakonfigurálás a szolgáltatás replikájának bármely változásának folyamatát jelenti. Lásd [](service-fabric-concepts-reconfiguration.md): újrakonfigurálás.

**Szolgáltatáscsomag**: A szolgáltatási típus `ServiceManifest.xml` fájlját tartalmazó lemez könyvtára. Ez a fájl a szolgáltatás típusának kódját, statikus adatait és konfigurációs csomagjaira hivatkozik. A szolgáltatási csomag könyvtárában lévő fájlokat az alkalmazás típusa `ApplicationManifest.xml` fájlja hivatkozik. A szervizcsomag például hivatkozhat a kód, a statikus és az adatbázis-szolgáltatást alkotó konfigurációs csomagokra.

**Nevesített szolgáltatás**: Elnevezett alkalmazás létrehozása után létrehozhat egy, a fürtön belüli egyik szolgáltatási típusának egy példányát. A szolgáltatás típusát a neve/verziója alapján adja meg. Minden egyes szolgáltatástípus-példányhoz hozzá van rendelve egy URI-név, amely az elnevezett alkalmazás URI-ja alá tartozik. Ha például létrehoz egy "MyDatabase" nevű szolgáltatást egy "MyNamedApp" nevű alkalmazásban, az URI a következőhöz hasonlít: `"fabric:/MyNamedApp/MyDatabase"`. Egy elnevezett alkalmazásban több nevesített szolgáltatást is létrehozhat. Mindegyik elnevezett szolgáltatás rendelkezhet saját partíciós sémával, valamint a példány vagy a replika számával.

**Kód csomagja**: A szolgáltatástípus végrehajtható fájljait (általában EXE/DLL fájlokat) tartalmazó lemez könyvtára. A Kódszerkesztő könyvtárában lévő fájlokat a szolgáltatás típusa `ServiceManifest.xml` fájlja hivatkozik. Névvel ellátott szolgáltatás létrehozásakor a rendszer átmásolja a kódot a csomópontra vagy a kijelölt csomópontokra a nevesített szolgáltatás futtatásához. Ezután elindul a kód futtatása. A kód csomag végrehajtható fájljainak két típusa létezik:

* **Vendég végrehajtható fájlok**: A-t futtató végrehajtható fájlok a gazdagép operációs rendszerén (Windows vagy Linux) futnak. Ezek a végrehajtható fájlok nem hivatkoznak Service Fabric futtatókörnyezeti fájlokra, ezért nem használnak Service Fabric programozási modelleket. Ezek a végrehajtható fájlok nem tudják használni a Service Fabric szolgáltatásait, például az elnevezési szolgáltatást a végpontok felderítéséhez. A vendég végrehajtható fájlok nem tudják jelenteni az egyes szolgáltatási példányokra vonatkozó betöltési metrikákat.
* A **Service Host végrehajtható fájljai**: Service Fabric programozási modelleket használó végrehajtható fájlok Service Fabric futtatókörnyezeti fájlokhoz való csatolással, Service Fabric funkciók engedélyezésével. Az elnevezett szolgáltatási példányok például regisztrálhatják a végpontokat Service Fabric elnevezési szolgáltatás, és jelenthetik be a terhelési metrikákat is.

**Adatcsomag**: A szolgáltatás típusának statikus, csak olvasható adatfájljait, jellemzően fénykép-, hang-és videofájlokat tartalmazó lemez könyvtára. Az adatcsomag könyvtárában lévő fájlokat a szolgáltatás típusa `ServiceManifest.xml` fájlja hivatkozik. Elnevezett szolgáltatás létrehozásakor a rendszer átmásolja az adatcsomagot a csomópontra vagy a kijelölt csomópontokra a nevesített szolgáltatás futtatásához. A kód elindul, és most már elérheti az adatfájlokat.

**Konfigurációs csomag**: A szolgáltatás típusának statikus, csak olvasható konfigurációs fájljait, általában szöveges fájlokat tartalmazó lemez könyvtára. A konfigurációs csomag könyvtárában lévő fájlokat a szolgáltatás típusa `ServiceManifest.xml` fájlja hivatkozik. Névvel ellátott szolgáltatás létrehozásakor a konfigurációs csomagban lévő fájlok egy vagy több, a nevesített szolgáltatás futtatására kijelölt csomópontot másolnak. Ezután elindul a kód futtatása, és most már elérheti a konfigurációs fájlokat.

**Tárolók**: Alapértelmezés szerint a Service Fabric folyamatokként telepíti és aktiválja a szolgáltatásokat. A Service Fabric a szolgáltatásokat tároló lemezképekben is üzembe helyezheti. A tárolók olyan virtualizációs technológiák, amelyek a mögöttes operációs rendszert virtualizálják az alkalmazásokból. Egy alkalmazás és annak futtatókörnyezete, függőségei és rendszerkönyvtárai egy tárolón belül futnak. A tároló teljes körű, privát hozzáféréssel rendelkezik az operációs rendszer szerkezetének saját elkülönített nézetéhez. A Service Fabric támogatja a Docker-tárolókat a Linux-és Windows Server-tárolókban. További információért olvassa el a [Service Fabric és](service-fabric-containers-overview.md)a tárolókat.

**Partíciós séma**: Elnevezett szolgáltatás létrehozásakor meg kell adnia egy partíciós sémát. A nagy mennyiségű állapotú szolgáltatások felosztják az adatpartíciók közötti elosztást, amely az állapotot a fürt csomópontjain keresztül terjeszti. Az adatpartíciók közötti felosztásával az elnevezett szolgáltatás állapota méretezhető is lehet. Egy partíción belül az állapot nélküli elnevezett szolgáltatások rendelkeznek példányokkal, míg az állapot-nyilvántartó nevesített szolgáltatások rendelkeznek replikákkal. Általában az állapot nélküli elnevezett szolgáltatások csak egy partícióval rendelkeznek, mert nincs belső állapotuk. A partíciós példányok rendelkezésre állást biztosítanak. Ha egy példány meghibásodik, más példányok továbbra is szabályosan működnek, majd Service Fabric új példányt hoz létre. Az állapot-nyilvántartó elnevezett szolgáltatások megőrzik az állapotukat a replikákban, és mindegyik partíció saját replikával rendelkezik, így az állapot szinkronban marad. Ha egy replika meghibásodik, Service Fabric új replikát hoz létre a meglévő replikák közül.

További információért olvassa el a [Partition Service Fabric megbízható szolgáltatások](service-fabric-concepts-partitioning.md) című cikket.

## <a name="system-services"></a>Rendszerszolgáltatások
Az Service Fabric platform képességeit biztosító minden fürtben létrejönnek rendszerszolgáltatások.

**Elnevezési szolgáltatás**: Minden Service Fabric-fürthöz tartozik egy elnevezési szolgáltatás, amely a fürt egyik helyére oldja fel a szolgáltatás nevét. A szolgáltatás nevét és tulajdonságait, például a fürt internetes tartománynevét (DNS) kezelheti. Az ügyfelek a elnevezési szolgáltatás használatával biztonságosan kommunikálhatnak a fürt bármely csomópontjára, és feloldják a szolgáltatás nevét és helyét. Az alkalmazások a fürtön belül mozognak. Ez lehet például hibák, erőforrás-kiegyensúlyozás vagy a fürt átméretezése. Olyan szolgáltatásokat és ügyfeleket fejleszthet, amelyek az aktuális hálózati helyet oldják fel. Az ügyfelek megkapják az aktuálisan futó számítógép IP-címét és portját.

A szolgáltatással folytatott kommunikációval kapcsolatos további információkért olvassa el elnevezési szolgáltatás a [szolgáltatások közötti kommunikációt](service-fabric-connect-and-communicate-with-services.md) ismertető témakört.

**Lemezképtároló szolgáltatás**: Mindegyik Service Fabric-fürt rendelkezik egy lemezképtároló szolgáltatással, ahol a rendszer telepíti a verzióval ellátott alkalmazáscsomag-csomagokat. Másolja az alkalmazáscsomag lemezképtároló, majd regisztrálja az adott alkalmazáscsomag keretében található alkalmazás típusát. Az alkalmazás típusának kiépítés után létre kell hoznia egy elnevezett alkalmazást. A lemezképtároló szolgáltatásból az összes megnevezett alkalmazás törlése után törölheti az alkalmazás típusát.

A lemezképtároló szolgáltatással kapcsolatos további információkért olvassa el [a ImageStoreConnectionString-beállítás](service-fabric-image-store-connection-string.md) ismertetése című témakört.

Az alkalmazások lemezképtároló szolgáltatásra történő központi telepítésével kapcsolatos további információkért olvassa el az [alkalmazás központi telepítése](service-fabric-deploy-remove-applications.md) című cikket.

**Feladatátvételi felügyelő szolgáltatás**: Minden Service Fabric-fürt rendelkezik egy Feladatátvételi felügyelő szolgáltatással, amely a következő műveletekért felelős:
   - A szolgáltatások magas rendelkezésre állásával és konzisztenciájával kapcsolatos funkciókat hajt végre.
   - Az alkalmazás-és fürt frissítéseinek összehangolása.
   - Interakció más rendszerösszetevőkkel.

**Javításkezelő szolgáltatás**: Ez egy opcionális rendszerszolgáltatás, amely lehetővé teszi a javítási műveletek végrehajtását a fürtön a biztonságos, automatizálható és átlátható módon. A Repair Manager használatban van:
   - Az Azure karbantartási javításait [ezüst és arany tartósságú](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Azure Service Fabric-fürtökön végezheti el.
   - Javítási műveletek végrehajtása [javítás](service-fabric-patch-orchestration-application.md) -előkészítési alkalmazáshoz

## <a name="deployment-and-application-models"></a>Üzembe helyezési és alkalmazási modellek 

A szolgáltatások üzembe helyezéséhez le kell írnia a futtatásuk módját. A Service Fabric három különböző üzembe helyezési modellt támogat:

### <a name="resource-model-preview"></a>Erőforrás-modell (előzetes verzió)
Service Fabric az erőforrások bármilyen módon üzembe helyezhetők, és a Service Fabric; beleértve az alkalmazásokat, a szolgáltatásokat, a hálózatokat és a köteteket. Az erőforrások egy JSON-fájllal vannak definiálva, amely a fürt végpontján helyezhető üzembe.  Service Fabric Mesh esetében az Azure Resource Model-séma használatos. A YAML-sémák a definíciós fájlok egyszerűbb létrehozásához is használhatók. Az erőforrások bármikor üzembe helyezhetők, Service Fabric futtatva. Az erőforrás-modell az Service Fabric-alkalmazások leírásának legegyszerűbb módja. Fő témája a tárolós szolgáltatások egyszerű üzembe helyezése és kezelése. További információért olvassa el [a Service Fabric Resource Model bemutatása](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)című témakört.

### <a name="native-model"></a>Natív modell
A natív alkalmazás modellje lehetővé teszi, hogy az alkalmazások teljes, alacsony szintű hozzáférést biztosítson Service Fabrichoz. Az alkalmazások és szolgáltatások regisztrált típusokként vannak definiálva az XML-jegyzékfájlokban.

A natív modell támogatja a Reliable Services és Reliable Actors keretrendszereket, amelyek hozzáférést biztosítanak a Service Fabric Runtime API-khoz és a Java C# -ban a fürt felügyeleti API-khoz. A natív modell szintén támogatja a tetszőleges tárolókat és végrehajtható fájlokat. A natív modell nem támogatott a [Service Fabric Mesh környezetben](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Reliable Services**: Egy API az állapot nélküli és állapot-nyilvántartó szolgáltatások létrehozásához. Az állapot-nyilvántartó szolgáltatások megbízható gyűjteményekben, például szótárban vagy várólistában tárolják az állapotukat. Különböző kommunikációs stackeket is csatlakoztathat, például a webes API-t és a Windows Communication Foundation (WCF).

**Reliable Actors**: Egy API, amely állapot nélküli és állapot-nyilvántartó objektumokat hoz létre a virtuális színész programozási modelljén keresztül. Ez a modell akkor hasznos, ha sok független számítási vagy állapotú egység van. Ez a modell egy körökön alapuló szálat használ, ezért érdemes elkerülni a más szereplőkkel vagy szolgáltatásokkal kezdeményezett kódokat, mert egy adott színész nem tudja feldolgozni a többi bejövő kérelmet, amíg az összes kimenő kérése be nem fejeződik.

A meglévő alkalmazásokat Service Fabric is futtathatja:

**Tárolók**:  Service Fabric támogatja a Docker-tárolók telepítését a Linux-és Windows Server-tárolókban a Windows Server 2016 rendszeren, és támogatja a Hyper-V elkülönítési módot. A Service Fabric [alkalmazás](service-fabric-application-model.md)-modellben egy tároló olyan alkalmazás-gazdagépet jelöl, amelyben több szolgáltatási replika van elhelyezve. Service Fabric futtathat bármilyen tárolót, és a forgatókönyv hasonló a vendég végrehajtható fájlhoz, ahol egy meglévő alkalmazást csomagolhat egy tárolóba. Emellett Service Fabric-szolgáltatásokat is [futtathat a tárolón belül](service-fabric-services-inside-containers.md) .

**Vendég végrehajtható fájlok**: Bármilyen típusú kódot futtathat, például a Node. js, a Python, a Java vagy C++ az Azure Service Fabric szolgáltatásként. Service Fabric az ilyen típusú szolgáltatásokra vonatkozik vendég végrehajtható fájlokként, amelyek állapot nélküli szolgáltatásként lesznek kezelve. A vendég végrehajtható fájlok Service Fabric-fürtön való futtatásának előnyei közé tartozik a magas rendelkezésre állás, az állapot figyelése, az alkalmazások életciklusának kezelése, a nagy sűrűség és a felderíthetőség.

További információért olvassa el a [szolgáltatási modell kiválasztása](service-fabric-choose-framework.md) a szolgáltatáshoz című cikket.

### <a name="docker-compose"></a>Docker Compose 
A Docker- [összeállítás](https://docs.docker.com/compose/) a Docker-projekt részét képezi. A Service Fabric korlátozott támogatást biztosít az [alkalmazások Docker-összeállítási modellel történő üzembe helyezéséhez](service-fabric-docker-compose.md).

## <a name="environments"></a>Környezetek

A Service Fabric egy nyílt forráskódú platform technológia, amelyet számos különböző szolgáltatás és termék alapul. A Microsoft a következő lehetőségeket biztosítja:

 - **Azure Service Fabric Mesh**: Teljes körűen felügyelt szolgáltatás Service Fabric alkalmazások Microsoft Azure-ban való futtatásához.
 - **Azure Service Fabric**: Az Azure által üzemeltetett Service Fabric-fürt ajánlata. Integrációt biztosít Service Fabric és az Azure-infrastruktúra között, valamint a Service Fabric-fürtök frissítésével és konfigurálásával.
 - **Önálló Service Fabric**: Telepítési és konfigurációs eszközök készlete, amelyekkel a [Service Fabric-fürtöket bárhol üzembe helyezheti](/azure/service-fabric/service-fabric-deploy-anywhere) (helyszíni vagy bármely felhőalapú szolgáltatón). Az Azure nem kezeli.
 - **Service Fabric fejlesztési fürt**: Helyi fejlesztési élményt nyújt Windows, Linux és Mac rendszereken Service Fabric alkalmazások fejlesztéséhez.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>A környezet, a keretrendszer és az üzembe helyezési modell támogatási mátrixa
A különböző környezetek különböző szintű támogatást biztosítanak a keretrendszerek és a telepítési modellek számára. A következő táblázat a támogatott keretrendszer-és telepítési modell-kombinációkat ismerteti.

| Alkalmazás típusa | Ismertette | Azure Service Fabric Mesh | Azure Service Fabric-fürtök (bármely operációs rendszer)| Helyi fürt | Önálló fürt |
|---|---|---|---|---|---|
| Service Fabric Mesh-alkalmazások | Erőforrás-modell (YAML & JSON) | Támogatott |Nem támogatott | Windows – támogatott, Linux és Mac – nem támogatott | Windows – nem támogatott |
|Natív alkalmazások Service Fabric | Natív alkalmazás modellje (XML) | Nem támogatott| Támogatott|Támogatott|Windows – támogatott|

A következő táblázat ismerteti a különböző alkalmazás-modelleket, valamint a Service Fabrichoz tartozó eszközöket.

| Alkalmazás típusa | Ismertette | Visual Studio | Eclipse | SFCTL | AZ PARANCSSORI FELÜLET | Powershell|
|---|---|---|---|---|---|---|
| Service Fabric Mesh-alkalmazások | Erőforrás-modell (YAML & JSON) | VS 2017 |Nem támogatott |Nem támogatott | Támogatott – csak Mesh környezet | Nem támogatott|
|Natív alkalmazások Service Fabric | Natív alkalmazás modellje (XML) | VS 2017 és VS 2015| Támogatott|Támogatott|Támogatott|Támogatott|

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
További információ a Service Fabricról:

* [A Service Fabric áttekintése](service-fabric-overview.md)
* [Miért érdemes a mikroszolgáltatás-alapú megközelítést választani alkalmazások létrehozásához?](service-fabric-overview-microservices.md)
* [Alkalmazáshasználati helyzetek](service-fabric-application-scenarios.md)

További információ a Service Fabric Rácsvonalról:

* [A Service Fabric Mesh áttekintése](/azure/service-fabric-mesh/service-fabric-mesh-overview)
