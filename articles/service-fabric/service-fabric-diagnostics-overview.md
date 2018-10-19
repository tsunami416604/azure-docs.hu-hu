---
title: Az Azure Service Fabric-figyelés és diagnosztika – áttekintés |} A Microsoft Docs
description: Ismerje meg a monitorozást és diagnosztikát az Azure Service Fabric-fürtökkel, alkalmazásokkal és szolgáltatásokkal.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: srrengar
ms.openlocfilehash: a607f560b5e74071f5ee15d03e615138f25a3aef
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406806"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorozás és diagnosztika az Azure Service Fabrichez

Ez a cikk áttekintést monitorozást és diagnosztikát az Azure Service Fabric. A monitoring and diagnostics létfontosságúak fejlesztéséhez, teszteléséhez és bármilyen felhőalapú környezetben a számítási feladatok üzembe helyezése. Figyelés segítségével nyomon követheti az alkalmazások használata, az erőforrások kihasználtságát és a fürt általános állapotát. Ezen információ segítségével diagnosztizálhatja, és kijavíthatja az esetleges problémákat, és megakadályozza, hogy a problémák előfordulását. 

## <a name="application-monitoring"></a>Alkalmazásfigyelés
Az alkalmazásfigyelés követi nyomon, milyen szolgáltatásokat és összetevőket az alkalmazás használ. Győződjön meg arról, hogy problémákat észlelt felhasználókat, hogy hatással az alkalmazások figyelni kívánt. Az alkalmazások figyelése a következő esetekben hasznos lehet:
* Alkalmazásterhelés és a felhasználói forgalmat – van szüksége a felhasználói igények figyelembevételével készült, és oldja meg az alkalmazás potenciális szűk keresztmetszetté services méretezése?
* A szolgáltatások közötti kommunikáció és a távelérés a problémák azonosításában a fürtön
* Foglalkozhatunk azzal, amit a felhasználók általi használatát az alkalmazás – az alkalmazások telemetriai adatok gyűjtésének segíthet az útmutató későbbi funkcióinak fejlesztését, és jobb diagnosztika érdekében az alkalmazás-hibák
* Mi történik a futó tárolók belül figyelése

Alakítsa ki az alkalmazás kódja a megfelelő nyomkövetések és telemetriai adatokat számos lehetőséget a Service Fabric támogatja. Azt javasoljuk, hogy használja-e Application Insights (AI). Mesterséges Intelligencia a Service Fabric-integráció tartalmaz eszközöket a Visual Studio és az Azure portal, valamint a Service Fabric adott mérőszámok, átfogó-a-beépített naplózási biztosítása érdekében. Bár sok naplók automatikusan létrehozza és mesterséges intelligenciával gyűjtött, azt javasoljuk, hogy további olyan egyéni naplózási részletesebb diagnosztikai környezetet biztosít az alkalmazások. A Service Fabric, az Application Insights használatának első lépéseivel kapcsolatos további [eseményelemzés az Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).

## <a name="platform-cluster-monitoring"></a>Platform (fürt) figyelése
A Service Fabric-fürt figyelése, kritikus fontosságú biztosításában, hogy a platform és az összes számítási feladatok futnak helyesen. A Service Fabric célok egyike az alkalmazások rugalmas a hardveres hibák esetén. A cél a platform system szolgáltatások képességére infrastruktúra problémákat, és gyors feladatátvételi számítási feladatok más csomópontokhoz a fürtben keresztül érhető el. Azonban ebben az esetben, mi történik, ha a rendszer szolgáltatások problémákat? Vagy ha az próbál áthelyezni a munkaterhelés, a szolgáltatások elhelyezését szabályainak megsértette vannak? A fürt figyelése lehetővé teszi a fürtön, amely segít a problémák diagnosztizálása és hatékonyan elhárításának zajló tevékenység információt szerezzenek. Keresse kívánt néhány főbb dolog van:
* Nem Service Fabric viselkedik a kívánt módon, elhelyezése az alkalmazások és a fürt áthidaló terheléselosztás? 
* Felhasználói műveletek megnyílik a fürtön arra vonatkozik, és a várt módon végrehajtott? Ez akkor különösen fontos, ha a fürt méretezése.
* A Service Fabric kezeli az adatokat, és a szolgáltatások közötti kommunikáció a fürtön belül megfelelően?

