---
title: További tudnivalók az Azure Service Fabric terminológiája |} Microsoft Docs
description: A Service Fabric terminológia áttekintése. Kulcs terminológia fogalmakat és szakkifejezéseket tartalmazza a dokumentáció a többi használt ismerteti.
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
ms.date: 03/26/2018
ms.author: ryanwi
ms.openlocfilehash: e3da081f9b327031d6d1e0afd2f2fb52383bf933
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-terminology-overview"></a>A Service Fabric-terminológia áttekintése
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését. Ez a cikk részletesen tudni, hogy a kifejezés, amely a dokumentáció a Service Fabric által használt terminológiával.

A jelen szakaszban felsorolt fogalmakat is tárgyalja a következő videók a Microsoft Virtual Academy: <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">alapvető fogalmait</a>, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">tervezési idejű fogalmak</a>, és <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">futásidejű fogalmak</a>.

## <a name="infrastructure-concepts"></a>Infrastruktúrával kapcsolatos fogalmak
**Fürt**: virtuális vagy fizikai gépek, amelybe a mikroszolgáltatások telepíteni és felügyelni hálózathoz csatlakozó készlete.  A fürtök több ezer gépre skálázhatók.

**Csomópont**: egy számítógép vagy virtuális Gépet, amely egy fürt része nevezzük egy *csomópont*. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy karakterlánc). Csomópontok, hogy jellemzőkkel elhelyezési tulajdonságok. Minden számítógép vagy virtuális gép rendelkezik egy automatikusan induló Windows szolgáltatás `FabricHost.exe`, rendszerindító után elindul, és elindítja a két végrehajtható fájlok: `Fabric.exe` és `FabricGateway.exe`. E két végrehajtható fájlokat a csomópont alkotják. Tesztelési forgatókönyvek, tárolhatja, több csomópont egyetlen számítógép vagy virtuális gép több példányára futtatásával `Fabric.exe` és `FabricGateway.exe`.

## <a name="application-concepts"></a>Alkalmazás fogalmak
**Az alkalmazástípus**: hozzárendeli egy gyűjteményhez található szolgáltatástípusok neve/verziója. Meg van határozva egy `ApplicationManifest.xml` fájlt, és a beágyazott egy alkalmazás csomag könyvtárában. A könyvtár majd másolja a Service Fabric-fürt lemezképtárolóhoz. Ezután az alkalmazás típusa a fürtön belül létrehozhat egy elnevezett alkalmazást.

Olvassa el a [alkalmazásmodell](service-fabric-application-model.md) cikkében találja.

**Alkalmazáscsomag**: az alkalmazás típusának tartalmazó lemez könyvtár `ApplicationManifest.xml` fájlt. A service-csomagok az egyes szolgáltatástípusokról az alkalmazástípus alkotó hivatkozik. Service Fabric-fürt lemezképtárolóhoz vannak másolja a fájlokat, az alkalmazás csomag könyvtárában. Például egy alkalmazáscsomagot, az e-mailek alkalmazástípust tartalmazhat a várólista-szolgáltatáscsomagot, egy előtér-szolgáltatás csomag és egy adatbázis-szolgáltatás csomag mutató hivatkozásokat.

**Alkalmazás nevű**: az image store másol egy alkalmazáscsomagot, miután az alkalmazást a fürtön belül példányt létrehozni. Az alkalmazáscsomag alkalmazástípust, adja meg, annak neve vagy a verziójával példányt hoz létre. Minden alkalmazás típuspéldány hozzá van rendelve egy egységes erőforrás-azonosító (URI) neve, amely a következőhöz hasonló: `"fabric:/MyNamedApp"`. A fürtön belül több nevű alkalmazást is létrehozhat egyetlen alkalmazás típusból. Különböző típusok elnevezett alkalmazások is létrehozhat. Minden elnevezett alkalmazása felügyelt és a rendszerverzióval ellátott egymástól függetlenül.      

**Szolgáltatás típusa**: egy szolgáltatás kód csomagok, adatok csomagokat és konfigurációs csomagok rendelt neve/verziója. A szolgáltatás típus definiálva van a `ServiceManifest.xml` fájlt, és a beágyazott a service-csomag könyvtárban található. A service-csomag könyvtárában hivatkozik rá egy alkalmazáscsomagot `ApplicationManifest.xml` fájlt. A fürtön belül egy elnevezett alkalmazás létrehozása után létrehozhat egy adott szolgáltatást az alkalmazástípus szolgáltatás típusok egyikét. A szolgáltatás típusának `ServiceManifest.xml` fájl ismerteti a szolgáltatást.

