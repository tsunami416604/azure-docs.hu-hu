---
title: További tudnivalók az Azure Service Fabric |} Microsoft Docs
description: További tudnivalók az alapvető fogalmait és főbb területei Azure Service Fabric. A Service Fabric és mikroszolgáltatások létrehozása kiterjesztett áttekintést nyújt.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: ryanwi
ms.openlocfilehash: 1c3ea5b041cf2a961ef57bc168ae86b83412e044
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Ezért kívánt további információt a Service Fabric?
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését.  A Service Fabric rendelkezik egy nagy felületet biztosít, és sok további.  Ez a cikk a Service Fabric kivonatát biztosít, és ismerteti az alapvető fogalmakat, programozási modellek, alkalmazás-életciklus, fürtök és a állapotfigyeléssel teszteléséhez. Olvassa el a [áttekintése](service-fabric-overview.md) és [mikroszolgáltatások Mik?](service-fabric-overview-microservices.md) bevezetést, és hogyan a Service Fabric mikroszolgáltatások létrehozására használható. Ez a cikk átfogó tartalom listája nem tartalmaz, de csatolás áttekintése és a Service Fabric minden területéhez elindított cikkek beolvasása. 

## <a name="core-concepts"></a>Alapfogalmak
[A Service Fabric-terminológia](service-fabric-technical-overview.md), [alkalmazásmodell](service-fabric-application-model.md), és [támogatott programozási modellek](service-fabric-choose-framework.md) adja meg a további fogalmakat és leírásokat, de itt alapjait.

<table><tr><th>Alapfogalmak</th><th>Tervezési idő</th><th>Futási idő</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>Tervezési idő: alkalmazástípusok, szolgáltatás típusa, alkalmazáscsomag és jegyzék, szolgáltatáscsomag és jegyzék
Az alkalmazástípus hozzárendeli egy gyűjteményhez található szolgáltatástípusok neve/verziója. Ez a van definiálva egy *ApplicationManifest.xml* fájlt, amely egy alkalmazás csomag könyvtárában van beágyazva. Az alkalmazáscsomag majd másolja a Service Fabric-fürt lemezképtárolóhoz. Ezután az alkalmazás típusa, amely majd fut a fürtön belül létrehozhat egy elnevezett alkalmazást. 

A szolgáltatás típus egy szolgáltatás kód csomagok, adatok csomagokat és konfigurációs csomagok rendelt neve/verziója. Ez egy ServiceManifest.xml fájlt, amely egy service-csomag könyvtárában van beágyazva van meghatározva. A service-csomag könyvtárában hivatkozik rá egy alkalmazáscsomagot *ApplicationManifest.xml* fájlt. A fürtön belül egy elnevezett alkalmazás létrehozása után létrehozhat egy adott szolgáltatást az alkalmazástípus szolgáltatás típusok egyikét. A szolgáltatás írja le annak *ServiceManifest.xml* fájlt. A szolgáltatás típusának végrehajtható kód szolgáltatás konfigurációs beállításokat, amelyek futási időben betöltése, és a szolgáltatás által felhasznált statikus adatok állnak.

![A Service Fabric alkalmazás és szolgáltatás típusainak][cluster-imagestore-apptypes]

Az alkalmazáscsomag az alkalmazástípus tartalmazó lemez könyvtár *ApplicationManifest.xml* fájlt, amely a service-csomagok az egyes szolgáltatástípusokról az alkalmazástípus alkotó hivatkozik. Például egy alkalmazáscsomagot, egy e-mailek alkalmazás típusra hivatkoznak a várólista szolgáltatáscsomagot, egy előtér-szolgáltatás csomag és egy adatbázis service-csomag tartalmazhatnak. Az alkalmazás csomag könyvtárban található fájlok kerülnek a Service Fabric-fürt lemezképtárolóhoz. 

A service-csomag a szolgáltatás típusának tartalmazó lemez könyvtár *ServiceManifest.xml* fájlt, amely a kódot, a statikus adatok és a konfigurációs csomagok a service Type hivatkozik. A service-csomag könyvtár az alkalmazástípus által hivatkozott *ApplicationManifest.xml* fájlt. A service-csomag például a kódot, statikus adatok és egy adatbázis-szolgáltatás alkotó konfigurációs csomagokat is hivatkozhat.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Futási idő: fürtök és a csomópontok, alkalmazások, szolgáltatások, a partíciók és a replikák nevű nevű
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) virtuális és fizikai gépek hálózaton keresztül csatlakozó készlete, amelyen mikroszolgáltatásokat helyezhet üzembe és felügyelhet. A fürtök több ezer gépre skálázhatók.