A Service Fabric beépített események széles választékát nyújtja. Ezek [Service Fabric-események](service-fabric-diagnostics-events.md) az EventStore API-k vagy a műveleti csatorna (esemény-csatorna a platform által elérhetővé tett) keresztül érhető el. 
* Az EventStore – az EventStore (elérhető Windows verziókban 6.2-es és újabb verziók, Linux még folyamatban van kezdődően ez a cikk utolsó frissítés dátuma), ezeket az eseményeket egy API-k (REST-végpontokat keresztül vagy az ügyféloldali kódtár keresztül elérhető) készlete-n keresztül tesz elérhetővé. További információ az EventStore, a [az EventStore áttekintése](service-fabric-diagnostics-eventstore.md).
* A Service Fabric - csatornák és a Windows, a Service Fabric eseményeinek érhetők el megfelelő számú egyetlen ETW-szolgáltató `logLevelKeywordFilters` választja ki, műveleti és adatok & üzenetküldés csatornák - között használt ezzel a a módszerrel azt meg a kimenő külön Service Fabric-események az igény szerint szűrni. Linuxon Service Fabric-események eljussanak az lttng érhető el, és a egy Storage-táblához, ahol azok szűrhető igény szerint a kerüljenek. Ezek a csatornák válogatott, strukturált események, amelyek segítségével értelmezheti a fürt állapotát tartalmazza. Diagnosztikai alapértelmezés szerint engedélyezve vannak a fürt létrehozásakor, amely hozzon létre egy Azure Storage-táblába, ahol kiolvassa az eseményeket ezek a csatornák, hogy a jövőben lekérdezése érkeznek. 

Azt javasoljuk, hogy az EventStore gyors elemzés céljából, valamint hogy a pillanatkép kiindulópont annak megértéséhez, hogy a fürt működik, és dolog történik Ha a várt. A naplók és a fürt által létrehozott események gyűjtéséhez általában használatát javasoljuk a [Azure Diagnostics bővítmény](service-fabric-diagnostics-event-aggregation-wad.md). Ez jól integrálható a Service Fabric-elemzés, a Log Analytics a Service Fabric konkrét megoldást, amely egy egyéni irányítópult biztosít a Service Fabric-fürtök figyelése, és lehetővé teszi a fürt és riasztásokat állíthat be. További információ a következő [esemény elemzése a Log Analytics](service-fabric-diagnostics-event-analysis-oms.md). 

 Tudjon meg többet a fürthöz figyelésével kapcsolatos [Platform szintű esemény és a naplófájl létrehozásának](service-fabric-diagnostics-event-generation-infra.md).

## <a name="performance-monitoring"></a>Teljesítményfigyelés
Az alapul szolgáló infrastruktúra figyelése a fürt és az erőforrások kihasználtságát állapotának megértése kulcsfontosságú része. Mérési rendszer teljesítménye számos tényezőtől függ, általában keresztül a fő teljesítménymutatók (KPI) mérik, amelyek mindegyike. A Service Fabric megfelelő KPI-ket is le lehet képezni a csomópontok a fürtben, a teljesítményszámlálók a gyűjtött metrikák.
Ezen KPI-k segítséget:
* Erőforrás-használat és a Betöltés – a fürt méretezése, vagy a szolgáltatás folyamatok optimalizálása céljából ismertetése.
* Infrastruktúrával kapcsolatos problémák – előrejelzésére sok hiba elhárításához előzi összekapcsolhatja a hirtelen változásokat (Elvetés) a teljesítmény, így előre jelezni, és infrastrukturális problémák diagnosztizálásához például a hálózati i/o- és CPU-kihasználtsága a KPI-k is használhatja.

Az infrastruktúra szintjén kell gyűjtött teljesítményszámlálók listája található [teljesítmény-mérőszámok](service-fabric-diagnostics-event-generation-perf.md). 