Olvassa el a [alkalmazásmodell](service-fabric-application-model.md) cikkében találja.

A szolgáltatások két típusa van:

* **Állapot nélküli**: állapotmentes szolgáltatások használja, ha a szolgáltatás állandó állapotának külső storage szolgáltatásnak, például az Azure Storage, az Azure SQL Database vagy az Azure Cosmos DB van tárolva. Állapot nélküli szolgáltatást használ, ha a szolgáltatás nem állandó tároló. Például egy Számológép szolgáltatáshoz, ahol érték lett átadva a szolgáltatáshoz, egy számítási történik, amely ezeket az értékeket használja, és majd eredményt ad vissza.
* **Állapotalapú alkalmazások és szolgáltatások**: állapot-nyilvántartó szolgáltatást használ, ha azt szeretné, hogy a szolgáltatás állapotának a megbízható gyűjtemények vagy a Reliable Actors programozási modell használatával kezelheti a Service Fabric. Amikor létrehoz egy névvel ellátott szolgáltatást, adja meg, hány partíciókat méretezhetőséget szeretne a állapot eloszlatva. Is megadhatja, hány alkalommal replikálni az állapot, a megbízhatóság-csomópont között. Minden elnevezett szolgáltatásához tartozik egy elsődleges replikát, és több másodlagos replika. Ha az elsődleges másodpéldányhoz ír az adott szolgáltatás állapota módosítása. A Service Fabric replikálja ebben az állapotban a másodlagos replikák szinkronban tartsa az állapotát. A Service Fabric automatikusan észleli, ha egy elsődleges másodpéldány nem sikerül, és elősegíthető a egy létező másodlagos replika elsődleges replikára. A Service Fabric majd létrehoz egy új másodlagos replikára.  

**Replikák és példányok** kód (és állapotalapú szolgáltatások állapota) telepített szolgáltatások és futtatása. Lásd: [replikák és példányok](service-fabric-concepts-replica-lifecycle.md).

**Újrakonfigurálás** folyamata bármi is módosul a replika egy szolgáltatás hivatkozik. Lásd: [újrakonfigurálás](service-fabric-concepts-reconfiguration.md).

**Szolgáltatáscsomag**: a szolgáltatás típusának tartalmazó lemez könyvtár `ServiceManifest.xml` fájlt. Ez a fájl a kódot, a statikus adatok és a konfigurációs csomagok a service Type hivatkozik. A service-csomag könyvtár az alkalmazástípus által hivatkozott `ApplicationManifest.xml` fájlt. Például a szolgáltatáscsomagot utalhat a kódot, statikus adatok és egy adatbázis-szolgáltatás alkotó konfigurációs csomagokat.

**Nevű szolgáltatás**: után hoz létre egy elnevezett alkalmazást, a fürtön belül szolgáltatás típusa egy példányának hozhat létre. A szolgáltatás típusának annak neve/verziója használatával adja meg. Minden szolgáltatáspéldány típus hozzá van rendelve egy elnevezett alkalmazásának URI alatt hatókörű URI nevét. Például, ha létrehoz egy "adatbázis" nevű szolgáltatás belül "MyNamedApp" nevű alkalmazás, az URI a következőképpen néz: `"fabric:/MyNamedApp/MyDatabase"`. Egy elnevezett alkalmazáson belül több elnevezett szolgáltatások is létrehozhat. Minden elnevezett szolgáltatás is rendelkezik, saját partícióséma és a példány, vagy a replika száma.

**A kódcsomag**: végrehajtható fájlok a szolgáltatás típusa, általában az EXE/DLL-fájlok tartalmazó lemez könyvtár. A kód csomag könyvtár a szolgáltatás típusának által hivatkozott `ServiceManifest.xml` fájlt. Amikor létrehoz egy adott szolgáltatást, a kódcsomag a csomópont vagy az adott szolgáltatás futtatásához a kiválasztott csomópontok másolja. Ezt követően a kód elindul. A kód csomag végrehajtható két típusa van:

* **Vendég végrehajtható fájlok**: végrehajtható fájlok, amelyek futtató – a gazdagép operációs rendszeren (Windows vagy Linux). A végrehajtható fájlok nem csatolása vagy nem hivatkozhat a Service Fabric futásidejű fájlokat, és nem ezért nem használható a Service Fabric programozási modellek. A végrehajtható fájlok nem tudják használni az egyes Service Fabric-szolgáltatások, például a végpont felderítése elnevezési szolgáltatás. Vendég végrehajtható fájlok nem tud jelenteni minden szolgáltatáspéldány jellemző terhelés metrikákat.
* **Állomás végrehajtható fájlok szolgáltatás**: végrehajtható fájlok, amelyek a Service Fabric programozási modellek létrehozhatja, a Service Fabric-futtatókörnyezet fájlok, a Service Fabric-szolgáltatások engedélyezését. Például a megnevezett példánya végpontok regisztrálhatja a Service Fabric-szolgáltatás, és terhelési metrika is jelentheti.      

