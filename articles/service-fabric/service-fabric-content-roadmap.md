---
title: További információ az Azure Service Fabric |} A Microsoft Docs
description: Ismerje meg az alapfogalmak és az Azure Service Fabric főbb információkat. A Service Fabric és a mikroszolgáltatások létrehozása kiterjesztett áttekintést nyújt.
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
ms.openlocfilehash: 58db410fe5a6c2b081507eae2ccad3a258ec0864
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427590"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Így biztosan ismerje meg a Service Fabric?
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését.  A Service Fabric rendelkezik egy nagy támadási azonban és sok további.  Ez a cikk egy szinopszist, Service fabric biztosítja, és ismerteti az alapfogalmakat, programozási modelleket, alkalmazás-életciklus során, tesztelés, fürtök és az állapotmonitorozást. Olvassa el a [áttekintése](service-fabric-overview.md) és [Mik azok a mikroszolgáltatások?](service-fabric-overview-microservices.md) bevezetést, és hogyan a Service Fabric mikroszolgáltatások létrehozására használható. Ez a cikk egy átfogó tartalmak listája nem tartalmaz, de a hivatkozások áttekintése és első lépéseket bemutató cikkekben a Service Fabric minden területéhez. 

## <a name="core-concepts"></a>Alapfogalmak
[A Service Fabric terminológiája](service-fabric-technical-overview.md), [alkalmazásmodell](service-fabric-application-model.md), és [támogatott programozási modellek](service-fabric-choose-framework.md) további fogalmakat és leírások, de az alapokat az alábbiakban.

<table><tr><th>Alapfogalmak</th><th>Tervezési idő</th><th>Futási idő</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-application-type-service-type-application-package-and-manifest-service-package-and-manifest"></a>Tervezési idő: alkalmazástípus, szolgáltatástípus, virtuálisalkalmazás-csomag és a jegyzékfájl, szolgáltatáscsomag és manifest
Az alkalmazástípus hozzárendeli egy gyűjteményhez típusú szolgáltatás neve/verziója. Van definiálva: egy *ApplicationManifest.xml* fájlt, amely egy alkalmazás-csomag könyvtárában van beágyazva. Az alkalmazáscsomag majd át lesznek másolva a Service Fabric-fürt lemezképtárolójába. Tento typ aplikace, majd futtatja a fürtön belül, amely ezután létrehozhat egy elnevezett alkalmazást. 

A szolgáltatás típus egy szolgáltatás kódcsomagok, adatok csomagokat és konfigurációs csomagokat rendelt neve/verziója. A ServiceManifest.xml fájlban van ágyazva egy szolgáltatási csomag könyvtárában van definiálva. A szolgáltatási csomag könyvtárában majd hivatkozik egy alkalmazáscsomagot *ApplicationManifest.xml* fájlt. A fürtön belüli után egy elnevezett alkalmazást hozhat létre egy elnevezett szolgáltatás az alkalmazástípus szolgáltatás típusok közül. A szolgáltatás írja le annak *ServiceManifest.xml* fájlt. A szolgáltatás típusának végrehajtható kód szolgáltatás konfigurációs beállításokat, amelyek futási időben töltődnek be, és a statikus adatok, amelyek a szolgáltatás által felhasznált tevődik össze.

![A Service Fabric alkalmazás és szolgáltatás típusainak][cluster-imagestore-apptypes]

Az alkalmazáscsomag egy lemez könyvtárra, amelyben az alkalmazás típusának *ApplicationManifest.xml* fájlt, amely hivatkozik a szolgáltatási csomagokat az egyes szolgáltatástípusokról, hogy az alkalmazás típusát. Például egy e-mailek alkalmazástípus az alkalmazáscsomag tartalmazhat várólista szolgáltatáscsomagot, egy előtérbeli service-csomag és egy adatbázis-szolgáltatás csomag mutató hivatkozásokat. A Service Fabric-fürt lemezképtárolójába lesz másolva a fájlokat az alkalmazás-csomag könyvtárában. 

Egy szolgáltatási csomagot egy lemez könyvtárra, amelyben a szolgáltatás típusának *ServiceManifest.xml* fájlt, amely a kódban, a statikus adatok, és a konfigurációs csomagokat a service Type hivatkozik. A fájlok a szolgáltatási csomag könyvtárában az alkalmazástípus által hivatkozott *ApplicationManifest.xml* fájlt. Például egy szolgáltatási csomagot sikerült tekintse meg a kódot, a statikus adatok és a egy adatbázis-szolgáltatás alkotó konfigurációs csomagokat.

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Futási idő: fürtök és a csomópontok, alkalmazások, szolgáltatások, a partíciók és a replikák nevű nevű
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) virtuális és fizikai gépek hálózaton keresztül csatlakozó készlete, amelyen mikroszolgáltatásokat helyezhet üzembe és felügyelhet. A fürtök több ezer gépre skálázhatók.

