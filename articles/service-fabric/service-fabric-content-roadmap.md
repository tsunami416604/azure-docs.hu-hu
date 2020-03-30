---
title: További információ az Azure Service Fabricről
description: Ismerje meg az Azure Service Fabric alapfogalmait és főbb területeit. A Service Fabric és a mikroszolgáltatások létrehozásának részletes áttekintését tartalmazza.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 4e6e21f5f9ebfeddb5292e00dc8a929341e77372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458146"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Tehát szeretne többet megtudni a Service Fabric?
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését.  A Service Fabric azonban nagy felülettel rendelkezik, és sokat kell tanulnia.  Ez a cikk a Service Fabric áttekintését tartalmazza, és ismerteti az alapvető fogalmakat, programozási modellek, alkalmazás életciklusa, tesztelés, fürtök és állapotfigyelés. Olvassa el a [áttekintést,](service-fabric-overview.md) és [mik a mikroszolgáltatások?](service-fabric-overview-microservices.md) egy bevezetést, és hogyan a Service Fabric mikroszolgáltatások létrehozásához használható. Ez a cikk nem tartalmaz átfogó tartalomlistát, de a Service Fabric minden területén áttekintésre és az első lépésekre mutató cikkekkel hivatkozik. 

## <a name="core-concepts"></a>Alapfogalmak
[A Service Fabric terminológiája](service-fabric-technical-overview.md), [az alkalmazásmodell](service-fabric-application-model.md)és [a támogatott programozási modellek](service-fabric-choose-framework.md) további fogalmakat és leírásokat biztosítanak, de itt vannak az alapok.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Tervezési idő: szolgáltatás típusa, szolgáltatáscsomag és jegyzékfájl, alkalmazástípus, alkalmazáscsomag és jegyzékfájl
A szolgáltatástípus a szolgáltatás kódcsomagjaihoz, adatcsomagjaihoz és konfigurációs csomagjaihoz rendelt név/verzió. Ezt servicemanifest.xml fájlban definiálja. A szolgáltatástípus végrehajtható kódból és szolgáltatáskonfigurációs beállításokból áll, amelyek futásidőben töltődnek be, valamint a szolgáltatás által felhasznált statikus adatokból.

A szolgáltatáscsomag a szolgáltatástípus ServiceManifest.xml fájlját tartalmazó lemezkönyvtár, amely a szolgáltatástípus kódjára, statikus adataira és konfigurációs csomagjaira hivatkozik. Egy szolgáltatáscsomag például hivatkozhat az adatbázis-szolgáltatást felálló kódra, statikus adatokra és konfigurációs csomagokra.

Az alkalmazástípus a szolgáltatástípusok gyűjteményéhez rendelt név/verzió. Ezt egy ApplicationManifest.xml fájlban definiálja.

![A Service Fabric alkalmazástípusai és szolgáltatástípusai][cluster-imagestore-apptypes]

Az alkalmazáscsomag egy lemezkönyvtár, amely az alkalmazástípus ApplicationManifest.xml fájlját tartalmazza, amely az alkalmazástípust képező minden egyes szolgáltatástípushoz tartozó szolgáltatáscsomagokra hivatkozik. Egy e-mail alkalmazástípus alkalmazáscsomagja például tartalmazhat egy várólista-szolgáltatáscsomagra, egy előtér-szolgáltatáscsomagra és egy adatbázis-szolgáltatási csomagra mutató hivatkozásokat.  

Az alkalmazáscsomag könyvtárában lévő fájlok másolása a Service Fabric-fürt lemezképtárolójára történik. Ezután létrehozhat egy elnevezett alkalmazást ebből az alkalmazástípusból, amely ezután a fürtön belül fut. Miután létrehozott egy elnevezett alkalmazást, létrehozhat egy elnevezett szolgáltatást az alkalmazástípus egyik szolgáltatástípusából. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Futási idő: fürtök és csomópontok, elnevezett alkalmazások, elnevezett szolgáltatások, partíciók és replikák
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatások üzembe helyezése és kezelése történik. A fürtök több ezer gépre skálázhatók.

A fürtök részét képező gépeket vagy virtuális gépeket csomópontoknak nevezzük. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). A csomópontok jellemzőkkel, például elhelyezési tulajdonságokkal rendelkeznek. Minden gép vagy virtuális gép rendelkezik `FabricHost.exe`egy automatikus indítású Windows-szolgáltatással, amely `Fabric.exe` `FabricGateway.exe`indításkor elindul, majd két végrehajtható fájlt indít el: és . Ez a két végrehajtható fájl alkotja a csomópontot. Fejlesztési vagy tesztelési forgatókönyvek esetén több csomópontot is üzemeltethet egyetlen gépen `Fabric.exe` vagy `FabricGateway.exe`virtuális gépen, ha több példányt futtat a és a.