**Adatcsomag**: a szolgáltatás típusának statikus, csak olvasható adatok fájlokat, és általában fénykép, hang-, és videofájlok tartalmazó lemez könyvtár. Az adatok csomag könyvtár a szolgáltatás típusának által hivatkozott `ServiceManifest.xml` fájlt. Amikor létrehoz egy névvel ellátott szolgáltatást, az adatcsomag másolódik a csomópont egy vagy több kijelölt az adott szolgáltatás futtatásához. A kód elindul, és az adatfájlokat érhetők el.

**Konfigurációs csomag**: a szolgáltatás típusának statikus, csak olvasható konfigurációs fájlokat, általában szövegfájlok tartalmazó lemez könyvtár. A konfigurációs csomag könyvtár a szolgáltatás típusának által hivatkozott `ServiceManifest.xml` fájlt. Amikor létrehoz egy névvel ellátott szolgáltatást, a fájlokat a konfigurációs csomag másolt egy vagy több csomópontot kijelölve az adott szolgáltatás futtatásához. Ezután a kódot futtathatja, és most a hozzáférést a konfigurációs fájlok is elindítja.

**Tárolók**: alapértelmezés szerint a Service Fabric telepíti, és aktiválja a szolgáltatást is folyamatok. A Service Fabric szolgáltatások tároló képek is telepítheti. Tárolók egy virtualizációs technológia, amely Virtualizálja az alapul szolgáló operációs rendszer alkalmazások. Az alkalmazás és a futásidejű, a függőségeket és a rendszer szalagtárak futhat egy tárolót. A tároló rendelkezik teljes körű, privát hozzáférést, az operációs rendszer megoldások tároló saját elkülönített nézetbe. A Service Fabric Docker-tároló támogatja a Linux és Windows Server-tárolókra vonatkozóan. További információkért olvassa el a [Service Fabric és a tárolók](service-fabric-containers-overview.md).

**Partícióséma**: egy adott szolgáltatás létrehozásakor megadott partícióséma. Szolgáltatások jelentős mennyiségű állapot-e az adatok osztani partíciók, amelyek között osztja el az állapot, a fürt csomópontjai között. Az adatok között partíciók rendezze az adott szolgáltatás állapota is méretezhető. A partíciókon belül elnevezett állapotmentes szolgáltatások példánya, lehet, mivel a szolgáltatások nevű állapotfüggő replikák. Általában az elnevezett állapotmentes szolgáltatások csak egy partíció rendelkezik, mert az nem belső állapot. A partíció példányok adja meg a rendelkezésre állás érdekében. Egy példány nem sikerül, ha más esetekben továbbra is megfelelően működik, és a Service Fabric létrehoz egy új példányát. Állapotalapú alkalmazások és szolgáltatások nevű szolgáltatások karbantartása állapotukra belül replikákat, és mindegyik partíció rendelkezik saját replika, ezért az állapot van szinkronban. Amennyiben nem egy replikát, a Service Fabric a meglévő replikákat új replikát hoz létre.

Olvassa el a [partíció Service Fabric megbízható szolgáltatások](service-fabric-concepts-partitioning.md) cikkében találja.

## <a name="system-services"></a>Rendszerszolgáltatások
Nincsenek rendszerszolgáltatások minden fürtben létrehozott, adja meg a Service Fabric platform képességeit.

**Naming Service**: minden Service Fabric-fürt rendelkezik egy szolgáltatás, amely szolgáltatásnevek a fürt helyre mutat. A szolgáltatás neveit és tulajdonságait, például egy internetes kezelése tartománynévrendszer (DNS) a fürthöz. Az ügyfelek biztonságosan tudjon kommunikálni a fürt bármely csomópontjának feloldani a szolgáltatás nevét és annak helye a szolgáltatás használatával. Alkalmazások áthelyezése a fürtön belül. Például valószínűleg hibák, erőforrás terheléselosztás vagy a fürt átméretezése. Szolgáltatások és az ügyfelek, oldja meg az aktuális hálózati helyét fejleszthet. Az ügyfelek a tényleges gép IP-cím és port, amelyen fut beszerzése.

Olvasási [kommunikáljon a szolgáltatások](service-fabric-connect-and-communicate-with-services.md) további információ az ügyfél és a szolgáltatás kommunikációs API-k, a szolgáltatás együtt használható.