A fürtök részét képező gépeket vagy virtuális gépeket csomópontoknak nevezzük. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). A csomópontok jellemzőkkel, például elhelyezési tulajdonságokkal rendelkeznek. Minden számítógép vagy virtuális gép rendelkezik egy automatikusan induló Windows szolgáltatás `FabricHost.exe`, amelyek rendszerindítási követően elindul, és elindítja a két végrehajtható fájlok: `Fabric.exe` és `FabricGateway.exe`. E két végrehajtható fájlok a csomóponton alkotják. Fejlesztési vagy tesztelési célokra, több példányát futtatja egy egyetlen gépen vagy a virtuális gép több csomópont is üzemeltethet `Fabric.exe` és `FabricGateway.exe`.

A nevesített alkalmazás elnevezett szolgáltatások gyűjteménye, amely egy bizonyos funkciója vagy funkciói hajt végre e. A szolgáltatás hajtja végre a teljes és a különálló függvény (képes elindítani és más szolgáltatások függetlenül futnak) és a kódot, konfiguráció és adatok tevődik össze. Után egy alkalmazáscsomagot a lemezképtárolójába másolja, hozzon létre a fürtön belül az alkalmazás egy példányát adja meg az alkalmazáscsomag alkalmazástípus (a neve/verziója használatával). Írja be mindegyik alkalmazáspéldány hozzá van rendelve egy hasonló öröklési URI nevet *fabric: / MyNamedApp*. A fürtön belül több elnevezett alkalmazást hozhat létre egy egyetlen alkalmazás írja be. Elnevezett alkalmazásokat is létrehozhat különböző alkalmazástípusokat. Minden elnevezett alkalmazás egymástól függetlenül, felügyelt és verziószámmal.

Miután létrehozott egy elnevezett alkalmazást, a szolgáltatás különböző (egy elnevezett szolgáltatás) egy példányát létrehozhat a fürtön belül a szolgáltatás típusa (a neve/verziója használatával) megadásával. Minden szolgáltatáspéldány típus URI nevét az elnevezett alkalmazást URI alatt hatóköre van hozzárendelve. Például, ha létrehoz egy "MyDatabase" nevű, "MyNamedApp" nevű application szolgáltatás, az URI-t néz ki: *fabric: / MyNamedApp/MyDatabase*. Egy elnevezett alkalmazáson belül egy vagy több névvel ellátott szolgáltatást hozhat létre. Minden elnevezett szolgáltatás lehet a saját partícióséma és példány/replika száma. 

A szolgáltatások két típusa van: állapot nélküli és állapotalapú. Állapotmentes szolgáltatások ne tárolja a szolgáltatás állapotát. Állapotmentes szolgáltatások nincsenek állandó tároló minden rendelkezik, vagy állandó állapot külső storage-szolgáltatás, például az Azure Storage, Azure SQL Database vagy Azure Cosmos DB tárolja. Az állapotalapú szolgáltatások állapota a szolgáltatásban tárolja, és a Reliable Collections vagy a Reliable Actors programozási modell segítségével állapot kezelése. 

Egy elnevezett szolgáltatás létrehozásakor meg kell adnia egy partícióséma. Szolgáltatások állapota nagy mennyiségű felosztani az adatokat a partíciók között. Mindegyik partíció feladata a teljes állapotát a szolgáltatásnak, amely a fürt csomópontjai között megoszlik egy részét.  

Az alábbi ábrán látható az alkalmazások és a szolgáltatáspéldány, a partíciók és a replikák közötti kapcsolat.