Az elnevezett alkalmazás egy bizonyos függvényt vagy függvényeket ellátó elnevezett szolgáltatások gyűjteménye. A szolgáltatás teljes és önálló függvényt hajt végre (más szolgáltatásoktól függetlenül indíthat és futtatható), és kódból, konfigurációból és adatokból áll. Miután egy alkalmazáscsomagot másolt a lemezképtárolóba, az alkalmazás egy példányát a fürtön belül hozza létre az alkalmazáscsomag alkalmazástípusának megadásával (annak neve/verziója használatával). Minden alkalmazástípus-példányhoz hozzá van rendelve egy URI-név, amely úgy néz ki, mint *a fabric:/MyNamedApp*. Egy fürtön belül egyetlen alkalmazástípusból több elnevezett alkalmazást is létrehozhat. Névvel ellátott alkalmazásokat is létrehozhat különböző alkalmazástípusokból. Minden egyes elnevezett alkalmazás kezelése és verzióverziója egymástól függetlenül történik.

Miután létrehozott egy elnevezett alkalmazást, létrehozhat egy példányt az egyik szolgáltatástípusából (egy elnevezett szolgáltatásból) a fürtön belül a szolgáltatás típusának megadásával (a neve/verziója használatával). Minden szolgáltatástípus-példányhoz hozzá van rendelve egy URI-névhatókör a megnevezett alkalmazás URI-ja alatt. Ha például egy "MyNamedApp" nevű alkalmazáson belül hoz létre egy "MyDatabase" nevű szolgáltatást, az URI így néz ki: *fabric:/MyNamedApp/MyDatabase*. Egy elnevezett alkalmazáson belül létrehozhat egy vagy több elnevezett szolgáltatást. Minden elnevezett szolgáltatás rendelkezhet saját partíciósémával és példány/replikaszámmal. 

Kétféle szolgáltatás létezik: állapot nélküli és állapotalapú. Az állapotnélküli szolgáltatások nem tárolják az állapotot a szolgáltatáson belül. Az állapotmentes szolgáltatások egyáltalán nem rendelkeznek állandó tárhellyel, vagy egy külső tárolási szolgáltatásban, például az Azure Storage, az Azure SQL Database vagy az Azure Cosmos DB tárolóban tárolnak állandó állapotot. Az állapotalapú szolgáltatás tárolja állapot a szolgáltatáson belül, és használja a Megbízható gyűjtemények vagy a Reliable Actors programozási modellek állapot kezeléséhez. 

Elnevezett szolgáltatás létrehozásakor partíciósémát kell megadnia. Nagy mennyiségű állapotú szolgáltatások az adatok partíciók között felosztása. Minden partíció felelős a szolgáltatás teljes állapotának egy részéért, amely a fürt csomópontjai között van elosztva.  

Az alábbi ábra az alkalmazások és a szolgáltatáspéldányok, partíciók és replikák közötti kapcsolatot mutatja be.