A Service Fabric biztosít a következő programozási modellekről: Reliable Services és Actors teljesítményszámlálók készletét. Ha ezek a modellek valamelyikét használja, a teljesítményszámlálók KPI-k, amelyek biztosítják, hogy az aktorok vannak működtetésével felfelé és lefelé megfelelően, illetve, hogy a reliable Services-kérések kezelésének elég gyorsan tud biztosítani. További információkért lásd: [Reliable Service szolgáltatás táveléréséhez figyelésének](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) és [alkalmazásteljesítmény-figyelés a Reliable actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Emellett az Application Insights is rendelkezik a teljesítmény-mérőszámokat gyűjti, ha az alkalmazás konfigurálva.

Használja a [Log Analytics-ügynököket](service-fabric-diagnostics-oms-agent.md) a megfelelő teljesítményszámlálót gyűjt, és ezen KPI-k megtekintése az Azure Log Analyticsben.

![Diagnosztika áttekintő diagram](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Állapotfigyelés
A Service Fabric platform állapotmodell, amely bővíthető állapotfigyelő jelentési biztosít egy fürtben entitások állapotát tartalmazza. Minden egyes csomópont, alkalmazás, szolgáltatás, partíció, replika vagy példány, folyamatosan frissíthető egészségügyi állapotba került. Az állapot "OK", "Figyelmeztetés" vagy "Error" lehet. Az állapotfigyelő állapota az egyes entitásokhoz, a fürt problémák alapján vannak kibocsátva rendszerállapot-jelentések használatával. Az entitások állapotát is ellenőrizni kell a Service Fabric Explorer (SFX) bármikor alább látható módon, vagy a platform állapotát API-n keresztül kérhetők le. Is testre szabhatja a rendszerállapot-jelentések, és a egy entitás állapotát módosíthatja a saját állapotjelentések hozzáadása vagy a rendszerállapot API-val. Az állapotközpontú modellről további részleteket tekinthet meg [Service Fabric állapotmonitorozásának bemutatása](service-fabric-health-introduction.md).

![SFX szolgáltatásállapot-irányítópult](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

SFX legújabb rendszerállapot-jelentések lát, mellett az egyes jelentések is rendelkezésre áll eseményként. A Szolgáltatásállapot-események összegyűjthetők az operatív csatornán keresztül (lásd: [események összesítése az Azure Diagnostics segítségével](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)), és tárolja a Log Analytics riasztási, és a jövőben lekérdezéséhez. Ennek segítségével észlelheti a problémákat, amelyek befolyásolhatják az alkalmazás rendelkezésre állását, ezért azt javasoljuk, állítsa be az értesítések megfelelő hibaesetet (egyéni riasztások a Log Analytics segítségével).

## <a name="other-logging-solutions"></a>Naplózás megoldásait

Bár a két megoldást javasoljuk, [Azure Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) és [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) sok eseményt etw-szolgáltatókon keresztül írták meg, és a Service Fabric-integráció készült bővíthető más naplózási megoldásokkal. Emellett be kell keresnie a [Elastic Stack](https://www.elastic.co/products) (különösen akkor, ha tervezi, hogy a fürt futtatása kapcsolat nélküli környezetben), [Splunk](https://www.splunk.com/), [Dynatrace](https://www.dynatrace.com/), vagy bármely más a megadott platform. 

A kulcsfontosságú pontokat bármely platformra választja tartalmaznia kell hogyan okoz gondot a van a felhasználói felület és lekérdezési lehetőségeket, lehetővé teszi az adatok megjelenítése és az irányítópultok könnyen olvasható, és azok javíthatják a figyelést, adja meg a további eszközök létrehozása például az automatikus riasztások.

## <a name="next-steps"></a>További lépések

* Ismerkedés az alkalmazások szándékkal, lásd: [alkalmazás szintű esemény és a naplófájl létrehozásának](service-fabric-diagnostics-event-generation-app.md).
* A platform és a Service Fabric biztosít Önnek, események monitorozásával kapcsolatos további információkért [Platform szintű esemény és a naplófájl létrehozásának](service-fabric-diagnostics-event-generation-infra.md).
* Lépkedjen végig a lépéseket az alkalmazás a mesterséges Intelligencia beállításához [figyelése és diagnosztizálása a Service Fabric ASP.NET Core alkalmazás](service-fabric-tutorial-monitoring-aspnet.md).
* Ismerje meg, hogyan állítható be az Azure Log Analytics tárolók- [Monitorozás és diagnosztika a Windows a tárolók az Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* További tudnivalók az Azure-erőforrásokhoz – általános figyelési javaslatok [ajánlott eljárások - figyelés és diagnosztika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