![Partíciók és a egy szolgáltatáson belüli replikák][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>A particionálás, méretezését és rendelkezésre állás
[A particionálás](service-fabric-concepts-partitioning.md) , nem egyedi a Service Fabric. Egy jól ismert űrlap particionálás, az adatparticionálás és horizontális particionálás. A nagy mennyiségű állapota, az állapotalapú szolgáltatások felosztani az adatokat a partíciók között. Mindegyik partíció felelős a szolgáltatás teljes állapota egy részét. 

A replikákat az egyes partíciók csomópontokon vannak elosztva a fürtben, amely lehetővé teszi, hogy az adott szolgáltatás állapotának [méretezési](service-fabric-concepts-scalability.md). Az adatok igények nő, partíciók nő, és a Service Fabric partíciók módon használhatják a hardvererőforrás-csomópontokon keresztüli újra egyensúlyba hozza. Ha új csomópontot ad hozzá a fürthöz, Service Fabric lesz újra egyensúlyoznia a partícióreplikákat az megnövekedett számú csomópontok között. A teljes javítja az alkalmazások teljesítményéről, és csökkenti a versengés memória való hozzáféréshez. Ha a fürt csomópontjainak hatékonyan nincsenek használatban, csökkentheti a fürtben található csomópontok számát. A Service Fabric a partíciók replikáit újra rebalances csökkent csomópontszámának jobb kihasználása érdekében a hardver minden egyes csomóponton keresztül.

Egy partíción belül elnevezett állapotmentes szolgáltatások példánya lehet, amíg elnevezett állapotalapú szolgáltatások replikával rendelkeznek. Általában nevesített állapotmentes szolgáltatások csak egyre rendelkeznek egy partíciót, mivel nincs belső állapot rendelkeznek. Adja meg a partíció-példányok [rendelkezésre állási](service-fabric-availability-services.md). Ha egy példány nem sikerül, más példányra továbbra is a szokott, és ekkor a Service Fabric létrehoz egy új példányt. Az állapotalapú szolgáltatások nevű karbantartása, a replikák és minden egyes partíción belül állapotuk rendelkezik a saját replika beállítása. Olvasási és írási műveletek egy replika (az elsődleges néven) történik. Módosításokat, de az írási műveletek (aktív másodlagos példány hozható létre néven) több más replika replikálja a rendszer. Kell egy replika meghibásodik, a Service Fabric a meglévő replikák új replikát hoz létre.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Állapot nélküli és állapotalapú mikroszolgáltatások a Service Fabrichez
A Service Fabric segítségével mikroszolgáltatásokból vagy tárolókból álló alkalmazásokat építhet. Az állapot nélküli mikroszolgáltatások (például a protokollátjárók és webproxyk) nem tartanak fenn változtatható állapotot a kéréseken és a szolgáltatástól ezekre kapott válaszokon kívül. Az Azure Cloud Services feldolgozói szerepkörei például állapotmentes szolgáltatások. Az állapotalapú mikroszolgáltatások (például felhasználói fiókok, adatbázisok, eszközök, kosarak és üzenetsorok) változtatható, mérvadó állapotot tartanak fenn a kéréseken és a válaszokon kívül is. A modern webes alkalmazások állapot nélküli és állapotalapú mikroszolgáltatások kombinációjából állnak. 

A Service Fabric legfontosabb különbséget az állapotalapú szolgáltatások, vagy erős koncentrálhat az [beépített programozási modelleket ](service-fabric-choose-framework.md) vagy a tárolóalapú állapotalapú szolgáltatásokkal. Az [alkalmazás-forgatókönyvek](service-fabric-application-scenarios.md) az állapotalapú szolgáltatások használatát mutatják be.

Miért kell állapotalapú mikroszolgáltatások és állapot nélküli azokat? A két fő oka a következők:

* Nagy átviteli sebességű, alacsony késésű, hibatűrő online tranzakciófeldolgozási (OLTP) szolgáltatások tartja, kód és az adatok ugyanazon a gépen hozhat létre. Néhány példa olyan interaktív kirakattípus, keresési, eszközök internetes hálózata (IoT) rendszerek, kereskedelmi rendszerek, hitelkártya feldolgozás és a csalások észlelése rendszerek és személyes rekord felügyeleti.
* Alkalmazás-tervezés egyszerűsítheti. Állapot-nyilvántartó mikroszolgáltatások távolítsa el a további várólisták és a gyorsítótárak hagyományosan szükséges tisztán állapot nélküli alkalmazások rendelkezésre állás és a késés követelményeinek teljesítéséhez szükséges. Állapotalapú szolgáltatások természetükből fakadóan magas rendelkezésre állást és kis késésű, ami csökkenti a részek kezelheti az alkalmazás teljes számát.

## <a name="supported-programming-models"></a>Támogatott programozási modellek
A Service Fabric írni, és a szolgáltatások kezeléséhez több lehetőséget is kínál. Szolgáltatások a Service Fabric API-k a teljes mértékben kihasználhatja a platform szolgáltatásai és alkalmazás-keretrendszerek használatával. Szolgáltatások is lehet bármely lefordított végrehajtható programot bármilyen nyelven fejlesztett és üzemeltetett Service Fabric-fürtön. További információkért lásd: [támogatott programozási modellek](service-fabric-choose-framework.md).

### <a name="containers"></a>Containers
Alapértelmezés szerint a Service Fabric üzembe helyezi, és aktiválja a szolgáltatást folyamatokat is. A Service Fabric is üzembe helyezheti a szolgáltatások [tárolók](service-fabric-containers-overview.md). Kombinálhatja is fontosabb, folyamatokon belüli és tárolókon belüli ugyanazt az alkalmazást szolgáltatásokat. Service Fabric Linux-tárolók és a Windows-tárolók üzembe helyezése, támogatja a Windows Server 2016 rendszeren. Telepítheti a meglévő alkalmazásokat, állapotmentes szolgáltatások vagy állapotalapú szolgáltatások tárolókban. 

### <a name="reliable-services"></a>Reliable Services
[A Reliable Services](service-fabric-reliable-services-introduction.md) egy egyszerűsített keretrendszer, szolgáltatások, amelyek integrálása a Service Fabric platformot és a platform funkciók teljes készletét írása. A Reliable Services lehet állapot nélküli (hasonlóan a legtöbb szolgáltatás platformok, például webkiszolgálók vagy az Azure Cloud Services feldolgozói szerepkörök), ahol állapot rendszer megőrzi a külső megoldás, például az Azure DB vagy az Azure Table Storage. A Reliable Services is lehet állapotfüggő, ahol állapot maga a Reliable Collections használata a szolgáltatás közvetlenül a rendszer megőrzi. Állapot legyen [magas rendelkezésre állású](service-fabric-availability-services.md) replikációval és elosztott keresztül [particionálás](service-fabric-concepts-partitioning.md), minden felügyelt automatikusan, a Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
A Reliable Services-ra épülő a [Reliable Actor](service-fabric-reliable-actors-introduction.md) keretrendszer áll-e alkalmazás-keretrendszer, amely megvalósítja a virtuális Aktor mintában szereplő tervezési minta alapján. A Reliable Actors keretrendszerben független egység a számítási műveletek és az állapot nevű actors egyszálas végrehajtási használ. A Reliable Actor-keretrendszert biztosít az actors kommunikációt épül, és előre beállított állapot megőrzését és a horizontális felskálázást konfigurációk.

### <a name="aspnet-core"></a>ASP.NET-mag
Integrálható a Service Fabric [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) , webes és API-alkalmazások kialakításához első osztályú programozási modellt.  ASP.NET Core a Service Fabric két különböző módon használható:

- Az üzemeltetett és a Vendég végrehajtható fájlja. Ez elsősorban meglévő ASP.NET Core-alkalmazások futtatását a Service Fabric programkód módosítása nélkül.
- A Reliable Services futtatható. Ez lehetővé teszi, hogy a Service Fabric-futtatókörnyezet jobb integrációt, és lehetővé teszi, hogy a állapotalapú ASP.NET Core szolgáltatások.

### <a name="guest-executables"></a>Vendég végrehajtható fájlok
A [futtatható vendégalkalmazás](service-fabric-guest-executables-introduction.md) egy meglévő, tetszőleges végrehajtható fájl (bármilyen nyelven fejlesztett) üzemeltetett Service Fabric-fürt, más szolgáltatásokkal együtt. Futtatható vendégalkalmazás nem közvetlenül a Service Fabric API-kkal integrálhatja. Azonban azok továbbra is kihasználhatják a platformot kínál, például egyéni egészségügyi szolgáltatásokat és betölteni a jelentéskészítési és felderíthetőség szolgáltatás REST API-jainak meghívásával. Alkalmazás teljes életciklus-támogatása is rendelkeznek. 

## <a name="application-lifecycle"></a>Alkalmazás-életciklus
Mivel az eltérő platformokhoz való az alkalmazás a Service Fabric általában halad végig a következő fázisok: tervezési, fejlesztési, tesztelési, központi telepítés, frissítés, karbantartási és eltávolítását. A Service Fabric első osztályú támogatási szolgáltatásokat biztosít a felhőalapú alkalmazások, a fejlesztéstől az üzembe helyezés, a napi felügyeleti és a karbantartási végleges leszerelésének alkalmazás teljes életciklusa. A modell lehetővé teszi, hogy több különböző szerepkörök egymástól függetlenül részt vesznek az alkalmazásfejlesztési életciklust. [A Service Fabric-alkalmazás-életciklus](service-fabric-application-lifecycle.md) áttekintést ad az API-k és azok hogyan használhatók a eltérő szerepkörök fázisai a a Service Fabric-alkalmazás életciklusa során. 

A teljes alkalmazás-életciklus felügyelhetők [PowerShell-parancsmagok](/powershell/module/ServiceFabric/), [CLI-parancsok](service-fabric-sfctl.md), [C# API-kat](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [Java API-k](/java/api/overview/azure/servicefabric), és [ REST API-k](/rest/api/servicefabric/). Is beállíthat eszközök használatával, mint például a folyamatos integráció/folyamatos üzembe helyezési folyamatok [Azure folyamatok](service-fabric-set-up-continuous-integration.md) vagy [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

Az alábbi Microsoft Virtual Academy-videó ismerteti, hogyan lehet az alkalmazások életciklus-kezelése: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-applications-and-services"></a>Alkalmazások és szolgáltatások tesztelése
Valóban felhőléptékű-szolgáltatások létrehozását, rendkívül fontos, hogy az alkalmazások és szolgáltatások képes elviselni valós hibák ellenőrzéséhez. A Fault Analysis Service tesztelése a Service Fabric beépített szolgáltatás lett tervezve. Az a [Fault Analysis Service](service-fabric-testability-overview.md), jelentéssel bíró hibák idéz elő, és futtassa a teljes teszt forgatókönyvek az alkalmazások ellen. Ezek a hibák és a forgatókönyvek gyakorolja, és ellenőrizze a számos állapotok és átmenetek, amely egy szolgáltatás teljes élettartamuk, az összes olyan ellenőrzött, megbízható és konzisztens módon fog tapasztalni.

[Műveletek](service-fabric-testability-actions.md) célként egy szolgáltatás használatával az egyes hibák vizsgálatához. Egy szolgáltatás fejlesztői használhatja ezeket az adatokat építőelemeket összetett forgatókönyvek írni. Példák a szimulált hibák a következők:

* Indítsa újra a szimulálásához helyzetekben, ahol egy gépet vagy virtuális gép újraindítása bármennyi csomópontot.
* Helyezze át egy replikát, az állapotalapú szolgáltatás szimulálása a terheléselosztást, a feladatátvétel vagy az alkalmazás frissítését.
* Az állapotalapú szolgáltatások egy olyan helyzetet, ahol nem lehet folytatni az írási műveletek, mert nincs elég új adatokat fogadni "biztonsági mentése" vagy "másodlagos" replika létrehozása a kvórum elvesztése meghívása.
* Adatvesztés előidézése a létrehozása olyan helyzet, amelyben minden a memóriában teljesen törli az állapotalapú szolgáltatások.

[Forgatókönyvek](service-fabric-testability-scenarios.md) összetett műveletek alkotják egy vagy több műveletet. A Fault Analysis Service két beépített teljes forgatókönyv biztosítja:

* [A Chaos forgatókönyv](service-fabric-controlled-chaos.md)-folyamatos, kihagyásos hibák (szabályos és végbemenjen) a fürt teljes szimulálja a kiterjesztett időszakokra.
* [Feladatátvételi forgatókönyv](service-fabric-testability-scenarios.md#failover-test)-tesztkörnyezet, amely egy adott szolgáltatás partíció célozza úgy, hogy más szolgáltatások nem érinti a chaos egy verziója.

## <a name="clusters"></a>Fürtök
A [Service Fabric-fürt](service-fabric-deploy-anywhere.md) virtuális és fizikai gépek hálózaton keresztül csatlakozó készlete, amelyen mikroszolgáltatásokat helyezhet üzembe és felügyelhet. A fürtök több ezer gépre skálázhatók. Egy számítógép vagy virtuális Gépet, amely egy fürt része egy fürt csomópontja nevezzük. Minden csomóponthoz hozzá van rendelve egy csomópontnév (egy sztring). A csomópontok jellemzőkkel, például elhelyezési tulajdonságokkal rendelkeznek. Minden számítógép vagy virtuális gép rendelkezik egy automatikusan induló szolgáltatás `FabricHost.exe`, amelyek rendszerindítási követően elindul, és elindítja a két végrehajtható fájlok: Fabric.exe és FabricGateway.exe. E két végrehajtható fájlok a csomóponton alkotják. Tesztelési forgatókönyvek, több példányát futtatja egy egyetlen gépen vagy a virtuális gép több csomópont is üzemeltethet `Fabric.exe` és `FabricGateway.exe`.

Service Fabric-fürtök a Windows Server vagy Linux rendszerű virtuális vagy fizikai gépeken hozható létre. Tudja üzembe helyezéséhez és a Service Fabric-alkalmazások futtatása minden környezetben, összekapcsolt Windows Server vagy Linux rendszerű számítógépek esetében: a helyszínen, a Microsoft Azure-ban, vagy bármely más szolgáltatónál.

Az alábbi Microsoft Virtual Academy-videó ismerteti a Service Fabric-fürtök: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Fürtök az Azure-on
Azure-ban futó Service Fabric-fürtök integrációt biztosít az egyéb Azure-funkciók és szolgáltatások, ami lehetővé teszi a műveletek és a fürt felügyeletének egyszerűbb és megbízhatóbb. A fürt egy Azure Resource Manager-erőforrás, így a fürtök, mint bármely más az Azure-erőforrások modellezheti. A Resource Manager is biztosít könnyű kezelését egyetlen egységként a fürt által használt összes erőforrást. Az Azure-beli diagnosztikai és a Log Analytics integrált részei a fürtöket az Azure-ban. Fürt csomópont-típusok a következők [a virtual machine scale sets](/azure/virtual-machine-scale-sets/index), így az automatikus skálázási funkció jön létre.

Létrehozhat egy fürtöt az Azure-on keresztül a [az Azure portal](service-fabric-cluster-creation-via-portal.md), az egy [sablon](service-fabric-cluster-creation-via-arm.md), vagy [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

A Service Fabric Linux rendszeren lehetővé teszi, hogy elkészítheti, telepítheti és kezelheti a magas rendelkezésre állású, nagy mértékben skálázható Linux-alapú alkalmazásokat, ugyanúgy, mint a Windows. A Service Fabric-keretrendszereket (Reliable Services és Reliable Actors) mellett a C# (.NET Core) Linux rendszeren Java nyelven érhetők el. Is létrehozható [végrehajtható vendégszolgáltatások](service-fabric-guest-executables-introduction.md) és bármilyen nyelv és keretrendszer. Replikálásával segít a vállalatnak a Docker-tárolók használata is támogatott. Docker-tárolókat futtathat Vendég végrehajtható fájlok vagy a natív Service Fabric-szolgáltatások, amelyet a Service Fabric-keretrendszereket használ. További információkért olvassa el [Service Fabric Linux](service-fabric-deploy-anywhere.md).

Van néhány funkció, amely Windows, de linuxon nem támogatottak. További tudnivalókért olvassa el a [Service Fabric Linux rendszeren és Windows közötti különbségek](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Önálló fürtök
A Service Fabric egy telepítőcsomagot biztosít, hozhat létre önálló Service Fabric fürtök a helyszíni vagy bármely más szolgáltatónál. Önálló fürtök teszik lehetővé a fürt üzemeltetéséhez, bárhol szabadon. Ha az adatok megfelelőségi vagy jogszabályi megkötések vonatkoznak, vagy meg szeretné tartani az adatok helyi, a saját fürt és az alkalmazásokat is üzemeltethet. Service Fabric-alkalmazásokat futtathat több üzemeltetési környezetek módosítása nélkül, így az alkalmazások ismerete végrehajtott módosítások megjelennek üzemeltetési egyik környezetből a másikba. 

[Az első Service Fabric önálló fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)

Önálló fürtök Linux még nem támogatott.

### <a name="cluster-security"></a>Fürtbiztonság
Fürtök kell biztosítani, hogy megakadályozza a jogosulatlan felhasználók csatlakozzanak a fürthöz, különösen akkor, ha fut rajta éles üzemi van. Bár egy biztonságos fürt létrehozásához, ezzel tehát lehetővé teszi a névtelen felhasználók szeretne csatlakozni, vagy ha felügyeleti végpontok érhetők el a nyilvános internethez. Már nem ahhoz, hogy később egy biztonságos fürt biztonsági: Fürtbiztonság engedélyezve van a fürt létrehozásakor.

A fürt biztonsági forgatókönyvek a következők:
* Csomópontok közötti biztonsági
* Ügyfél-csomópont biztonsági
* Szerepköralapú hozzáférés-vezérlés (RBAC)

További információkért olvassa el [fürt biztonságossá tétele](service-fabric-cluster-security.md).

### <a name="scaling"></a>Méretezés
Ha új csomópontot ad hozzá a fürtöt, akkor a Service Fabric csomópontok megnövekedett számú rebalances a partíciók replikáit és példányok. A teljes javítja az alkalmazások teljesítményéről, és csökkenti a versengés memória való hozzáféréshez. Ha a fürt csomópontjainak hatékonyan nincsenek használatban, csökkentheti a fürtben található csomópontok számát. A Service Fabric rebalances a partíciók replikáit és példányainak újra a csomópontokat, hogy a hardver jobban kihasználja a csomópontokon csökkent számú. Fürtök az Azure-on vagy skálázhatja [manuálisan](service-fabric-cluster-scale-up-down.md) vagy [programozott módon](service-fabric-cluster-programmatic-scaling.md). Önálló fürtök méretezhetők [manuálisan](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Fürt frissítése
Rendszeres időközönként a Service Fabric-futtatókörnyezet új verzióinak kiadásakor. Hajtsa végre a futtatókörnyezet, vagy a háló, a frissítések a fürtön, hogy mindig futtatja egy [támogatott verziója](service-fabric-support.md). Hálófrissítések, mellett fürtkonfiguráció, például a tanúsítványokat és alkalmazásportok is frissítheti.

Service Fabric-fürt egy erőforrás, amelynek a tulajdonosa, de részben a Microsoft felügyeli. A Microsoft felelős az alapul szolgáló operációs rendszer javításait, és a fürtön futó fabric frissítéskezelésének végrehajtása. Állítsa be a fürt automatikus hálófrissítések, kapni a Microsoft által kiadott új verzióját, vagy kiválaszthat egy támogatott fabric-verzióra. Fabric és a konfiguráció frissítése az Azure Portalon keresztül vagy a Resource Manageren keresztül állítható. További információkért olvassa el [frissítése a Service Fabric-fürt](service-fabric-cluster-upgrade.md). 

Önálló fürt egy erőforrást, amikor teljesen saját. Ön felelős az alapul szolgáló operációs rendszer javításait, és hálófrissítések indítása. Ha a fürt csatlakozhat [ https://www.microsoft.com/download ](https://www.microsoft.com/download), beállíthatja, hogy a fürt automatikusan töltse le és kiépítése az új Service Fabric futtatókörnyezet-csomag. Meg szeretné majd indítsa el a frissítést. Ha nem érhető el a fürt [ https://www.microsoft.com/download ](https://www.microsoft.com/download), az új futtatókörnyezet-csomagot manuálisan letölthető egy internetkapcsolattal rendelkező gépen, és majd indítsa el a frissítést. További információkért olvassa el [önálló Service Fabric-fürt frissítése](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Állapotfigyelés
Bemutatja a Service Fabric egy [állapotmodell](service-fabric-health-introduction.md) jelző nem megfelelő állapotú fürt és az alkalmazás feltételeket az adott entitások (például a fürtcsomópontok és a szolgáltatás replikák) tervezték. Az állapotközpontú modellről egészségügyi jelentéskészítők (rendszer összetevőit és watchdogs) használja. A cél, egyszerű és gyors diagnosztizálása és javítás. Szolgáltatás írók kell health szolgáltatással kapcsolatos előzetes költségek, és hogyan [tervezési állapotfigyelő jelentési](service-fabric-report-health.md#design-health-reporting). Tetszőleges feltételt, amely hatással lehet a health kell jelenteni, különösen akkor, ha a legfelső szintű közeli jelző problémák megkönnyíti. Az egészségügyi információk időt takaríthat meg, és a Hibakeresés és a vizsgálat után a szolgáltatás munka működik-e nagy mennyiségű éles környezetben.

A Service Fabric-jelentéskészítők nevű figyelő azonosítja a lényeges feltételek. Ezek a helyi nézetén alapuló feltételek jelentést. A [health Store adatbázisban](service-fabric-health-introduction.md#health-store) összesíti az összes jelentéskészítők által küldött határozza meg, hogy globálisan kifogástalan állapotban-e entitásokat egészségügyi adatokat. A minta célja gazdag, rugalmas és könnyen használható. A rendszerállapot-jelentések minőségét meghatározza, hogy a fürt állapotának nézete pontosságát. Vakriasztások tévesen megjelenítése a nem megfelelő állapotú problémák is negatív hatással a frissítések vagy egészségügyi adatokat használó egyéb szolgáltatásokba. Ilyen szolgáltatás például a következők: javítása és a riasztási mechanizmusokat. Jelentések készítése, fontos a lehető legjobb módon feltételek rögzítő ezért néhány gondolat van szükség.

Jelentéskészítés teheti meg:
* A figyelt Service Fabric-szolgáltatás replikája vagy példányt.
* Belső watchdogs telepített Service Fabric-szolgáltatás (például egy Service Fabric állapotmentes szolgáltatás, amely figyeli a feltételek és jelentések problémák). A watchdogs is üzembe helyezhetők az összes csomóponton, vagy a figyelt szolgáltatás affinitized is lehet.
* Belső watchdogs, amely a Service Fabric-csomópontokon fut, de nem Service Fabric-szolgáltatások vannak implementálva.
* Az erőforrások (például-figyelési szolgáltatás például Gomez) Service Fabric-fürtön kívül mintavételi külső watchdogs.

Beépített a Service Fabric összetevői health jelentést a fürtben lévő összes entitáshoz. [Rendszerállapot-jelentések](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) fürt és az alkalmazások funkcióit és jelző problémák health segítségével betekintést biztosítanak. Alkalmazások és szolgáltatások rendszerállapot-jelentések győződjön meg arról, hogy az entitások vannak megvalósítva, és megfelelően a Service Fabric-futtatókörnyezet szempontjából vezérelhesse. A jelentések nem biztosít bármely szolgáltatásállapot-figyelést a szolgáltatás üzleti logika vagy lefagyott folyamatok észlelésére. Adott egészségügyi információk hozzáadása a szolgáltatás logikát, [megvalósítása egyéni állapotfigyelő jelentési](service-fabric-report-health.md) a szolgáltatásban.

A Service Fabric több módot is biztosít [rendszerállapot-jelentések megtekintéséhez](service-fabric-view-entities-aggregated-health.md) összevonva jelenik meg a health Store adatbázisban:
* [A Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) vagy más vizualizációs eszközökkel.
* Állapotlekérdezések (keresztül [PowerShell](/powershell/module/ServiceFabric/), [CLI](service-fabric-sfctl.md), a [C# FabricClient API-k](/dotnet/api/system.fabric.fabricclient.healthclient) és [Java FabricClient API-k](/java/api/system.fabric), vagy [REST API-k](/rest/api/servicefabric)).
* Általános lekérdezi, hogy visszatérési entitások listáját, amelyben állapota (a PowerShell, CLI, az API-k vagy REST) tulajdonságok egyike.

Az alábbi Microsoft Virtual Academy-videó ismerteti a Service Fabric állapotközpontú modellről, és azok felhasználási módjáról: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika
[A monitoring and diagnostics](service-fabric-diagnostics-overview.md) különösen fontos fejlesztése, tesztelése és üzembe helyezése az alkalmazások és szolgáltatások bármilyen környezetben. A Service Fabric-megoldások akkor működnek a legjobban, ha megfigyelésének és megtervezése és diagnosztikai, amelyek segítségével győződjön meg arról, alkalmazások és szolgáltatások a helyi fejlesztői környezetben vagy éles környezetben a várt módon működnek.

A fő célja a monitorozási és diagnosztikai a következők:

- Hardver- és infrastrukturális problémák észlelése és diagnosztizálása
- Szoftver és az alkalmazás a hibák észlelése, csökkentheti a szolgáltatási
- Erőforrás használat és a Súgó meghajtó üzemeltetési döntéseket tudunk hozni ismertetése
- Alkalmazás-, szolgáltatás és infrastruktúra teljesítményének optimalizálása
- Üzleti elemzések készítése és fejlesztési területek azonosítása
 
A teljes munkafolyamatba, monitorozási és diagnosztikai három lépésből áll:

1. Az eseménygenerálás: Ez események (naplókat, nyomkövetéseket, egyéni események) magában foglalja az infrastruktúrát (fürt), a platform és az alkalmazás / szolgáltatás szintjén
2. Események összesítése: létrehozott események összegyűjtésének és összesített előtt is megjeleníthetők kell
3. Elemzés: események kell lennie a teszi és néhány formátumban, elemzéshez engedélyezése ki és jelenítheti meg az igény szerint elérhető

Több termék is elérhető, amelyek vonatkoznak ezek a területek, és szabadon választhat a különböző technológiák minden. További információkért olvassa el [monitorozása és diagnosztikája az Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>További lépések
* Megismerkedhet egy [azure-beli fürt](service-fabric-cluster-creation-via-portal.md) vagy egy [önálló windowsbeli fürt](service-fabric-cluster-creation-for-windows-server.md) létrehozásával.
* Próbáljon szolgáltatást létrehozni a [Reliable Services](service-fabric-reliable-services-quick-start.md) vagy a [Reliable Actors](service-fabric-reliable-actors-get-started.md) programozási modellek használatával.
* Ismerje meg, hogyan [migrálása a Cloud servicesből](service-fabric-cloud-services-migration-differences.md).
* Ismerje meg, hogyan [szolgáltatások monitorozása és diagnosztizálása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Ismerje meg, hogyan [tesztelheti az alkalmazások és szolgáltatások](service-fabric-testability-overview.md).
* Ismerje meg, hogyan [kezelésére és vezénylésére fürterőforrások](service-fabric-cluster-resource-manager-introduction.md).
* Nézze át a [Service Fabric-minták](https://aka.ms/servicefabricsamples).
* Ismerje meg [Service Fabric támogatási lehetőségeinek](service-fabric-support.md).
* Olvassa el a [csapat blogján](https://blogs.msdn.microsoft.com/azureservicefabric/) cikkeket és közleményeket.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
