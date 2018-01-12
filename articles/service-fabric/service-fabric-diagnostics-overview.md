---
title: "Azure Service Fabric figyelése és diagnosztika áttekintése |} Microsoft Docs"
description: "Tudnivalók a figyelés és az Azure Service Fabric fürt, alkalmazások és szolgáltatások diagnosztika."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: 43a45a31efffcd623e6381049876c3607663ec4f
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Megfigyelési és diagnosztikai az Azure Service Fabric

Ez a cikk áttekintést beállítása figyelése és a diagnosztika a Service Fabric-fürtök a futó alkalmazások. Megfigyelési és diagnosztikai létfontosságúak fejleszt, tesztelés vagy bármely felhőalapú környezetben munkaterhelések üzembe helyezése. Figyelési segítségével nyomon követheti az alkalmazások használatának módját, az erőforrás-használat és a fürt általános állapotát. Ezek az információk felderítésére, és javítsa ki a fürt esetleg felmerülő problémákat, és a jövőben bekövetkezését problémák megelőzése érdekében használható. 

A fő figyelése és diagnosztikai céljai számára:
* Észlelheti és diagnosztizálhatja az infrastruktúra-problémák
* Az alkalmazással kapcsolatos problémák észlelése
* Erőforrás-felhasználás ismertetése
* Alkalmazás, szolgáltatás és az infrastruktúra teljesítmény nyomon követése

A Service Fabric-fürt, a figyelés és diagnosztikai adatok elérhető lesz a három szint: alkalmazás, a platform (fürt) és az infrastruktúra. 
* A [alkalmazásszintű](service-fabric-diagnostics-event-generation-app.md) az alkalmazások és a további hozzáadott egyéni naplózási teljesítményére vonatkozó adatokat tartalmazza. Alkalmazás figyelési adatokat kell végül az Application Insights (AI) az alkalmazás saját magát. Az Eszközintelligencia SDK, EventFlow vagy egy másik folyamat az Ön által választott azt származhatnak.
* A [platformot szintű](service-fabric-diagnostics-event-generation-infra.md) műveletekből elérhetetlenné a fürtben, a fürt és a rajta futó alkalmazások kezelésével kapcsolatos eseményeket tartalmazza. Platform figyelési adatokat kell továbbítani OMS szolgáltatáshoz, a Service Fabric Analytics-megoldás a bejövő események megjelenítése érdekében. Ezeket az adatokat általában egy tárfiókba, a Windows vagy Linux rendszerű Azure Diagnostics bővítmény keresztül gépről küldött ahol hozzáfér az OMS szolgáltatáshoz. 
* Az infrastruktúra szint összpontosít [teljesítményfigyelés](service-fabric-diagnostics-event-generation-perf.md), akik szeretnének metrikáit és a teljesítményszámlálók erőforrás-használat és a betöltés meghatározásához. Figyelési ügynök használatával megvalósítható - javasoljuk a (Microsoft Monitoring) OMS-ügynököt, így a teljesítményadatok említi ugyanazon a helyen-eseményként rögzíti a platform, amely lehetővé teszi a diagnosztika érdekében, hogy összefüggéseket a módosítások a fürtön. 

