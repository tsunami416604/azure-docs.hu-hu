---
title: Azure Service Fabric figyelése és diagnosztika áttekintése |} Microsoft Docs
description: Tudnivalók a figyelés és az Azure Service Fabric fürt, alkalmazások és szolgáltatások diagnosztika.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2018
ms.author: dekapur;srrengar
ms.openlocfilehash: b7bd5ce879e5119417a24879c875915245f9070b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Megfigyelési és diagnosztikai az Azure Service Fabric

Ez a cikk figyelése és diagnosztikai áttekintést nyújt az Azure Service Fabric. Megfigyelési és diagnosztikai létfontosságúak fejleszt, tesztelés vagy bármely felhőalapú környezetben munkaterhelések üzembe helyezése. Figyelési segítségével nyomon követheti az alkalmazások használatának módját, az erőforrás-használat és a fürt általános állapotát. Ezen információk használatával diagnosztizálása, és kijavíthatja az esetleges problémákat, és a jövőben bekövetkezését előforduló problémák megelőzéséhez. 

## <a name="application-monitoring"></a>Alkalmazásmonitoring
Az alkalmazásfigyelés nyomon követi, hogyan szolgáltatásait és összetevőit, az alkalmazás használják. Az alkalmazásait, és győződjön meg arról, hogy problémákat a felhasználók észlelt hatás figyelni kívánt. Az alkalmazások figyeléséről a következő esetekben hasznos lehet:
* alkalmazásterhelés és a felhasználó forgalmi - van szüksége a szolgáltatásokat, hogy a felhasználó követelményeinek, vagy az alkalmazás potenciális szűk keresztmetszet címmel méretezési?
* szolgáltatások közötti kommunikáció és a távelérés a fürtszinten azonosító problémái
* Tudja, a felhasználók tevékenységeit az alkalmazással – az alkalmazások telemetriai adatainak összegyűjtése segíthet az útmutató későbbi szolgáltatás fejlesztési és jobb diagnosztika érdekében alkalmazás hibáit
* Mi történik a futó tárolókba figyelése

A Service Fabric is beállíthatják az alkalmazás kódja a megfelelő nyomkövetéseket és telemetriai számos lehetőség támogatja. Azt javasoljuk, hogy használja-e Application Insights (AI). AI tartozó integrációját a Service Fabric tartalmaz tooling eszköz feladatait, a Visual Studio és az Azure portálon, és a Service Fabric adott mérőszámok, egy átfogó out-of-az-box naplózási élményt biztosít. Bár sok naplók automatikusan létrehozott és gyűjtik az Ön AI, javasoljuk, hogy adja hozzá további egyéni naplózási az alkalmazások részletesebb diagnosztikai környezetet. Az Application insights szolgáltatással a Service Fabric: használatának első lépéseivel kapcsolatos további [esemény elemzése az Application insights szolgáltatással](service-fabric-diagnostics-event-analysis-appinsights.md).