![Partíciók és replikák egy szolgáltatáson belül][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Particionálás, méretezés és rendelkezésre állás
[Particionálás](service-fabric-concepts-partitioning.md) nem egyedi a Service Fabric. A particionálás jól ismert formája az adatparticionálás vagy a szilánkok. Állapotalapú szolgáltatások nagy mennyiségű állapot felosztása az adatok partíciók között. Minden partíció felelős a szolgáltatás teljes állapotának egy részéért. 

Az egyes partíciók replikái a fürt csomópontjai között vannak elosztva, így a megnevezett szolgáltatás állapota [méretezhető.](service-fabric-concepts-scalability.md) Az adatigények növekedésével a partíciók növekednek, és a Service Fabric újraegyensúlyozza a partíciókat a csomópontok között a hardvererőforrások hatékony kihasználása érdekében. Ha új csomópontokat ad hozzá a fürthöz, a Service Fabric újraegyensúlyozza a partícióreplikákat a megnövekedett számú csomópont között. Az alkalmazások általános teljesítménye javul, és csökken a memóriahoz való hozzáférés rekedése. Ha a fürt csomópontjait nem használja hatékonyan, csökkentheti a csomópontszámát a fürtben. A Service Fabric újra egyensúlyba hozza a partícióreplikákat a csomópontok számának csökkenésével, hogy jobban kihasználja a hardvert az egyes csomópontokon.

Egy partíción belül állapot nélküli elnevezett szolgáltatások példányok, míg állapotalapú elnevezett szolgáltatások replikák. Általában az állapotnélküli elnevezett szolgáltatások csak egy partícióval rendelkeznek, mivel nincs belső állapotuk. A partíciópéldányok [biztosítják a rendelkezésre állást.](service-fabric-availability-services.md) Ha egy példány meghibásodik, más példányok továbbra is normálisan működnek, és majd a Service Fabric létrehoz egy új példányt. Az állapotalapú elnevezett szolgáltatások állapotukat a replikákon belül tartják fenn, és minden partíció saját replikakészletet rendelkezik. Az olvasási és írási műveletek et egy replika (az elsődleges) hajtja végre. Az írási műveletek állapotának módosításait a rendszer több más replikára (aktív másodlagos állapotokra) replikálja. Ha egy replika sikertelen, a Service Fabric új replika a meglévő replika.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Állapot nélküli és állapotalapú mikroszolgáltatások a Service Fabrichez
A Service Fabric segítségével mikroszolgáltatásokból vagy tárolókból álló alkalmazásokat építhet. Az állapot nélküli mikroszolgáltatások (például a protokollátjárók és webproxyk) nem tartanak fenn változtatható állapotot a kéréseken és a szolgáltatástól ezekre kapott válaszokon kívül. Az Azure Cloud Services feldolgozói szerepkörei például állapotmentes szolgáltatások. Az állapotalapú mikroszolgáltatások (például felhasználói fiókok, adatbázisok, eszközök, kosarak és üzenetsorok) változtatható, mérvadó állapotot tartanak fenn a kéréseken és a válaszokon kívül is. A modern webes alkalmazások állapot nélküli és állapotalapú mikroszolgáltatások kombinációjából állnak. 

A Service Fabric egyik legfontosabb különbsége az, hogy nagy hangsúlyt fektet az állapotalapú szolgáltatások kiépítésére, akár a [beépített programozási modellekkel,](service-fabric-choose-framework.md) akár a tárolós állapotalapú szolgáltatásokkal. Az [alkalmazás-forgatókönyvek](service-fabric-application-scenarios.md) az állapotalapú szolgáltatások használatát mutatják be.

Miért stateful mikroszolgáltatások mellett állapotmentes is? A két fő ok a következő:

* Nagy átviteli sebességű, alacsony késésű, hibatűrő online tranzakciófeldolgozási (OLTP) szolgáltatásokat hozhat létre a kód és az adatok közel tartásával ugyanazon a gépen. Néhány példa az interaktív kirakatokra, a keresésre, az eszközök internetes hálózatára (IoT) rendszerekre, a kereskedési rendszerekre, a hitelkártya-feldolgozásra és a csalásészlelési rendszerekre, valamint a személyes adatrekordok kezelésére.
* Egyszerűsítheti az alkalmazástervezést. Állapotalapú mikroszolgáltatások szükségtelenné kell tenni a további várólisták és gyorsítótárak, amelyek hagyományosan szükségesek a rendelkezésre állási és késési követelmények egy tisztán állapotmentes alkalmazás. Az állapotalapú szolgáltatások természetesen magas rendelkezésre állásúak és alacsony késleltetésűek, ami csökkenti az alkalmazás egészében kezelendő mozgó alkatrészek számát.

## <a name="supported-programming-models"></a>Támogatott programozási modellek
A Service Fabric többféle módon írhatja és kezelheti a szolgáltatásokat. A szolgáltatások a Service Fabric API-k segítségével teljes mértékben kihasználhatják a platform szolgáltatásait és alkalmazáskeretrendszeri szolgáltatásait. A szolgáltatások bármely, bármely nyelven írt és service fabric-fürtön üzemeltetett lefordított végrehajtható program is lehetnek. További információt a [Támogatott programozási modellek című témakörben talál.](service-fabric-choose-framework.md)

### <a name="containers"></a>Containers
Alapértelmezés szerint a Service Fabric folyamatokként telepíti és aktiválja a szolgáltatásokat. A Service Fabric [tárolókban](service-fabric-containers-overview.md)is üzembe helyezhetszolgáltatásokat. Fontos, hogy a szolgáltatások a folyamatok és szolgáltatások tárolókban ugyanabban az alkalmazásban. A Service Fabric támogatja a Linux-tárolók és a Windows-tárolók telepítését a Windows Server 2016 rendszeren. Meglévő alkalmazásokat, állapotmentes szolgáltatásokat vagy állapotalapú szolgáltatásokat helyezhet üzembe tárolókban. 

### <a name="reliable-services"></a>Reliable Services
[A Reliable Services](service-fabric-reliable-services-introduction.md) egy könnyű keretrendszer olyan szolgáltatások írásához, amelyek integrálhatók a Service Fabric platformmal, és a platform összes szolgáltatásának előnyeit élvezhetik. A megbízható szolgáltatások állapot nélküliek lehetnek (hasonlóan a legtöbb szolgáltatásplatformhoz, például a webkiszolgálókhoz vagy a feldolgozói szerepkörökhöz az Azure Cloud Servicesben), ahol az állapot egy külső megoldásban, például az Azure DB-ben vagy az Azure Table Storage-ban megmarad. Megbízható szolgáltatások is lehet állapotalapú, ahol az állapot közvetlenül a szolgáltatás ban is megbízható gyűjtemények használatával. Állapot magas [rendelkezésre állású](service-fabric-availability-services.md) replikációrévén, és [particionálás](service-fabric-concepts-partitioning.md)sal elosztott, a Service Fabric által automatikusan felügyelt állapot.

### <a name="reliable-actors"></a>Reliable Actors
A megbízható szolgáltatások, a [reliable actor](service-fabric-reliable-actors-introduction.md) keretrendszer egy alkalmazás keretrendszer, amely megvalósítja a virtuális aktor minta, az aktor tervezési minta alapján. A megbízható szereplő keretrendszer független számítási és állapot-egységeket használ egyszálas végrehajtással, az úgynevezett szereplőkkel. A Megbízható szereplő keretrendszer beépített kommunikációt biztosít a szereplők és az előre beállított állapotmegőrzési és horizontális felskálázási konfigurációk számára.

### <a name="aspnet-core"></a>ASP.NET-mag
A Service Fabric integrálható [ASP.NET Core-nal,](service-fabric-reliable-services-communication-aspnetcore.md) mint első osztályú programozási modell webes és API-alkalmazások létrehozásához.  ASP.NET Core kétféleképpen használható a Service Fabricben:

- Vendég végrehajtható fájlként üzemeltetve. Ez elsősorban a meglévő ASP.NET Core alkalmazások futtatásához a Service Fabric kódmódosítások nélkül.
- Megbízható szolgáltatáson belül futva. Ez lehetővé teszi a Service Fabric futásidejű jobb integrációt, és lehetővé teszi az állapotalapú ASP.NET Core szolgáltatások.

### <a name="guest-executables"></a>Vendég végrehajtható fájlok
A [vendég végrehajtható egy](service-fabric-guest-executables-introduction.md) meglévő, tetszőleges végrehajtható (bármilyen nyelven írt) üzemelteti a Service Fabric-fürtön más szolgáltatások mellett. A vendég végrehajtható fájlok nem integrálhatók közvetlenül a Service Fabric API-kkal. Azonban továbbra is kihasználják a platform által nyújtott funkciók, például az egyéni állapot- és terhelésjelentési és szolgáltatásfelderíthetőség a REST API-k hívásával. Ők is teljes körű alkalmazás életciklus-támogatás. 

## <a name="application-lifecycle"></a>Alkalmazás-életciklus
Más platformokhoz is a Service Fabric-en futó alkalmazások általában a következő fázisokon megy keresztül: tervezés, fejlesztés, tesztelés, üzembe helyezés, frissítés, karbantartás és eltávolítás. A Service Fabric első osztályú támogatást nyújt a felhőalapú alkalmazások teljes alkalmazáséletciklusához, a fejlesztéstől a telepítésen át a napi felügyeletig és a karbantartásig, az esetleges leszerelésig. A szolgáltatásmodell lehetővé teszi, hogy több különböző szerepkörök egymástól függetlenül vegyenek részt az alkalmazás életciklusában. [A Service Fabric-alkalmazás életciklusa](service-fabric-application-lifecycle.md) áttekintést nyújt az API-król és arról, hogyan használják azokat a különböző szerepkörök a Service Fabric-alkalmazás életciklusának fázisaiban. 

A teljes alkalmazáséletciklus [PowerShell-parancsmagokkal](/powershell/module/ServiceFabric/), [CLI-parancsokkal,](service-fabric-sfctl.md) [C# API-kkal,](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) [Java API-kkal](/java/api/overview/azure/servicefabric)és [REST API-kkal](/rest/api/servicefabric/)kezelhető. Folyamatos integrációs/folyamatos üzembe helyezési folyamatokat is beállíthat olyan eszközökkel, mint az [Azure Pipelines](service-fabric-set-up-continuous-integration.md) vagy a [Jenkins.](service-fabric-cicd-your-linux-applications-with-jenkins.md)

## <a name="test-applications-and-services"></a>Alkalmazások és szolgáltatások tesztelése
A valóban felhőszintű szolgáltatások létrehozásához fontos annak ellenőrzése, hogy az alkalmazások és szolgáltatások ellenállnak-e a valós hibáknak. A hibaelemzési szolgáltatás a Service Fabric-re épülő szolgáltatások tesztelésére szolgál. A [Hibaelemzési szolgáltatással](service-fabric-testability-overview.md)érdemi hibákat hozhat ki, és teljes tesztforgatókönyveket futtathat az alkalmazásokon. Ezek a hibák és forgatókönyvek gyakorolják és érvényesítik a számos állapotot és átmenetet, amelyeket egy szolgáltatás az egész élettartama alatt tapasztal, mindezt ellenőrzött, biztonságos és következetes módon.

[A műveletek](service-fabric-testability-actions.md) egy szolgáltatást céloznak meg az egyes hibák teszteléséhez. A szolgáltatásfejlesztő használhatja ezeket építőelemként bonyolult forgatókönyvek írásához. Szimulált hibák például a következők:

* Indítsa újra a csomópontot, hogy szimulálja a számos olyan helyzetben, amikor egy gép vagy virtuális gép újraindul.
* Az állapotalapú szolgáltatás kópiájának áthelyezése a terheléselosztás, a feladatátvétel vagy az alkalmazásfrissítés szimulálása érdekében.
* Kvórumveszteség meghívása egy állapotalapú szolgáltatás on hozzon létre egy olyan helyzetet, ahol írási műveletek nem folytatható, mert nincs elég "back-up" vagy "másodlagos" replikák az új adatok fogadásához.
* Adatvesztés meghívása egy állapotalapú szolgáltatás olyan helyzetet hoz létre, amelyben az összes memóriában állapot teljesen törlődik.

[A forgatókönyvek](service-fabric-testability-scenarios.md) összetett műveletek, amelyek egy vagy több műveletből állnak. A Hibaelemzési szolgáltatás két beépített teljes forgatókönyvet biztosít:

* [Chaos forgatókönyv](service-fabric-controlled-chaos.md)- szimulálja a folyamatos, áthatárolt hibák (kecses és ungraceful) az egész fürt hosszabb ideig.
* [Feladatátvételi forgatókönyv](service-fabric-testability-scenarios.md#failover-test)– a chaos teszt forgatókönyv, amely egy adott szolgáltatáspartíciót céloz meg, miközben más szolgáltatások változatlan.

## <a name="clusters"></a>Fürtök
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatások üzembe helyezése és kezelése történik. A fürtök több ezer gépre skálázhatók. A fürt részét képezi gépet vagy virtuális gépet fürtcsomópontnak nevezzük. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). A csomópontok jellemzőkkel, például elhelyezési tulajdonságokkal rendelkeznek. Minden gép vagy virtuális gép rendelkezik `FabricHost.exe`egy automatikus indítási szolgáltatással, amely elindul a rendszerindításkor, majd elindítja a két végrehajtható fájlt: a Fabric.exe és a FabricGateway.exe. Ez a két végrehajtható fájl alkotja a csomópontot. Tesztelési forgatókönyvek esetén több csomópontot is üzemeltethet egyetlen gépen vagy `Fabric.exe` virtuális `FabricGateway.exe`gépen, ha több példányt futtat a és a.

A Service Fabric-fürtök Windows Server vagy Linux operációs rendszert futtató virtuális vagy fizikai gépeken hozhatók létre. A Service Fabric-alkalmazásokat bármilyen környezetben telepítheti és futtathatja, ahol összekapcsolt Windows Server vagy Linux rendszerű számítógépekkel rendelkezik: a helyszíni, a Microsoft Azure-on vagy bármely felhőszolgáltatón.

### <a name="clusters-on-azure"></a>Fürtök az Azure-on
A Service Fabric-fürtök Azure-on való futtatása integrációt biztosít más Azure-funkciókkal és -szolgáltatásokkal, ami megkönnyíti és megbízhatóbbá teszi a fürt működését és felügyeletét. A fürt egy Azure Resource Manager-erőforrás, így az Azure-ban más erőforrásokhoz hasonlóan modellezheti a fürtöket. Az Erőforrás-kezelő a fürt által egyetlen egységként használt összes erőforrás egyszerű kezelését is biztosítja. Az Azure-beli fürtök integrálva vannak az Azure-diagnosztika és az Azure Monitor-naplókkal. A fürtcsomópont-típusok [virtuálisgép-méretezési készletek,](/azure/virtual-machine-scale-sets/index)így az automatikus skálázási funkciók be vannak építve.

Fürtöt az [Azure-portálon](service-fabric-cluster-creation-via-portal.md)keresztül hozhat létre egy [sablonból](service-fabric-cluster-creation-via-arm.md)vagy a [Visual Studio-ból.](service-fabric-cluster-creation-via-visual-studio.md)

Service Fabric Linux lehetővé teszi, hogy magas rendelkezésre állású, jól méretezhető alkalmazások linuxon, mint a Windows. A Service Fabric-keretrendszerek (Reliable Services és Reliable Actors) a C# (.NET Core) mellett Java-ban érhetők el Linuxon. Vendég végrehajtható [szolgáltatásokat](service-fabric-guest-executables-introduction.md) is létrehozhat bármilyen nyelven vagy keretrendszerrel. A Docker-tárolók vezénylése is támogatott. A Docker-tárolók futtathatnak vendég végrehajtható fájlokat vagy natív Service Fabric-szolgáltatásokat, amelyek a Service Fabric-keretrendszereket használják. További információ: [Service Fabric linuxos](service-fabric-deploy-anywhere.md).

Vannak olyan funkciók, amelyek et a Windows támogatja, de nem linuxos. További információért olvassa el [a Linux on és a Windows szolgáltatásháló közötti különbségeket.](service-fabric-linux-windows-differences.md)

### <a name="standalone-clusters"></a>Önálló fürtök
A Service Fabric egy telepítőcsomagot biztosít, amely önálló Service Fabric-fürtöket hozhat létre a helyszínen vagy bármely felhőszolgáltatón. Az önálló fürtök szabadon üzemeltetheti konstitovát, ahol csak szeretné. Ha az adatok megfelelőségi vagy szabályozási korlátozások hatálya alá tartoznak, vagy az adatokat helyi szinten szeretné tartani, saját fürtöt és alkalmazásokat üzemeltethet. A Service Fabric-alkalmazások több üzemeltetési környezetben is futtathatók, módosítások nélkül, így az alkalmazások létrehozásának ismerete az egyik üzemeltetési környezetből a másikba vihető át. 

[Az első önálló Service Fabric-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)

A Linux önálló fürtjei még nem támogatottak.

### <a name="cluster-security"></a>Fürtbiztonság
A fürtöket biztonságosnak kell tekinteni, hogy illetéktelen felhasználók ne csatlakozhassanak a fürthöz, különösen akkor, ha éles számítási feladatok futnak rajta. Bár lehetőség van nem biztonságos fürt létrehozására, ez lehetővé teszi a névtelen felhasználók számára, hogy csatlakozzanak hozzá, ha a felügyeleti végpontok a nyilvános internetnek vannak kitéve. Nem lehetséges később engedélyezni a biztonságot egy nem biztonságos fürtön: a fürtbiztonság a fürt létrehozásakor engedélyezve van.

A fürt biztonsági forgatókönyvei a következők:
* Csomópont-csomópont biztonság
* Ügyfél-csomópont biztonság
* Szerepköralapú hozzáférés-vezérlés (RBAC)

További információ: [Fürt biztosítása](service-fabric-cluster-security.md).

### <a name="scaling"></a>Méretezés
Ha új csomópontokat ad hozzá a fürthöz, a Service Fabric újraegyensúlyozza a partícióreplikákat és példányokat a megnövekedett számú csomópont között. Az alkalmazások általános teljesítménye javul, és csökken a memóriahoz való hozzáférés rekedése. Ha a fürt csomópontjait nem használja hatékonyan, csökkentheti a csomópontszámát a fürtben. A Service Fabric újra egyensúlyba hozza a partícióreplikákat és példányokat a csomópontok számának csökkenésével, hogy jobban kihasználja a hardvert az egyes csomópontokon. Az Azure-ban [manuálisan](service-fabric-cluster-scale-up-down.md) vagy programozott módon skálázhatja a [fürtöket.](service-fabric-cluster-programmatic-scaling.md) Az önálló fürtök [manuálisan](service-fabric-cluster-windows-server-add-remove-nodes.md)méretezhetők.

### <a name="cluster-upgrades"></a>Fürtfrissítések
Rendszeresen a Service Fabric-futásidejű új verziói felszabadulnak. Futásidejű vagy hálós frissítéseket hajtson végre a fürtön, hogy mindig [támogatott verziót](service-fabric-support.md)futtasson. A hálófrissítések mellett frissítheti a fürtkonfigurációt, például a tanúsítványokat vagy az alkalmazásportokat is.

A Service Fabric-fürt olyan erőforrás, amely az Ön tulajdonában van, de részben a Microsoft kezeli. A Microsoft felelős az alapul szolgáló operációs rendszer javításáért és a hálófrissítések végrehajtásáért a fürtön. Beállíthatja, hogy a fürt automatikus hálófrissítéseket kapjon, amikor a Microsoft új verziót ad ki, vagy kiválaszthatja a kívánt támogatott hálóverziót. A Fabric és a configuration frissítések az Azure Portalon vagy az Erőforrás-kezelőn keresztül állíthatók be. További információ: [Service Fabric-fürt frissítése.](service-fabric-cluster-upgrade.md) 

Az önálló fürt olyan erőforrás, amely teljes egészében a tulajdonában van. Ön felelős az alapul szolgáló operációs rendszer javításáért és a háló frissítésekének megküldéséért. Ha a fürt [https://www.microsoft.com/download](https://www.microsoft.com/download)csatlakozhat, beállíthatja, hogy a fürt automatikusan töltse le és hozza létre az új Service Fabric futásidejű csomagot. Ezután kezdeményezze a frissítést. Ha a fürt nem [https://www.microsoft.com/download](https://www.microsoft.com/download)tud hozzáférni, manuálisan letöltheti az új futásidejű csomagot egy internethez csatlakoztatott gépről, majd kezdeményezheti a frissítést. További információért olvassa el [az Önálló service Fabric-fürt frissítése](service-fabric-cluster-upgrade-windows-server.md)című olvasnivalót.

## <a name="health-monitoring"></a>Állapotfigyelés
A Service Fabric egy [állapotmodellt](service-fabric-health-introduction.md) vezet be, amely nek megfelelő állapotú fürt és alkalmazásfeltételek et kell megjelölni e adott entitásokon (például fürtcsomópontokon és szolgáltatásreplikákon). Az egészségügyi modell egészségügyi riporterek (rendszer-összetevők és figyelők) használ. A cél az egyszerű és gyors diagnózis és javítás. Szolgáltatás írók kell gondolni előre az egészségügyi és hogyan [kell tervezni az egészségügyi jelentések](service-fabric-report-health.md#design-health-reporting). Minden olyan feltételt, amely hatással lehet az egészségre kell jelenteni, különösen akkor, ha segíthet a root közelében felmerülő problémák megjelölése. Az állapotinformációk időt és energiát takaríthat meg a hibakeresés és a vizsgálat, ha a szolgáltatás éles környezetben nagy méretekben működik.

A Service Fabric-riporterek figyelik az azonosított érdeklődési feltételeket. Ezekről a feltételekről a helyi nézetük alapján számolnak be. Az [állapottároló](service-fabric-health-introduction.md#health-store) összesíti az összes adatkezelő által küldött állapotadatokat annak megállapítására, hogy az entitások globálisan kifogástalanok-e. A modell célja, hogy gazdag, rugalmas, és könnyen használható. Az állapotjelentések minősége határozza meg a fürt állapotnézetének pontosságát. A nem megfelelő állapotú problémákat helytelenül mutató hamis pozitív pozitív adatok negatívan befolyásolhatják a frissítéseket vagy más, állapotadatokat használó szolgáltatásokat. Ilyen szolgáltatások például a javítási szolgáltatások és a riasztási mechanizmusok. Ezért néhány gondolat van szükség, hogy a jelentések, hogy elfog feltételeket az érdeklődés a lehető legjobb módon.

A jelentés a következő innen végezhető el:
* A figyelt Service Fabric szolgáltatás replika vagy példány.
* Belső figyelők egy Service Fabric-szolgáltatás (például egy Service Fabric állapotmentes szolgáltatás, amely figyeli a feltételeket és jelentéseket a központi üzembe helyezés. A figyelők minden csomóponton telepíthetők, vagy affinitized a figyelt szolgáltatás.
* Belső figyelők, amelyek a Service Fabric-csomópontokon futnak, de nem implementálva Service Fabric-szolgáltatásokként.
* Külső figyelők, amelyek az erőforrást a Service Fabric-fürtön kívülről (például a figyelési szolgáltatás, például a Gomez) mintavételezi.

A boxból kimondott, Service Fabric-összetevők a fürt összes entitásának állapotát jelenti. [A rendszerállapot-jelentések](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) betekintést nyújtanak a fürt- és alkalmazásfunkciókba, és az állapoton keresztül megjelölik a problémákat. Alkalmazások és szolgáltatások esetén a rendszerállapot-jelentések ellenőrzik, hogy az entitások vannak-e megvalósítva, és a Service Fabric futásidejű szempontjából megfelelően viselkednek.For applications and services, system health reports verify that entitások are implemented and are behaving correct ly from the perspective of the Service Fabric runtime. A jelentések nem biztosítanak a szolgáltatás üzleti logikájának állapotfigyelését, és nem észlelik azokat a folyamatokat, amelyek nem válaszolnak. A szolgáltatás logikájára vonatkozó állapotadatok hozzáadásához [valósítsa meg az egyéni állapotjelentéseket](service-fabric-report-health.md) a szolgáltatásokban.

A Service Fabric többféle lehetőséget kínál az állapotfigyelők állapotfigyelők [állapotfigyelőinek megtekintésére](service-fabric-view-entities-aggregated-health.md) az állapottárolóban:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) vagy más vizualizációs eszközök.
* Állapotlekérdezések [(a PowerShell](/powershell/module/ServiceFabric/), [CLI](service-fabric-sfctl.md), a [C # FabricClient API-k](/dotnet/api/system.fabric.fabricclient.healthclient) és a [Java FabricClient API-k,](/java/api/system.fabric)vagy [REST API-k).](/rest/api/servicefabric)
* Általános lekérdezések, amelyek a tulajdonságok egyikeként (a PowerShellen, a CLI-n, az API-kon vagy a REST-en keresztül) állapottal rendelkező entitások listáját adják vissza.

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika
[A figyelés és a diagnosztika](service-fabric-diagnostics-overview.md) kritikus fontosságú az alkalmazások és szolgáltatások bármilyen környezetben történő fejlesztése, tesztelése és üzembe helyezése szempontjából. A Service Fabric-megoldások akkor működnek a legjobban, ha olyan figyelést és diagnosztikát tervez és valósít meg, amely biztosítja, hogy az alkalmazások és szolgáltatások a helyi fejlesztési környezetben vagy az éles környezetben a várt módon működjenek.

A monitorozás és a diagnosztika fő célja:

- Hardver- és infrastruktúraproblémák észlelése és diagnosztizálása
- Szoftver- és alkalmazásproblémák észlelése, a szolgáltatás állásidejének csökkentése
- Az erőforrás-felhasználás megértése és a műveletekre vonatkozó döntések ösztönzése
- Az alkalmazás-, szolgáltatás- és infrastruktúra-teljesítmény optimalizálása
- Üzleti elemzések létrehozása és a fejlesztés területeinek azonosítása
 
A monitorozás és diagnosztika általános munkafolyamata három lépésből áll:

1. Esemény generálása: ez magában foglalja az eseményeket (naplók, nyomkövetések, egyéni események) az infrastruktúrában (fürt), a platformon és az alkalmazás/ szolgáltatás szintjén
2. Eseményösszesítés: a létrehozott eseményeket össze kell gyűjteni és összesíteni kell, mielőtt megjelennének
3. Elemzés: az eseményeket valamilyen formátumban meg kell jeleníteni és hozzáférhetővé kell tenni, hogy szükség esetén elemzésre és megjelenítésre kerülhessenek

Több termék áll rendelkezésre, amelyek lefedik ezt a három területet, és szabadon választhat különböző technológiákat mindegyikhez. További információért olvassa el [az Azure Service Fabric figyelésének és diagnosztikájának](service-fabric-diagnostics-overview.md)című olvasni.

## <a name="next-steps"></a>További lépések
* Megismerkedhet egy [azure-beli fürt](service-fabric-cluster-creation-via-portal.md) vagy egy [önálló windowsbeli fürt](service-fabric-cluster-creation-for-windows-server.md) létrehozásával.
* Próbáljon szolgáltatást létrehozni a [Reliable Services](service-fabric-reliable-services-quick-start.md) vagy a [Reliable Actors](service-fabric-reliable-actors-get-started.md) programozási modellek használatával.
* További információ a [Felhőszolgáltatásokból való áttelepítésről.](service-fabric-cloud-services-migration-differences.md)
* Ismerje meg a [szolgáltatások figyelése és diagnosztizálása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Ismerje meg [az alkalmazások és szolgáltatások tesztelését.](service-fabric-testability-overview.md)
* Ismerje meg a [fürterőforrások kezelését és vezénylését.](service-fabric-cluster-resource-manager-introduction.md)
* Tekintse át a [Service Fabric-mintákat.](https://aka.ms/servicefabricsamples)
* További információ a [Service Fabric támogatási lehetőségeiről.](service-fabric-support.md)
* Olvassa el a [csapat blog](https://blogs.msdn.microsoft.com/azureservicefabric/) cikkek és közlemények.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