A fürtök részét képező gépeket vagy virtuális gépeket csomópontoknak nevezzük. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy karakterlánc). A csomópontok jellemzőkkel, például elhelyezési tulajdonságokkal rendelkeznek. Minden számítógép vagy virtuális gép rendelkezik egy automatikusan induló Windows szolgáltatás `FabricHost.exe`, amely rendszerindítási után elindul, és elindítja a két végrehajtható fájlok: `Fabric.exe` és `FabricGateway.exe`. E két végrehajtható fájlokat a csomópont alkotják. Fejlesztési és tesztelési forgatókönyvekhez, tárolhatja, több csomópont egyetlen számítógép vagy virtuális gép több példányára futtatásával `Fabric.exe` és `FabricGateway.exe`.

Egy nevesített alkalmazásra az elnevezett szolgáltatások gyűjteménye, amely egy bizonyos funkciója vagy funkciói hajt végre. A szolgáltatás végzi a teljes és az önálló (akkor is elindításához és egyéb szolgáltatások függetlenül) és a kódot, a konfiguráció és az adatok tevődik össze. Az image store másolja egy alkalmazáscsomagot, miután az alkalmazás a fürtön belül példányt hoz létre az alkalmazáscsomag alkalmazástípus (használja a neve/verziója) megadásával. Minden alkalmazás típuspéldány hozzá van rendelve egy URI-nevet, amely a következőképpen néz *fabric: / MyNamedApp*. A fürtön belül több nevű alkalmazást is létrehozhat egyetlen alkalmazás típusból. Különböző típusok elnevezett alkalmazások is létrehozhat. Minden elnevezett alkalmazása felügyelt és a rendszerverzióval ellátott egymástól függetlenül.

Egy nevesített alkalmazás létrehozása után is létrehozhat szolgáltatás típusa (egy adott szolgáltatás) egy példányának a fürtön belül (a név/verzióját használja) szolgáltatás típusának megadásával. Minden szolgáltatáspéldány típus hozzá van rendelve egy elnevezett alkalmazásának URI alatt hatókörű URI nevét. Például, ha létrehoz egy "adatbázis" nevű szolgáltatás belül "MyNamedApp" nevű alkalmazás, az URI a következőképpen néz: *fabric: / MyNamedApp/adatbázis*. Egy elnevezett alkalmazáson belül létrehozhat egy vagy több nevű szolgáltatás. Minden elnevezett szolgáltatás a saját partícióséma is rendelkezik, és a replika/példány adatokra. 

Szolgáltatások két típusa van: az állapotmentes és állapotalapú. Állapotmentes szolgáltatások állandó állapotát tárolhatja például az Azure Storage, az Azure SQL Database vagy az Azure Cosmos DB egy külső tároló szolgáltatást. Állapot nélküli szolgáltatást használ, ha a szolgáltatás egyáltalán nem állandó tároló rendelkeznek. Egy állapotalapú szolgáltatás a Service Fabric használja a szolgáltatás állapotának a megbízható gyűjtemények vagy a Reliable Actors programozási modell használatával kezelheti. 

Egy adott szolgáltatás létrehozásakor meg kell adni egy partícióséma. Szolgáltatások állapota nagy mennyiségű adatok osztani partíciókat. Mindegyik partíció felelős a szolgáltatást, amely a fürt csomópontjai között megoszlik teljes állapotának része.  

Az alábbi ábrán látható, az alkalmazások és a szolgáltatáspéldány, a partíciók és a replikák közötti kapcsolat.