**Image Store szolgáltatás**: minden Service Fabric-fürt rendelkezik egy Image Store szolgáltatás ahol telepített, verzióval ellátott alkalmazáscsomagok tartanak. Másolja az Image Store egy alkalmazáscsomagot, és regisztrálja az adott alkalmazás csomagban lévő típusa. Az alkalmazástípus üzembe helyezése után elnevezett-alkalmazás létrehozása belőle. Az Image Store szolgáltatás egy alkalmazás típusának tudja törölni, miután az elnevezett alkalmazásokhoz, hogy törölték.

Olvasási [megérteni a előtaggal beállítás](service-fabric-image-store-connection-string.md) az Image Store szolgáltatásról további információt.

Olvassa el a [alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md) cikkben olvashat az Image Store szolgáltatás-alkalmazások telepítése.

**A Failover Manager service**: minden Service Fabric-fürt rendelkezik egy Feladatátvevőfürt-kezelő szolgáltatás, amely felelős az alábbi műveleteket:
   - Magas rendelkezésre állás és a szolgáltatások konzisztencia kapcsolódó funkciókat hajtja végre.
   - Alkalmazás és a fürt frissítéseket koordinálja.
   - Egyéb rendszerösszetevők kommunikál.

**Javítsa ki a service Manager**: Ez egy választható-szolgáltatás, amely lehetővé teszi, hogy egy módon biztonságos, a fürt végrehajtandó javítási műveletek automatable és átlátható az. A javítási Managert használja:
   - Az Azure karbantartást kijavítja [ezüst és arany tartóssági](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Azure Service Fabric-fürtök.
   - A javítási műveletek elvégzése [javítás Vezénylési alkalmazás](service-fabric-patch-orchestration-application.md)

## <a name="built-in-programming-models"></a>Beépített programozási modellek
Nincsenek elérhető, hogy a Service Fabric-szolgáltatásokat a .NET Framework és a Java programozási modellek:

**Megbízható szolgáltatások**: az API állapotmentes és állapotalapú szolgáltatással létrehozásához. Állapotalapú szolgáltatások állapotukra megbízható gyűjteményekben, például egy könyvtár, illetve a várólista tárolja. Különböző kommunikációs verem, például a Web API és a Windows Communication Foundation (WCF) is csatlakoztatható.

**Reliable Actors**: az API a virtuális szereplő programozási modell használatával az állapotmentes és állapotalapú objektumok létrehozásához. Ez a modell akkor hasznos, ha nagyszámú független egység számítási vagy állapotban van. Ez a modell kapcsolja alapú szálkezelési modellt használ, a legjobb behívó kód más szereplője vagy szolgáltatásokhoz, mert egy egyedi szereplő más bejövő kérelmek nem dolgozható fel a kimenő kérelmek befejezéséig elkerülése érdekében.

A meglévő alkalmazások Service Fabric is futtathatja:

**Tárolók**: a Service Fabric a Docker-tároló központi telepítését támogatja a Linux és Windows Server-tárolókra vonatkozóan a Windows Server 2016 működési feltételeit, támogatja a Hyper-V elkülönítési üzemmódját. A Service Fabric a [alkalmazásmodell](service-fabric-application-model.md), a tároló jelöli egy alkalmazásgazda, mely több szolgáltatásban replikák kerülnek. A Service Fabric tárolókkal futtathatja, és a forgatókönyv hasonló Vendég végrehajtható forgatókönyvet, ahol a becsomagolt belül egy tárolót egy meglévő alkalmazást. Emellett képes [tárolókba Service Fabric-szolgáltatások futtatásához](service-fabric-services-inside-containers.md) is.

**Vendég végrehajtható fájlok**: Azure Service Fabric szolgáltatás kódok, például a Node.js, Java vagy C++ bármilyen típusú futtathat. A Service Fabric Vendég végrehajtható fájlok, amelyek állapotmentes szolgáltatások számít ilyen típusú szolgáltatások néven hivatkozik. A Vendég végrehajtható, a Service Fabric-fürt futtatására előny magas rendelkezésre állású, állapotfigyelés, alkalmazás-életciklus kezelésének, nagy sűrűségű, és a Felderíthetőségi.

Olvassa el a [válassza ki a szolgáltatás programozási modellt](service-fabric-choose-framework.md) cikkében találja.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
További információt a Service Fabric:

* [A Service Fabric áttekintése](service-fabric-overview.md)
* [Miért érdemes a mikroszolgáltatás-alapú megközelítést választani alkalmazások létrehozásához?](service-fabric-overview-microservices.md)
* [Alkalmazáshasználati helyzetek](service-fabric-application-scenarios.md)