![AI nyomrészletek](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

## <a name="platform-cluster-monitoring"></a>Platform (fürt) figyelése
A Service Fabric-fürt figyelési fontos biztosításában, hogy a platform és minden munkaterhelést módon futnak készült. A Service Fabric célok egyik alkalmazások rugalmas tarthatja hardver meghibásodása. A cél a gazdafájlon keresztül, a platform system szolgáltatások képességét infrastrukturális problémára, és gyorsan feladatátvételi munkaterhelések más csomópontok észlelése a fürtben. De az adott esetben, mi történik, ha a rendszer szolgáltatásoknak problémákba? Vagy ha az megpróbálta áthelyezni a munkaterhelés, a szolgáltatások elhelyezésre szabályok nem sikeres? A fürt figyelési lehetővé teszi-tevékenységre figyelmeztető üzenetre a fürt, amely segít a problémák diagnosztizálása és elhárítása hatékonyan értesüljön. Néhány kulcsfontosságú dolgot keres szeretné a következők:
* A Service Fabric működése a megfelelően, helyezi el az alkalmazások és a fürt megkerülése terheléselosztás? 
* A fürt vonatkozik, és a várt módon végre készít felhasználói műveletek? Ez akkor különösen fontos, ha a fürt méretezése.
* A Service Fabric kezeli az adatokat és a szolgáltatások kommunikációs, a fürtben található megfelelően?

A Service Fabric események első használatkor a működési és az adat & Messaging csatornákon keresztül széles választékát nyújtja. A Windows, ezek olyan formájában egyetlen ETW-szolgáltató megfelelő számú `logLevelKeywordFilters` válasszon másik csatorna segítségével. Linux a platform események keresztül LTTng és üzembe egy olyan táblát, ahol azok szűrhetők igény szerint a. 

Ezeknek a csatornáknak válogatott, strukturált az eseményeket, amelyek segítségével jobb megértése érdekében a fürt állapotát tartalmazza. Diagnosztika alapértelmezés szerint engedélyezve vannak a fürt létrehozása során, ami hozzon létre egy Azure Storage táblát, ha ezeket a csatornákat származó események kerülnek ahhoz, hogy a jövőben lekérdezése. További, a fürt figyelésével kapcsolatos [Platform szintű esemény és a naplófájlok generációs](service-fabric-diagnostics-event-generation-infra.md).

A naplók és a rendszer a fürt által létrehozott események gyűjtéséhez általában javasoljuk a [Azure Diagnostics bővítmény](service-fabric-diagnostics-event-aggregation-wad.md). Ez jól integrálható a OMS napló Analytics Service Fabric bizonyos megoldást, Service Fabric elemzés, amely tartalmaz egy egyéni irányítópultot Service Fabric-fürtök megfigyelésére szolgáló, és lehetővé teszi a fürt események és értesítések beállítása. További információk a következő [esemény elemzésekről az OMS](service-fabric-diagnostics-event-analysis-oms.md). 

 ![OMS ú megoldás](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

## <a name="performance-monitoring"></a>Teljesítményfigyelés
Az alapul szolgáló infrastruktúra figyelése része egy kulcs állapotát, a fürt és az erőforrás-használat ismertetése. Méri a rendszer teljesítményét több tényezőtől függ, amelyek általában mérik a fő teljesítménymutatók (KPI-k). A Service Fabric vonatkozó KPI-k rendelhetők metrikakészletet teljesítményszámlálók, a fürt a csomópontok gyűjteni kell.
Ezen KPI-k is segítséget nyújt:
* Erőforrás-használat és a betöltés - méretezés a fürt, vagy a szolgáltatás folyamatok optimalizálása ismertetése.
* Infrastrukturális problémára - előrejelzésére számos problémájának előzi hirtelen változásai (elhagyta) teljesítmény érdekében, hogy használhassa a KPI-k például a hálózati i/o- és a CPU-kihasználtság előrejelzése és infrastrukturális problémák elemzéséhez.

Az infrastruktúra szintjén be kell teljesítményszámlálók listája található a [teljesítménymutatók](service-fabric-diagnostics-event-generation-perf.md). 

A Service Fabric teljesítményszámlálókat biztosít a Reliable Services és a Actors programozási modellek esetén. Ha ezek a modellek valamelyikét használja, a teljesítményszámlálók biztosíthat KPI-k, amelyek segítenek, győződjön meg arról, hogy a szereplője van forgó felfelé és lefelé megfelelően, vagy az, hogy a megbízható szolgáltatáskérések kezelt elég gyorsan. További információkért lásd: [a megbízható szolgáltatás távelérése figyelési](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) és [teljesítményfigyelés a Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Továbbá a Application Insights is rendelkezik egy teljesítménymutatók összegyűjti, ha az alkalmazás konfigurálva.

Használja a [OMS-ügynököt](service-fabric-diagnostics-oms-agent.md) a megfelelő teljesítményszámlálók adatainak összegyűjtése, és ezen KPI-k megtekintheti az OMS szolgáltatáshoz.

![Diagnosztika overview diagram](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Állapotfigyelés
A Service Fabric-platformról egy bővíthető állapotfigyelő reporting biztosít bejegyzései szerepelnek a fürt állapota állapotmodellt tartalmazza. Minden csomópont, alkalmazás, szolgáltatás, partíció, a replika vagy példányt, folyamatosan frissíthető állapotfigyelő állapotba került. Az állapot ellenőrzése "OK", "Figyelmeztetés" vagy "Error" lehet. Az állapotfigyelő állapota minden egyes entitásnál, a fürt problémák alapján kibocsátott állapotjelentések keresztül. Az entitások biztonsági állapotát is ellenőrizni kell a Service Fabric Explorer (SFX) bármikor alább látható módon, vagy a platformok rendszerállapot API-n keresztül kérdezhetők le. Is rendszerállapot-jelentések testreszabása és saját állapotjelentések hozzáadásával, vagy a rendszerállapot API-jával entitás állapotának módosítása. További részleteket az állapotmodell a helyen találhatók [Bevezetés a Service Fabric állapotfigyelésének](service-fabric-health-introduction.md).

![SFX irányítópult](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

SFX legújabb rendszerállapot-jelentések mellett, az egyes jelentések is rendelkezésre áll egy eseményként. Állapotával kapcsolatos események összegyűjthetők a működési csatornán keresztül (lásd: [az Azure Diagnostics esemény összesítési](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)), és tárolja az OMS szolgáltatáshoz a riasztás és a jövőben lekérdezése. Ez segítséget nyújt az problémákat is hatással lehetnek az alkalmazás rendelkezésre állás érdekében, ezért azt javasoljuk, hogy beállította a riasztások megfelelő sikertelen forgatókönyvek esetén (OMS keresztül egyéni riasztások).

## <a name="other-logging-solutions"></a>Egyéb naplózási megoldások

Bár a két megoldás ajánlott, [OMS](service-fabric-diagnostics-event-analysis-oms.md) és [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) létrehozása a Service Fabric integrációja, sok eseményt etw-szolgáltató használatával írt, és más naplózási megoldások. Is be kell keresnie a [rugalmas verem](https://www.elastic.co/products) (különösen akkor, ha tervezi, hogy a fürt egy kapcsolat nélküli környezetben futó), [Splunk](https://www.splunk.com/), [Dynatrace](https://www.dynatrace.com/), vagy bármely más a megadott platform. 

A legfontosabb bármely platformhoz úgy dönt, hogyan kényelmes áll a felhasználói felület és a beállítások lekérdezése adato és könnyen olvasható irányítópultokat és javítása érdekében a figyelést biztosítanak további eszközöket kell tartalmaznia például az automatikus lehetőséget.

## <a name="next-steps"></a>További lépések

* Bevezetés az alkalmazások tagolása használatába, lásd: [alkalmazás szintű esemény és a naplófájlok generációs](service-fabric-diagnostics-event-generation-app.md).
* További információ a platform és a Service Fabric itt meg a következő események figyelése [Platform szintű esemény és a naplófájlok generációs](service-fabric-diagnostics-event-generation-infra.md).
* Lépkedjen végig a lépéseket az alkalmazásba AI beállítása [figyelő és diagnosztizálhatja a Service Fabric ASP.NET Core alkalmazás](service-fabric-tutorial-monitoring-aspnet.md).
* Megtudhatja, hogyan tárolók figyelése OMS Naplóelemzési beállítása- [megfigyelési és diagnosztikai a Windows tárolókat, az Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* További tudnivalók az Azure-erőforrások - figyelési általános javaslatok [gyakorlati tanácsok - figyelés és diagnosztikai](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