![Partíciók és replikáit a szolgáltatáson belül][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Particionálás, a méretezés és a rendelkezésre állás
[Particionálás](service-fabric-concepts-partitioning.md) nincs egyedi, hogy a Service Fabric. Egy jól ismert particionálás formátuma adatparticionálás vagy horizontális. Állapotalapú szolgáltatások állapota nagy mennyiségű adatok osztani partíciókat. Mindegyik partíció feladata a teljes állapotát a szolgáltatás része. 

Mindegyik partíció replikáinak vannak elosztva a fürtcsomópontokon, amely lehetővé teszi, hogy az adott szolgáltatás állapotra [méretezési](service-fabric-concepts-scalability.md). Az adatok igények nő, partíciók nő, és a Service Fabric partíciók módon használhatják a hardvererőforrás-csomópontokon keresztüli újra egyensúlyba hozza. Ha új csomópont hozzáadása a fürthöz, akkor a Service Fabric a partíció replikák egyensúlyba a növekvő számának a csomópontok között. A teljes javítja az alkalmazások teljesítményéről, és csökkenti a versengés a memória a hozzáférést. Ha a fürt a csomópontok nem használ a hatékony, csökkentheti a fürtben található csomópontok számát. A Service Fabric újra újra egyensúlyba hozza a partíció replikák között jobb kihasználása érdekében a hardver minden egyes csomóponton csomópontok csökkent száma.

A partíciókon belül elnevezett állapotmentes szolgáltatások példánya lehet, míg a állapot-nyilvántartó elnevezett szolgáltatások replikák. Általában elnevezett állapotmentes szolgáltatásokhoz csak kell egy partíciót óta belső állapot nélküli rendelkeznek. A partíció-példányok megadása [rendelkezésre állási](service-fabric-availability-services.md). Egy példány nem sikerül, ha más esetekben továbbra is megfelelően működik, és a Service Fabric létrehoz egy új példányát. Állapotalapú alkalmazások és szolgáltatások nevű szolgáltatások karbantartása állapotukra belül replikákat, és mindegyik partíció rendelkezik saját replikakészlet. Olvasási és írási műveleteket, egy replika (más néven az elsődleges). Az állapot módosításai az írási műveletek replikálódnak több más replika (más néven aktív másodlagos). Amennyiben nem egy replikát, a Service Fabric a meglévő replikákat új replikát hoz létre.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Állapot nélküli és állapotalapú mikroszolgáltatások a Service Fabrichez
A Service Fabric segítségével mikroszolgáltatásokból vagy tárolókból álló alkalmazásokat építhet. Az állapot nélküli mikroszolgáltatások (például a protokollátjárók és webproxyk) nem tartanak fenn változtatható állapotot a kéréseken és a szolgáltatástól ezekre kapott válaszokon kívül. Az Azure Cloud Services feldolgozói szerepkörei például állapotmentes szolgáltatások. Az állapotalapú mikroszolgáltatások (például felhasználói fiókok, adatbázisok, eszközök, kosarak és üzenetsorok) változtatható, mérvadó állapotot tartanak fenn a kéréseken és a válaszokon kívül is. A modern webes alkalmazások állapot nélküli és állapotalapú mikroszolgáltatások kombinációjából állnak. 

A Service Fabric egy kulcs differentation az állapotalapú szolgáltatások, vagy erős összpontosít a [programozási modellek beépített ](service-fabric-choose-framework.md) vagy indexelése állapotalapú szolgáltatással. Az [alkalmazás-forgatókönyvek](service-fabric-application-scenarios.md) az állapotalapú szolgáltatások használatát mutatják be.

Miért kell állapotalapú mikroszolgáltatások állapotmentes ők együtt? A két fő oka a következők:

* Nagy átviteli, alacsony késésű, hibatűrő online tranzakció-feldolgozási (OLTP-) szolgáltatások kódot, és zárja be az adatokat ugyanezen a gépen tartja hozhat létre. Néhány többek között az interaktív kirakatokkal, keresést, az eszközök internetes hálózatát (IoT) rendszerek, kereskedési rendszerek-megnövelt, hitelkártya feldolgozási és csalás rendszerek és személyes rekord felügyeleti.
* Egyszerűbbé teheti az alkalmazás tervét. Állapot-nyilvántartó mikroszolgáltatások távolítsa el a további várólisták és a gyorsítótár, amely hagyományosan szükségesek a rendelkezésre állás és a késési követelménynek tisztán állapot nélküli alkalmazások van szükség. Állapotalapú szolgáltatások természetes, magas rendelkezésre állású és alacsony késésű, ami csökkenti az alkalmazás egészének kezeléséhez áthelyezése részek száma.

## <a name="supported-programming-models"></a>Támogatott programozási modellek
A Service Fabric és a szolgáltatások kezeléséhez több lehetőséget is kínál. Szolgáltatások használhatnak a Service Fabric API-k teljes kihasználásához a platform szolgáltatásai és alkalmazás-keretrendszerek számára. Szolgáltatások bármilyen lefordított végrehajtható bármilyen nyelven írt, és a Service Fabric fürt által futtatott is lehet. További információkért lásd: [támogatott programozási modellek](service-fabric-choose-framework.md).

### <a name="containers"></a>Containers
Alapértelmezés szerint a Service Fabric telepíti, és aktiválja a szolgáltatást is folyamatokat. A Service Fabric is telepítheti a szolgáltatások [tárolók](service-fabric-containers-overview.md). Fontos kombinálhatja a folyamatokat a szolgáltatások és szolgáltatások tárolókban ugyanabban az alkalmazásban. A Service Fabric Windows Server 2016 Linux tárolók Windows tárolók központi telepítését támogatja. A meglévő alkalmazásokat, állapotmentes szolgáltatásokhoz vagy tárolókban lévő állapotalapú szolgáltatások telepítése. 

### <a name="reliable-services"></a>Reliable Services
[Megbízható szolgáltatások](service-fabric-reliable-services-introduction.md) egyszerűsített keretrendszere, amely integrálható a Service Fabric-platformról és igénybe vehesse az platform funkciói a teljes készletének szolgáltatások írása. Lehet, hogy a Reliable Services állapot nélküli (a legtöbb service platformokat, például a webkiszolgálók vagy feldolgozói szerepkörök az Azure Felhőszolgáltatások hasonlóan), ahol állapot megőrződjenek a külső megoldás például Azure DB vagy az Azure Table Storage. Megbízható szolgáltatások is lehet állapotfüggő, közvetlenül a megbízható gyűjtemények használatával a szolgáltatás állapota tároló. Állapot legyen [magas rendelkezésre állású](service-fabric-availability-services.md) replikáció útján és elosztott keresztül [particionálás](service-fabric-concepts-partitioning.md), az összes kezelt Service Fabric által automatikusan.

### <a name="reliable-actors"></a>Reliable Actors
Reliable Services platformra épül a [megbízható szereplő](service-fabric-reliable-actors-introduction.md) keretrendszer egy alkalmazás-keretrendszer, amely a virtuális szereplő mintában szereplő kialakítási minta alapján. A megbízható szereplő keretrendszer a számítási műveletek és állapot független egység szereplője nevű egyszálas végrehajtási használ. A megbízható szereplő keretrendszer által biztosított beépített szereplőket közötti kommunikáció, és előre beállított állapot megőrzését és kibővített konfigurációkat.

### <a name="aspnet-core"></a>ASP.NET-mag
Integrálható a Service Fabric [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) webes és API-alkalmazások készítéséhez első osztályú programozási modell.  Az ASP.NET Core a Service Fabric két különböző módon használható:

- Üzemeltetett és a Vendég végrehajtható fájlja. Ez elsősorban az ASP.NET Core meglévő alkalmazások futtatásához a Service Fabric módosítások nélküli kód.
- A megbízható szolgáltatás futhat. Ez lehetővé teszi, hogy a Service Fabric-futtatókörnyezet jobb integrációt, és lehetővé teszi az állapotalapú ASP.NET Core szolgáltatások.

### <a name="guest-executables"></a>Vendég végrehajtható fájlok
A [Vendég végrehajtható](service-fabric-guest-executables-introduction.md) meglévő, tetszőleges végrehajtható (bármilyen nyelven írt) más szolgáltatásokkal együtt a Service Fabric fürt által futtatott. Vendég végrehajtható fájlok integrálható közvetlenül Service Fabric API-k. Azonban azok továbbra is tudják igénybe venni a platformot kínál, például az egyéni állapotot funkciókat és betölteni a jelentéskészítés és felderíthetőség szolgáltatás REST API-k hívásával. Teljes alkalmazás életciklusa támogatási is rendelkeznek. 

## <a name="application-lifecycle"></a>Alkalmazás-életciklus
És egyéb platformok, a Service Fabric alkalmazás általában végig kell vinnie a következő fázisok: tervezési, fejlesztési, tesztelési, központi telepítés, frissítés, karbantartási és eltávolítása. A Service Fabric első osztályú támogatást nyújt a teljes alkalmazás életciklusa a felhőalapú alkalmazások, a fejlesztését a telepítés, a napi felügyeleti és a karbantartás végleges leszerelése. A modell lehetővé teszi, hogy az alkalmazás-életciklus egymástól függetlenül részt számos különböző szerepkörrel. [A Service Fabric-alkalmazás életciklusa](service-fabric-application-lifecycle.md) az API-k és azok használata során a Service Fabric-alkalmazás életciklusa fázisai a különböző szerepkörök áttekintése. 

A teljes alkalmazás-életciklus felügyelhetők [PowerShell-parancsmagok](/powershell/module/ServiceFabric/), [parancssori felület parancsait](service-fabric-sfctl.md), [C# API-k](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [Java API-k](/java/api/system.fabric._application_management_client), és [ REST API-k](/rest/api/servicefabric/). Is beállíthat folyamatos integrációt/folyamatos telepítési folyamatok eszközökkel, például a [Visual Studio Team Services](service-fabric-set-up-continuous-integration.md) vagy [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

A következő Microsoft Virtual Academy videó bemutatja, hogyan kezelheti az alkalmazás-életciklus: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-applications-and-services"></a>Alkalmazások és szolgáltatások tesztelése
Valóban felhőméretű szolgáltatások létrehozására, kiemelten fontos annak ellenőrzése, hogy az alkalmazások és szolgáltatások is hardverkapacitás valós. A tartalék elemzési szolgáltatás végzi a Service Fabric épülő szolgáltatások tesztelése. Az a [hiba az Analysis Services](service-fabric-testability-overview.md), idéz elő a jelentéssel bíró hibák, és a teljes teszt forgatókönyvek az alkalmazások futtatásához. Ezeket a hibákat és forgatókönyvek gyakorolja, és érvényesítse a számos állapotok és átmenetek, amelyeknek a szolgáltatás teljes élettartamuk összes ellenőrzött, biztonságos és egységes módon.

[Műveletek](service-fabric-testability-actions.md) céloz tesztelésre használt egyes hibák szolgáltatás. A szolgáltatás fejlesztők segítségével ezek építőelemeiként összetett forgatókönyvek írni. Például szimulált hibák a következők:

* Indítsa újra az olyan helyzetekben, ahol egy számítógép vagy virtuális gép újraindítása után tetszőleges számú szimulálása csomópont.
* Helyezze át egy replikát készít az állapot-nyilvántartó szolgáltatás terheléselosztás, feladatátvétel vagy az alkalmazásfrissítés szimulálásához.
* Egy állapotalapú szolgáltatás segítségével olyan helyzetet, ahol írási műveletek nem folytatható, mert nincs elég "biztonsági másolat" vagy "másodlagos" replikák új adatokat fogadni a kvórum elvesztése meghívni.
* Olyan helyzet, ahol minden a memóriában teljesen tárolt adatok törléséig kimenő létrehozásához állapotalapú Service az adatvesztés meghívni.

[Forgatókönyvek](service-fabric-testability-scenarios.md) összetett műveletek álló egy vagy több művelet. A tartalék Analysis Service két beépített teljes eseteit tartalmazza:

* [Chaos forgatókönyv](service-fabric-controlled-chaos.md)-hosszú időn keresztül a folyamatos, kihagyásos hibák (szabályos és ungraceful) a fürt teljes szimulálja.
* [Feladatátvételi forgatókönyv](service-fabric-testability-scenarios.md#failover-test)-egy verziója a chaos tesztkörnyezet, amelynek célja egy adott szolgáltatás partíció úgy, hogy más szolgáltatások nem érinti.

## <a name="clusters"></a>Fürtök
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) virtuális és fizikai gépek hálózaton keresztül csatlakozó készlete, amelyen mikroszolgáltatásokat helyezhet üzembe és felügyelhet. A fürtök több ezer gépre skálázhatók. Egy számítógép vagy virtuális Gépet, amely egy fürt része egy fürtcsomópont neve. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy karakterlánc). A csomópontok jellemzőkkel, például elhelyezési tulajdonságokkal rendelkeznek. Minden számítógép vagy a virtuális gép rendelkezik egy automatikusan induló szolgáltatás `FabricHost.exe`, amely rendszerindítási után elindul, és elindítja a két végrehajtható fájlok: Fabric.exe és FabricGateway.exe. E két végrehajtható fájlokat a csomópont alkotják. Tesztelési forgatókönyvek, tárolhatja, több csomópont egyetlen számítógép vagy virtuális gép több példányára futtatásával `Fabric.exe` és `FabricGateway.exe`.

Service Fabric-fürtök a Windows Server vagy Linux rendszerű virtuális vagy fizikai gépek hozhatók létre. Tud telepítésére és a Service Fabric-alkalmazások futtatására minden környezetben, Windows Server vagy Linux rendszerű számítógépek összekapcsolt esetében: a helyszíni Microsoft Azure, vagy bármely felhőalapú szolgáltató.

A következő Microsoft Virtual Academy videó ismerteti a Service Fabric-fürtök: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Fürtök az Azure-on
Integráció más Azure-szolgáltatások és szolgáltatások, egyszerűbb és megbízhatóbb műveletek és a fürt felügyeletének így Service Fabric-fürtök futó Azure biztosít. A fürt egy Azure Resource Manager erőforrás, így a modellezhető fürtök, mint bármely más erőforrások, az Azure-ban. A Resource Manager is biztosít kezelésének egyetlen egységként a fürt által használt összes erőforrást. Azure-fürtök integrált Azure diagnostics és az Naplóelemzési. Fürt csomópont típusok [virtuálisgép-méretezési csoportok](/azure/virtual-machine-scale-sets/index), így az automatikus skálázás funkciót részét.

A fürt hozhat létre Azure keresztül a [Azure-portálon](service-fabric-cluster-creation-via-portal.md), a egy [sablon](service-fabric-cluster-creation-via-arm.md), vagy a [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

A Service Fabric Linux lehetővé teszi, hogy hozhat létre, telepíthetnek és Linux magas rendelkezésre állású, nagymértékben méretezhető alkalmazásokat kezeléséhez, ugyanúgy, mint a Windows. A Service Fabric-keretrendszerek (Reliable Services és Reliable Actors) a C# (.NET Core) mellett a Linux Java nyelven érhetők el. Is létrehozható [végrehajtható vendégszolgáltatások](service-fabric-guest-executables-introduction.md) rendelkező bármilyen nyelven vagy keretrendszerben. Docker-tároló koordinálása is támogatott. Docker-tároló Vendég végrehajtható fájlok, illetve a Service Fabric-keretrendszert használó natív Service Fabric szolgáltatások futtathatók. További információkért olvassa el [Service Fabric Linux](service-fabric-deploy-anywhere.md).

Nincsenek az egyes szolgáltatások Windows rendszeren, de nem Linux rendszeren támogatott. További tudnivalókért olvassa el a [Service Fabric Linux rendszeren és Windows közötti különbségek](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Önálló fürtök
A Service Fabric egy csomag biztosít, hozza létre a különálló Service Fabric fürt helyszíni vagy bármely felhőalapú szolgáltató. Önálló fürtök a szabadságot, hogy a gazdagép egy fürt, ahol azt szeretné, biztosítanak. Ha az adatok a megfelelőségi és szabályozási korlátozások vonatkoznak, vagy meg szeretné tartani az adatok helyi, a saját fürt és az alkalmazások tárolhatja. Service Fabric-alkalmazások futtathat több üzemeltetési környezetekben változtatás nélkül, így az alkalmazások ismerete hordoz magában, ha a üzemeltetési környezetek egy másikra. 

[Az első Service Fabric önálló fürt létrehozása](service-fabric-get-started-standalone-cluster.md)

Linux önálló fürtök még nem támogatott.

### <a name="cluster-security"></a>Fürtbiztonság
Fürtök védetté kell tennie, hogy jogosulatlan felhasználók csatlakozzon a fürthöz, különösen akkor, ha rendelkezik termelési számítási feladatokhoz fut rajta. Bár lehetséges egy nem biztonságos fürtök létrehozásához, ezzel engedélyezi a névtelen felhasználók csatlakozni, ha a felügyeleti végpontok érhetők el a nyilvános internethez. Nincs lehetőség ahhoz, hogy később egy biztonságos fürt biztonsági: fürt biztonsági engedélyezve van a fürt létrehozása során.

A fürt biztonsági forgatókönyvek a következők:
* Csomópontok biztonsági
* Ügyfél-csomópont biztonsági
* Szerepköralapú hozzáférés-vezérlés (RBAC)

További információkért olvassa el a [fürt biztonságos](service-fabric-cluster-security.md).

### <a name="scaling"></a>Méretezés
Ha új csomópont hozzáadása a fürthöz, a Service Fabric újra egyensúlyba hozza a partíció replikákat és a példányt a növekvő számának a csomópontok között. A teljes javítja az alkalmazások teljesítményéről, és csökkenti a versengés a memória a hozzáférést. Ha a fürt a csomópontok nem használ a hatékony, csökkentheti a fürtben található csomópontok számát. A Service Fabric újra újra egyensúlyba hozza a partíció replikákat és a példány között jobb kihasználása érdekében a hardver minden egyes csomóponton csomópontok csökkent száma. Azure-fürtök vagy méretezheti [manuálisan](service-fabric-cluster-scale-up-down.md) vagy [programozott módon](service-fabric-cluster-programmatic-scaling.md). Önálló fürtök méretezhetők [manuálisan](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Fürt frissítése
Rendszeres időközönként a Service Fabric-futtatókörnyezet új verzióinak kiadásakor. Hajtsa végre a futtatókörnyezet, vagy fordítva, frissítések a fürtön, hogy mindig futtatja egy [támogatott verziója](service-fabric-support.md). Fabric frissítéskezelésének mellett fürtkonfiguráció, például tanúsítványok vagy alkalmazás portok is frissítheti.

A Service Fabric-fürt, amely a saját, de részben Microsoft által felügyelt erőforrás. Microsoft javítását az alapul szolgáló operációs rendszer és a fürtön lévő fabric-frissítések végrehajtása felelős. Állítsa be a fürt automatikus fabric frissítéskezelésének fordul elő, ha a Microsoft által kiadott új verzióra, vagy válassza ki a támogatott háló verzióját, amelyet válassza. Háló és a konfigurációs frissítéseket az Azure portálon keresztül vagy a Resource Manageren keresztül állítható be. További információkért olvassa el a [a Service Fabric-fürt frissítése](service-fabric-cluster-upgrade.md). 

Egy önálló fürthöz egy erőforrást, amikor teljesen saját. Az alapul szolgáló operációs rendszer javítását, és kezdeményezi a fabric frissítéskezelésének felelős áll. Ha a fürt csatlakozni tud-e [ https://www.microsoft.com/download ](https://www.microsoft.com/download), beállíthatja, hogy a fürt automatikusan letölteni és telepíteni a Service Fabric-futtatókörnyezet új csomag. Akkor lesz majd indítsa el a frissítést. Ha nem érhető el a fürt [ https://www.microsoft.com/download ](https://www.microsoft.com/download), manuálisan töltse le az új futásidejű csomag egy internethez csatlakoztatott gépről, és majd indítsa el a frissítést. További információkért olvassa el a [különálló Service Fabric fürt frissítése](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Állapotfigyelés
A Service Fabric bevezet egy [állapotmodell](service-fabric-health-introduction.md) úgy tervezték, hogy ez a jelző nem kifogástalan fürt és az alkalmazás feltételeket az adott entitások (például a fürtcsomópontok és a szolgáltatás replikák). A health modellje állapotfigyelő jelentéskészítők (rendszer összetevőit és watchdogs). Könnyű és gyors diagnosztikai és javítási célja. Szolgáltatás írók szükség az egészségügyi kapcsolatos társaságuk és [állapotfigyelő reporting tervezési](service-fabric-report-health.md#design-health-reporting). Minden olyan esetben, kedvezőtlen hatással lehet az egészségügyi kell kiválasztását, különösen akkor, ha segíthet megközelíti a legfelső szintű jelző problémák. Az állapotadatok időt takaríthat meg, és elérhető a Hibakeresés és a vizsgálat után a szolgáltatás megfelelően működik, és éles környezetben léptékű.

A Service Fabric-jelentéskészítők nevű figyelő meghatározott feltételek egyik fontos. Azok a helyi nézet alapján feltételek jelentést. A [a health Store adatbázisban](service-fabric-health-introduction.md#health-store) összefoglalja az entitások globálisan kifogástalan megállapításához összes jelentéskészítők által küldött egészségügyi adatokat. A modell olyan gazdag, rugalmas és könnyen használható. A rendszerállapot-jelentések minőségének meghatározza, hogy a fürt az állapotmegtekintő nézet pontosságát. Téves tévesen jeleníti meg a nem megfelelő negatívan befolyásolhatja a frissítések vagy az egészségügyi adatokat használó más szolgáltatásokba. Ezek a szolgáltatások többek között a javítása és a riasztási mechanizmusokat. Adja meg a jelentéseket, amelyek a lehető legjobb módon érdeklő feltételek rögzítése ezért néhány gondolat van szükség.

Jelentéskészítési teheti meg:
* A figyelt Service Fabric szolgáltatás replika vagy példányt.
* Belső watchdogs telepített Service Fabric szolgáltatás (például a Service Fabric állapotmentes szolgáltatások, amely figyeli a feltételek és a jelentések problémák). A watchdogs is telepíthető az összes olyan csomóponton, vagy is rendelhetők affinitás alapján a figyelt szolgáltatás.
* Belső watchdogs, futtassa a Service Fabric-csomóponton, de nem valósult meg a Service Fabric szolgáltatásként.
* Az erőforrást a Service Fabric-fürt (például figyelőszolgáltatás Gomez hasonlóan) kívül mintavételi külső watchdogs.

Alapesetben a Service Fabric összetevői jelentés állapotát a fürt valamennyi entitást. [Rendszerállapot-jelentések](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) adja meg a fürt- és funkcióit és jelző problémák keresztül állapotfigyelő láthatósága. Alkalmazások és szolgáltatások rendszerállapot-jelentések győződjön meg arról, hogy az entitások valósíthatók meg, és a Service Fabric-futtatókörnyezet szempontjából megfelelően működik. A jelentések ne adja meg az üzleti logika, a szolgáltatás bármely állapotának figyelését és lefagyott folyamatok észlelésére. Adott állapottal kapcsolatos adatok hozzáadása a szolgáltatás logika [valósítja meg az egyéni állapotfigyelő reporting](service-fabric-report-health.md) a szolgáltatásban.

Több módszert biztosít a Service Fabric [rendszerállapot-jelentések megtekintéséhez](service-fabric-view-entities-aggregated-health.md) összesíteni a health Store adatbázisban:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) vagy más képi megjelenítés eszközök.
* Állapotlekérdezések (keresztül [PowerShell](/powershell/module/ServiceFabric/), [CLI](service-fabric-sfctl.md), a [C# FabricClient API-k](/dotnet/api/system.fabric.fabricclient.healthclient) és [Java FabricClient API-k](/java/api/system.fabric._health_client), vagy [REST API-k](/rest/api/servicefabric)).
* Általános lekérdezi az állapotfigyelő egyik (a PowerShell, CLI-t, az API-k vagy REST) tulajdonságaként rendelkező entitásokat, amely visszatérési listája.

A következő Microsoft Virtual Academy videó ismerteti a Service Fabric állapotmodellt és használatának módját: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika
[Megfigyelési és diagnosztikai](service-fabric-diagnostics-overview.md) kritikus fejleszt, tesztelése és telepítése az alkalmazások és szolgáltatások minden környezetben. Service Fabric-megoldás a legmegfelelőbb tervezése és megvalósítása a figyelési és diagnosztika, amelyek segítenek győződjön meg róla, alkalmazásokat, és szolgáltatások vannak a várt módon működik a helyi környezet vagy a termelési.

A fő figyelése és diagnosztikai céljai számára:

- Észlelheti és diagnosztizálhatja a hardver- és infrastruktúra-problémák
- Szoftver- és alkalmazás problémák észlelése, csökkentheti az állásidőt szolgáltatás
- Erőforrás használati és súgó meghajtó műveletek döntések fontossága
- Alkalmazás, szolgáltatás és az infrastruktúra teljesítményének optimalizálása
- Üzleti elemzések készítése és területeinek
 
Az általános munkafolyamat figyelési és diagnosztika három lépésből áll:

1. Az eseménygenerálás: Ez magában foglalja az események (naplókat, nyomkövetéseket, egyéni események), az infrastruktúra (fürt), a platform és az alkalmazás / szolgáltatás szintjén
2. Esemény összesítési: létrehozott események összegyűjtésének és azok megjelenítése előtt összesítve kell
3. Elemzési: események kell lennie a feladatkonfigurációkat és néhány formátumban engedélyezése elemzéshez ki és jelenítheti meg igény szerint elérhető

Több termék érhető el, amelyek vonatkoznak ezek a területek három, és válassza ki a különböző technológiák az egyes szabadon. További információkért olvassa el a [megfigyelési és diagnosztikai az Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>További lépések
* Megismerkedhet egy [azure-beli fürt](service-fabric-cluster-creation-via-portal.md) vagy egy [önálló windowsbeli fürt](service-fabric-cluster-creation-for-windows-server.md) létrehozásával.
* Próbáljon szolgáltatást létrehozni a [Reliable Services](service-fabric-reliable-services-quick-start.md) vagy a [Reliable Actors](service-fabric-reliable-actors-get-started.md) programozási modellek használatával.
* Megtudhatja, hogyan [Felhőszolgáltatások át](service-fabric-cloud-services-migration-differences.md).
* Ismerje meg, hogy [figyelése és diagnosztizálása szolgáltatások](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Ismerje meg, hogy [tesztelni az alkalmazások és szolgáltatások](service-fabric-testability-overview.md).
* Ismerje meg, hogy [kezelése és levezényléséhez fürterőforrások](service-fabric-cluster-resource-manager-introduction.md).
* Nézze át a [Service Fabric minták](http://aka.ms/servicefabricsamples).
* További tudnivalók [Service Fabric támogatási lehetőségek](service-fabric-support.md).
* Olvassa el a [csapat blogján](https://blogs.msdn.microsoft.com/azureservicefabric/) cikkeket és közleményeket.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