![Diagnosztika overview diagram](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>Figyelési forgatókönyvek

Ez a szakasz ismerteti a kulcs eseteket ajánlott figyelni a Service Fabric-fürt - alkalmazás, a fürt, a teljesítmény és a állapotfigyelés. Az egyes forgatókönyvek esetében a leképezési és a teljes megközelítés figyelési tárgyalt. Ezeknek és más Azure-erőforrások általános figyelési javaslatok a további részletek találhatók [gyakorlati tanácsok - figyelés és diagnosztikai](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 

Ezek a forgatókönyvek van lazán is leképezve a figyelési és diagnosztikai adatok három szintje, kapcsolatban a fentiekben ismertetett, azaz az egyes forgatókönyvek esetén a fürt megfelelően kezelni kívánt, rendelkeznie kell a megfelelő szintű várható figyelése és diagnosztikai adatok . A forgatókönyv állapotfigyelési kivételt, azért, mert a terjed ki a fürtöt, és minden benne futó.

## <a name="application-monitoring"></a>Alkalmazásmonitoring
Az alkalmazásfigyelés követi nyomon, hogyan szolgáltatásait és összetevőit, hogy már létrehozta a buildet, az alkalmazások vannak használatban. Az alkalmazásait, és győződjön meg arról, hogy problémákat a felhasználók észlelt hatás figyelni kívánt. Az alkalmazások figyelési lehet hasznos:
* alkalmazásterhelés és a felhasználó forgalmi - van szüksége a szolgáltatásokat, hogy a felhasználó követelményeinek, vagy az alkalmazás potenciális szűk keresztmetszet címmel méretezési?
* szolgáltatások közötti kommunikáció és a távelérés a fürtszinten azonosító problémái
* hogy a felhasználók tevékenységeit az alkalmazással tudja – az alkalmazások tagolása segíthet az útmutató későbbi szolgáltatás fejlesztési és jobb diagnosztika érdekében alkalmazás hibáit

Az alkalmazás szintjén, a Service Fabric figyelés ajánlott, hogy Application Insights (AI). AI tartozó integrációját a Service Fabric tartalmazza a szükséges Visual Studio és az Azure-portál és a Service Fabric szolgáltatás helyi és távoli eljáráshívási a AI irányítópult és az alkalmazás-hozzárendelés, ami egy átfogó out-of-az-box naplózási élmények felhasználói élmény. Bár sok naplók automatikusan létrehozott és gyűjti az Ön AI használata esetén, javasoljuk, hogy további egyéni naplózási hozzáadása az alkalmazások, és rendelkezik láthatók a AI mellett milyen áll rendelkezésre részletesebb diagnosztikai környezetet problémák kezelése a jövőben. A Service Fabric AI használatával kapcsolatban további [esemény elemzése az Application insights szolgáltatással](service-fabric-diagnostics-event-analysis-appinsights.md). 

A kód az alkalmazások figyeléséhez tagolása megkezdéséhez, látogasson el [alkalmazás szintű esemény és a naplófájlok generációs](service-fabric-diagnostics-event-generation-app.md). 

### <a name="monitoring-application-availability"></a>Alkalmazás rendelkezésre állásának figyelése
Akkor lehet, hogy a fürt minden úgy tűnik, hogy a várt módon fut, és az jelentéskészítési állapota kiváló, de úgy tűnik, az alkalmazások lehet vagy nem érhető el. Bár nem sok esetben, ahol ez történne, fontos, ha ez történik, csökkentése érdekében a lehető leghamarabb tenni. Rendelkezésre állásának figyelésére szolgáló körben aggasztanak nyomon követése a rendszerösszetevők tudni, hogy a rendszer "üzemideje" általános rendelkezésre állását. A fürt azt összpontosítanak az alkalmazás szempontjából rendelkezésre állási - platform építési győződjön meg arról, hogy alkalmazás-életciklus felügyeleti lehetőségeket, nem váltják ki állásidő. Mindazonáltal a fürt különböző problémákat okozhat az alkalmazás hasznos üzemidő érintő, és ebben az esetben az alkalmazás tulajdonosa általában szeretné azonnal tudni. Azt javasoljuk, hogy együtt a a többi alkalmazást, központi telepítését a figyelő a fürtön. Ilyen figyelő céljának lenne, ellenőrizze a megfelelő végpontok az alkalmazáshoz beállított időközönként, és jelentést, hogy elérhetők legyenek. Is ehhez Pingeli a végpontot, és adja vissza a jelentést a megadott időközönként egy külső szolgáltatás segítségével.

## <a name="cluster-monitoring"></a>Fürt figyelése
A Service Fabric-fürt figyelési fontos a platformot, és a rajta futó összes feladatok futásának rendeltetésszerű biztosításában. A Service Fabric célok egyikét az adatok megőrzése a hardver meghibásodása keresztül futó alkalmazások. Ez a platform system szolgáltatások képességét infrastrukturális problémára, és gyorsan feladatátvételi munkaterhelések más csomópontok észlelése a fürt keresztül érhető el. De az adott esetben, mi történik, ha a rendszer szolgáltatásoknak problémákba? Vagy ha az megpróbálta áthelyezni a munkaterhelés, a szolgáltatások elhelyezésre szabályok nem sikeres? A fürt figyelési lehetővé teszi-tevékenységre figyelmeztető üzenetre a fürt, amely segít a problémák diagnosztizálása és elhárítása hatékonyan értesüljön. Néhány kulcsfontosságú dolgot keres szeretné a következők:
* A Service Fabric működése a megfelelően, helyezi el az alkalmazások és a fürt megkerülése terheléselosztás? 
* Műveletek a rendszer készít a fürt vonatkozik, és a, amelyre a várt módon konfigurációjának módosítása? Ez akkor különösen fontos, ha a fürt méretezése.
* A Service Fabric kezeli az adatokat és a szolgáltatások kommunikációs, a fürtben található megfelelően?

A Service Fabric események első használatkor a működési és az adat & Messaging csatornákon keresztül széles választékát nyújtja. A Windows, ezek olyan formájában egyetlen ETW-szolgáltató megfelelő számú `logLevelKeywordFilters` válasszon másik csatorna segítségével. Linux a platform események keresztül LTTng és üzembe egy olyan táblát, ahol azok szűrhetők igény szerint a. 

Ezeknek a csatornáknak válogatott, strukturált az eseményeket, amelyek segítségével jobb megértése érdekében a fürt állapotát tartalmazza. "Diagnosztika" alapértelmezés szerint engedélyezve van a létrehozáskor a fürt, amelyet meg, és az Azure Storage tábla ahol ezeknek a csatornáknak származó események kerülnek, hogy a jövőben lekérdezése. További, a fürt figyelésével kapcsolatos [Platform szintű esemény és a naplófájlok generációs](service-fabric-diagnostics-event-generation-infra.md). Azt javasoljuk, hogy OMS Naplóelemzési használatával figyelheti a fürthöz. OMS Naplóelemzési a Service Fabric Service Fabric elemzés, amely tartalmaz egy egyéni irányítópultot Service Fabric-fürtök megfigyelésére szolgáló, és lehetővé teszi a fürt események és riasztások beállítása bizonyos megoldást kínál. További információk a következő [esemény elemzésekről az OMS](service-fabric-diagnostics-event-analysis-oms.md). 

### <a name="watchdogs"></a>Watchdogs
Egy figyelő általában egy külön szolgáltatás, amely állapotfigyelő megtekinthet és a fürt számára különböző szolgáltatások, a ping végpontok és a jelentés állapotának betöltése. Ez segítséget nyújt a nézet egy egyetlen szolgáltatás alapján, amely nem észlelhető hiba. Watchdogs egyaránt remek állomás kódot, amely végrehajtja a javító műveleteket (például bizonyos időközönként törölje a naplófájlokat tároló) felhasználói beavatkozás nélkül. A minta-figyelő szolgáltatás megvalósításának található [Itt](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="performance-monitoring"></a>Teljesítményfigyelés
Az alapul szolgáló infrastruktúra figyelése része egy kulcs állapotát, a fürt és az erőforrás-használat ismertetése. Méri a rendszer teljesítményét több tényezőtől függ, amelyek általában mérik a fő teljesítménymutatók (KPI-k). A Service Fabric vonatkozó KPI-k rendelhetők metrikakészletet teljesítményszámlálók, a fürt a csomópontok gyűjteni kell.
Ezen KPI-k is segítséget nyújt:
* erőforrás-használat és a betöltés - méretezés a fürt, vagy a szolgáltatás folyamatok optimalizálása ismertetése
* infrastrukturális problémára - előrejelzésére számos problémájának előzi hirtelen változásai (elhagyta) teljesítmény érdekében, hogy használhassa a KPI-k például a hálózati i/o- és a CPU kihasználtsága előrejelzése és infrastrukturális problémák diagnosztizálása

Az infrastruktúra szintjén be kell teljesítményszámlálók listája található a [teljesítménymutatók](service-fabric-diagnostics-event-generation-perf.md). 

Az alkalmazás szintjén teljesítményének figyelésére, a Service Fabric biztosít teljesítményszámlálók a Reliable Services és a Actors programozási modellek esetén. Ha ezek a modellek valamelyikét használja, a teljesítményszámlálók biztosíthat KPI-k, amelyek segítenek, győződjön meg arról, hogy a szereplője van forgó felfelé és lefelé megfelelően, vagy az, hogy a megbízható szolgáltatáskérések kezelt elég gyorsan. Lásd: [a megbízható szolgáltatás távelérése figyelési](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) és [teljesítményfigyelés a Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters) ezek olvashat. Továbbá a Application Insights is rendelkezik egy teljesítménymutatók összegyűjti, ha az alkalmazás konfigurálva.

A megfelelő teljesítményszámlálók összegyűjtéséhez használja az OMS-ügynököt, és ezen KPI-k megtekintése az OMS szolgáltatáshoz. Használhatja az Azure diagnosztikai ügynök bővítmény (vagy bármely más hasonló ügynök) gyűjt, és a metrikák elemzés céljából. 

## <a name="health-monitoring"></a>Állapotfigyelés
A Service Fabric-platformról egy bővíthető állapotfigyelő reporting biztosít bejegyzései szerepelnek a fürt állapota állapotmodellt tartalmazza. Minden csomópont, alkalmazás, szolgáltatás, partíció, a replika vagy példányt, folyamatosan frissíthető állapotfigyelő állapotba került. Az állapot ellenőrzése "OK", "Figyelmeztetés" vagy "Error" lehet. Az állapotfigyelő állapota minden egyes entitásnál, a fürt problémák alapján kibocsátott állapotjelentések keresztül. Az entitások biztonsági állapotát is ellenőrizni kell a Service Fabric Explorer (SFX) bármikor alább látható módon, vagy a platformok rendszerállapot API-n keresztül kérdezhetők le. Is rendszerállapot-jelentések testreszabása és saját állapotjelentések hozzáadásával, vagy a rendszerállapot API-jával entitás állapotának módosítása. További részleteket az állapotmodell a helyen találhatók [Bevezetés a Service Fabric állapotfigyelésének](service-fabric-health-introduction.md).

![SFX irányítópult](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

SFX legújabb rendszerállapot-jelentések mellett, az egyes jelentések is rendelkezésre áll egy eseményként. Állapotával kapcsolatos események összegyűjthetők a működési csatornán keresztül (lásd: [az Azure Diagnostics esemény összesítési](service-fabric-diagnostics-event-aggregation-wad.md#collect-health-and-load-events)), és tárolja az OMS szolgáltatáshoz a riasztás és a jövőben lekérdezése. Ez segítséget nyújt az problémákat is hatással lehetnek az alkalmazás rendelkezésre állás érdekében, ezért azt javasoljuk, hogy beállította a riasztások megfelelő sikertelen forgatókönyvek esetén (OMS keresztül egyéni riasztások).

## <a name="monitoring-workflow"></a>Figyelési munkafolyamat 

Az általános munkafolyamat figyelési és diagnosztika három lépésből áll:

1. **Az eseménygenerálás**: Ez magában foglalja az események (naplókat, nyomkövetéseket, egyéni események), az infrastruktúra, a platform (fürt) és az alkalmazás szintjén
2. **Esemény összesítési**: Ebben a szakaszban hatékonyan az események hol is elemezheti őket, egy eszközt, amely tartalmazza az Azure Diagnostics bővítmény vagy EventFlow ebben a folyamatot
3. **Elemzés**: események szükséges hozzá egy eszközt, hogy elemzés - képi megjelenítés, lekérdezése, riasztások stb.

Több termék érhető el, amelyek vonatkoznak ezek a területek három, és válassza ki a különböző technológiák az egyes szabadon. Fontos, győződjön meg arról, hogy a különböző darabok működnek együtt a fürt egy végpont figyelési megoldást biztosítanak.

## <a name="event-generation"></a>Az eseménygenerálás

Az első lépés a figyelési, diagnosztikai munkafolyamat létrehozása és esemény-és napló generációja. Ezen események, a naplókat, és a teljesítményszámlálók kibocsátott összes három szinten: az alkalmazás szintjét (bármely instrumentation hozzáadódik az alkalmazások és szolgáltatások központi telepítése a fürt), a platform (a Service Fabric művelettől függően fürtből kibocsátott esemény), és az infrastruktúra szintjén (teljesítménymutatók minden csomóponton). Minden szinten gyűjtött diagnosztikai adatok testre szabható, bár a Service Fabric néhány alapértelmezett instrumentation engedélyezése. 

Tudjon meg többet az [platform szintű események](service-fabric-diagnostics-event-generation-infra.md) és [szintű alkalmazásesemények](service-fabric-diagnostics-event-generation-app.md) megtudhatja, hogy milyen áll rendelkezésre, és további instrumentation hozzáadása. Az itt, hogy a fő döntési állíthatnak be az alkalmazás módját. A .NET-alkalmazásokban ILogger használata javasolt, de is felfedezhet EventSource, Serilog és más hasonló szalagtárak. Java azt javasoljuk, Log4j. Ezek túl többféle módon más érhető el, amely az alkalmazás jellege alapján használható. Nyugodtan segítségével megismerheti, mi akkor ajánlott a konkrét használati eset, vagy válasszon egyet az Ön számára leginkább elfogadható használatával. 

Miután meghozná a döntést a naplózás szolgáltatót szeretne használni, győződjön meg arról, hogy a naplókat a rendszer éppen kell összesíteni, és megfelelően tárolja.

## <a name="event-aggregation"></a>Esemény aggregáció

A naplók és a rendszer az alkalmazások és a fürt által létrehozott események gyűjtéséhez általában javasoljuk a [Azure Diagnostics bővítmény](service-fabric-diagnostics-event-aggregation-wad.md) (ügynök-alapú naplógyűjtést több hasonló) vagy [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (a folyamat naplógyűjtést). Ha az Application Insights használ, és a .NET vagy Java fejlesztői, majd az Application Insights SDK helyett EventFlow használata javasolt.

Amíg az jelenhet meg egy hasonló végzett használatával csak az egyik, a legtöbb esetben a feladat egy folyamat gyűjtési folyamat (AI SDK vagy EventFlow) és az Azure Diagnostics bővítési ügynök együttes vezet megbízhatóbb, átfogó, figyelési munkafolyamat . Az Azure Diagnostics bővítési ügynök platform szintű események, az elérési út lesz, amíg AI SDK vagy EventFlow (a folyamat gyűjtemény) használ a szintű alkalmazásnaplók. 

Abban az esetben csak az egyik használni kívánt az alábbiakban néhány kulcsfontosságú pont szem előtt tartani.
* Az Azure Diagnostics kiterjesztés használatával alkalmazás naplók gyűjtésére esetén a Service Fabric szolgáltatások jó választás napló források és célok nem változik gyakran és egy egyszerű megfeleltetés a források és a célhelyek között. A ok az Azure Diagnostics konfigurálását a történik az erőforrás-kezelő rétegben, így az egész fürt frissíteni kell a konfiguráció jelentős módosítása. Ez azt jelenti, hogy azt gyakran említi alatt is SDK-val AI vagy EventFlow-nél kevésbé hatékonyak.
* EventFlow használata lehetővé teszi a elküldeni a naplókat, közvetlenül az elemzést, a képi megjelenítés platform, illetve tárolási szolgáltatásokat. Más függvénytárak (ILogger, Serilog stb.) az ugyanerre a célra is használhatók, de EventFlow rendelkezik az előnye, hogy tervezték, kifejezetten a folyamaton belüli naplógyűjtést és a Service Fabric-szolgáltatásokat támogatja. Általában könnyű és telepítési szempontjából a számos előnye van, és támogatja a különböző naplózási szalagtárak és elemzésére szolgáló eszközöket nagyobb rugalmasságot biztosít. 

## <a name="event-analysis"></a>Esemény elemzése

Nincsenek számos nagy platformra, amelyek a piacon szerepel, amikor az elemzést, a képi megjelenítés figyelése és diagnosztikai adatok. Javasolt a két rendszer [OMS](service-fabric-diagnostics-event-analysis-oms.md) és [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) miatt a Service Fabric integrációját. Is be kell keresnie a [rugalmas verem](https://www.elastic.co/products) (különösen akkor, ha tervezi, hogy a fürt egy kapcsolat nélküli környezetben futó), [Splunk](https://www.splunk.com/), vagy bármilyen más platformon igény szerint. 

A legfontosabb bármely platformhoz úgy dönt, hogyan kényelmes áll a felhasználói felület és a beállítások lekérdezése adato és könnyen olvasható irányítópultokat és javítása érdekében a figyelést biztosítanak további eszközöket kell tartalmaznia például az automatikus lehetőséget.

Úgy dönt, a platform mellett a Service Fabric-fürt Azure erőforrásként beállításakor meg is elérheti az Azure out-of-az-box teljesítményfigyelés a gépek számára.

### <a name="azure-monitor"></a>Azure Monitor

Használhat [Azure figyelő](../monitoring-and-diagnostics/monitoring-overview.md) számos, amelyen a Service Fabric-fürt épül Azure-erőforrások figyelése. A metrikák készlete a [virtuálisgép-méretezési csoport](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) és egyéni [virtuális gépek](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) automatikusan gyűjti, és az Azure-portálon jelenik meg. Az Azure-portálon az összegyűjtött információk megtekintéséhez válassza ki a Service Fabric-fürt tartalmazó erőforráscsoportot. Ezután válassza ki a megtekinteni kívánt virtuálisgép-méretezési csoport. Az a **figyelés** című szakaszban (a bal oldali navigációs), jelölje be **metrikák** megtekintheti az értékek.

![A metrika az összegyűjtött információk az Azure portál nézet](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

A diagram testreszabásához kövesse a [a Microsoft Azure-ban mérőszámok](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Hozhat létre a fenti metrikák alapján riasztások leírtak [hozzon létre riasztásokat Azure figyelése az Azure-szolgáltatások](../monitoring-and-diagnostics/monitoring-overview-alerts.md). 

## <a name="next-steps"></a>További lépések

* További információ a platform és a Service Fabric itt meg a következő események figyelése [Platform szintű esemény és a napló létrehozása](service-fabric-diagnostics-event-generation-infra.md)
* Bevezetés az alkalmazások tagolása használatába, lásd: [alkalmazás szintű esemény és a napló létrehozása](service-fabric-diagnostics-event-generation-app.md)
* Nyissa meg az oktatóanyag teljesítéséhez [a figyelő az ASP.NET Core alkalmazás a Service Fabric és diagnosztizálása](service-fabric-tutorial-monitoring-aspnet.md)

