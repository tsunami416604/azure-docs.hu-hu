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
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: bb4ffe959fd3b973f55b08908ea603839222365d
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243262"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorozás és diagnosztika az Azure Service Fabrichez

Ez a cikk áttekintést monitorozást és diagnosztikát az Azure Service Fabric. A monitoring and diagnostics létfontosságúak fejlesztéséhez, teszteléséhez és bármilyen felhőalapú környezetben a számítási feladatok üzembe helyezése. Ha például nyomon követheti az alkalmazások használata, a Service Fabric platformot, az erőforrások kihasználtságát, a teljesítményszámlálók és az általános állapotát, a fürt által végrehajtott műveletek. Ezen információk használatával diagnosztizálása és a problémák megoldása és megelőzheti azok bekövetkezését a jövőben. A következő néhány szakaszban röviden ismertetik, érdemes figyelembe venni a termelési számítási feladatokhoz monitoring Service Fabric minden területéhez. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Alkalmazások monitorozása
Az alkalmazásfigyelés követi nyomon, milyen szolgáltatásokat és összetevőket az alkalmazás használ. Győződjön meg arról, hogy problémákat észlelt felhasználókat, hogy hatással az alkalmazások figyelni kívánt. Az alkalmazásfigyelés felelőssége a felhasználók az alkalmazások és a hozzá tartozó szolgáltatások fejlesztéséhez, mivel az egyedi az alkalmazás üzleti logikája számára be van kapcsolva. Az alkalmazások figyelése a következő esetekben hasznos lehet:
* Mekkora forgalom az alkalmazásom tapasztal? -Van szüksége a services felhasználói igényeknek és a potenciális szűk keresztmetszetté az alkalmazás címének megfelelő a méretezése?
* Azok a szolgáltatások közötti hívások, a sikeres és a nyomon követett?
* Milyen műveletek saját alkalmazást készít? -Jövőbeli funkcióinak fejlesztését, és jobb diagnosztika érdekében alkalmazáshibák telemetriai adatok gyűjtésének is útmutató
* Az alkalmazás nem kezelt kivételeket dob? 
* Mi történik a saját tárolókban futó szolgáltatások belül?

A nagy kapcsolatos az alkalmazásfigyelés előnye, hogy a fejlesztők a megszokott minden eszközt és keretrendszert szeretnének, mivel az alkalmazás összefüggésében diagramadattár! További információ az Azure-alkalmazások figyelésére és az Azure Monitor – az Application Insights megoldás [eseményelemzés az Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
Az oktatóanyag van [állítsa ezt a .NET-alkalmazások regisztrálásához](service-fabric-tutorial-monitoring-aspnet.md). Ez az oktatóanyag ismerteti, hogyan telepítse a megfelelő eszközökkel lehet például az application diagnostics és a telemetriai adatok megtekintése az Azure Portalon, és az alkalmazás egyéni telemetriát írhat, kerül. 


## <a name="platform-cluster-monitoring"></a>Platform (fürt) figyelése
A felhasználó az vezérlőben milyen telemetriai adatokat keresztül származik alkalmazását ír a kód magát, de mi a diagnosztikai információ a Service Fabric platformot, mivel? A Service Fabric célok egyike az alkalmazások rugalmas a hardveres hibák esetén. A cél a platform system szolgáltatások képességére infrastruktúra problémákat, és gyors feladatátvételi számítási feladatok más csomópontokhoz a fürtben keresztül érhető el. Azonban ebben az esetben, mi történik, ha a rendszer szolgáltatások problémákat? Vagy ha az próbál telepíteni, vagy helyezze át a számítási feladatok, a szolgáltatások elhelyezését szabályainak megsértette vannak? A Service Fabric biztosít ezen és annak biztosításához, hogy a fürt zajló tevékenység kapjanak az Ön további diagnosztika. Fürt figyelés néhány mintaként használható jelen forgatókönyvek a következők:

A Service Fabric beépített események széles választékát nyújtja. Ezek [Service Fabric-események](service-fabric-diagnostics-events.md) az EventStore vagy a műveleti csatorna (esemény-csatorna a platform által elérhetővé tett) keresztül érhető el. 

* A Service Fabric - csatornák és a Windows, a Service Fabric eseményeinek érhetők el megfelelő számú egyetlen ETW-szolgáltató `logLevelKeywordFilters` választja ki, műveleti és adatok & üzenetküldés csatornák - között használt ezzel a a módszerrel azt meg a kimenő külön Service Fabric-események az igény szerint szűrni. Linuxon Service Fabric-események eljussanak az lttng érhető el, és a egy Storage-táblához, ahol azok szűrhető igény szerint a kerüljenek. Ezek a csatornák válogatott, strukturált események, amelyek segítségével értelmezheti a fürt állapotát tartalmazza. Diagnosztikai alapértelmezés szerint engedélyezve vannak a fürt létrehozásakor, amely hozzon létre egy Azure Storage-táblába, ahol kiolvassa az eseményeket ezek a csatornák, hogy a jövőben lekérdezése érkeznek. 

* Az EventStore – az EventStore egy olyan funkció, a platform által biztosított Service Fabric platform eseményeket a Service Fabric Explorerben vagy a REST API-n keresztül érhető el. Láthatja, mi történik a fürtben az egyes entitásokhoz például csomópont, service, alkalmazás és az esemény időpontjában a lekérdezéshez egy pillanatképnézet. Emellett az EventStore található további információ a [az EventStore áttekintése](service-fabric-diagnostics-eventstore.md).    

![EventStore](media/service-fabric-diagnostics-overview/eventstore.png)

A megadott diagnosztikai események beépített átfogó készletét formájában vannak. Ezek [Service Fabric-események](service-fabric-diagnostics-events.md) bemutatják a különböző entitások, például a csomópontok, alkalmazások, szolgáltatások, a partíciók stb platform által végzett műveleteket. A fenti utolsó esetben, ha egy csomópont leáll, a platform lenne gridre bocsáthatja ki az olyan `NodeDown` esemény, és sikerült haladéktalanul értesíti a figyelési eszközben dolgozhat. Egyéb gyakori példák `ApplicationUpgradeRollbackStarted` vagy `PartitionReconfigured` feladatátvétel alatt. **Ugyanazokat az eseményeket a Windows- és Linux-fürtök érhetők el.**

Az események bármely felügyeleti eszköz, amely támogatja ezeket is olvashatják, Windows és Linux rendszereken egyaránt standard csatornákon keresztül továbbítja. Az Azure Monitor megoldás az Azure Monitor naplóira. Nyugodtan tudjon meg többet a [Azure Monitor-naplók integrációja](service-fabric-diagnostics-event-analysis-oms.md) mely tartalmazza a fürt és néhány mintalekérdezést, amely riasztásokat hozhat létre egyéni műveleti irányítópult. Több fürt megfigyelési fogalmak esetén érhető el [Platform szintű esemény és a naplófájl létrehozásának](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Állapotfigyelés
A Service Fabric platform állapotmodell, amely bővíthető állapotfigyelő jelentési biztosít egy fürtben entitások állapotát tartalmazza. Minden egyes csomópont, alkalmazás, szolgáltatás, partíció, replika vagy példány, folyamatosan frissíthető egészségügyi állapotba került. Az állapot "OK", "Figyelmeztetés" vagy "Error" lehet. Úgy gondolja, hogy a Service Fabric-események, mint minden entitásnál egy melléknév különböző entitások és egészségügyi a fürt által végzett műveleteket. Minden alkalommal, amikor egy adott entitás állapotát értékre vált, az esemény is lehet rendelkezésre. Így állíthat be a lekérdezések és a riasztások hálózatállapot-események tetszőleges, csakúgy, mint bármely más esemény a figyelési eszközben. 

Ezenkívül azt is segítségével a felhasználók felülbírálják az entitások állapotát. Ha az alkalmazás akkor frissítés alatt áll, és az ellenőrzések sikertelenek, írható-e a Service Fabric Health Health API használatával történő jelzik, hogy az alkalmazás már nem kifogástalan állapotú, és a Service Fabric automatikusan az visszaállítás a frissítés! További információk az állapotközpontú modellről, tekintse meg a [Service Fabric állapotmonitorozásának bemutatása](service-fabric-health-introduction.md)

![SFX szolgáltatásállapot-irányítópult](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Watchdogs
Általában egy figyelő egy külön szolgáltatás, amely állapotának megtekintése és szolgáltatások, a ping végpontok és a jelentés állapotának a fürtben lévő összes adat tölthető be. Ez megakadályozhatja a hibák észlelése nem történne a nézet egyetlen szolgáltatás alapján. Watchdogs egyúttal nagyszerű hely az állomás kódot, amely végrehajtja a javító műveleteket (például bizonyos időközönként törlése a naplófájlokat tároló) felhasználói beavatkozás nélkül. Egy minta-figyelő szolgáltatás megvalósítási annak [Itt](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>A figyelési infrastruktúra (teljesítmény)
Most, hogy az alkalmazás, valamint a platformot a diagnosztikai már szó, hogyan tudjuk, hogy a hardver elvárásoknak megfelelően működik? Az alapul szolgáló infrastruktúra figyelése a fürt és az erőforrások kihasználtságát állapotának megértése kulcsfontosságú része. Rendszer teljesítményének mérése attól függ, hogy számos tényezőtől függően a számítási feladatok szubjektív lehet. Ezek a tényezők teljesítményszámlálók keresztül általában mérése történik. A teljesítményszámlálók többféle forrásból, beleértve az operációs rendszer, a .NET-keretrendszer vagy a Service Fabric platformot maga származhatnak. Néhány forgatókönyvet, amelyben, akkor lehet hasznos, amelyek

* Vagyok I felhasználásával a hardver hatékonyan? Szeretné használni a hardver 90 %-ot vagy 10 %-ot. Ez akkor hasznos, ha érhető el a fürt méretezése, vagy az alkalmazás folyamatok optimalizálása.
* E előre meg tudjuk infrastrukturális problémák proaktív módon? -számos probléma előzi meg összekapcsolhatja a hirtelen változásokat (Elvetés) a teljesítmény, így használhatja teljesítményszámlálói például a hálózati i/o- és CPU-kihasználtság előrejelzése és a problémák proaktív diagnosztika.

Az infrastruktúra szintjén kell gyűjtött teljesítményszámlálók listája található [teljesítmény-mérőszámok](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric alsp biztosít teljesítményszámlálók a Reliable Services és Actors programozási modell. Ezek a modellek valamelyikét használja, ha ezeket a teljesítményszámlálókat is győződjön meg arról, hogy az aktorok vannak működtetésével felfelé és lefelé megfelelően, illetve, hogy a reliable Services-kérések kezelésének elég gyorsan adatokat. További információkért lásd: [Reliable Service szolgáltatás táveléréséhez figyelésének](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) és [alkalmazásteljesítmény-figyelés a Reliable actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

Az Azure Monitor megoldás ezek gyűjtéséhez az Azure Monitor naplóira hasonlóan platform szintű figyelése. Használjon a [Log Analytics-ügynököket](service-fabric-diagnostics-oms-agent.md) a megfelelő teljesítményszámlálót gyűjt, és ezeket az Azure Monitor naplókat is megtekintheti.

## <a name="recommended-setup"></a>Javasolt beállítás
Most, hogy egyes területeken példa és forgatókönyvek de, Íme az Azure monitorozási eszközökkel, és állítsa be az összes fenti terület figyeléséhez szükséges összegzését. 

* Az alkalmazásfigyelés [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Fürt figyelése a [Diagnosztikaiügynök](service-fabric-diagnostics-event-aggregation-wad.md) és [Azure Monitor-naplók](service-fabric-diagnostics-oms-setup.md)
* A figyelési infrastruktúra [Azure Monitor-naplók](service-fabric-diagnostics-oms-agent.md)

Is használhatja, és módosítsa a minta ARM-sablon található [Itt](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) segítségével automatizálja az összes szükséges erőforrást és az ügynökök telepítését. 

## <a name="other-logging-solutions"></a>Naplózás megoldásait

Bár a két megoldást javasoljuk, [naplózza az Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md) és [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) sok eseményt ETW-szolgáltatókon keresztül írták meg, és a Service Fabric-integráció készült bővíthető más naplózási megoldásokkal. Is be kell keresnie a [Elastic Stack](https://www.elastic.co/products) (különösen akkor, ha tervezi, hogy a fürt futtatása kapcsolat nélküli környezetben), [Dynatrace](https://www.dynatrace.com/), vagy bármely más platformra a beállításokat. Integrált partnereink által kínált érhető el az alábbi listában [Itt](service-fabric-diagnostics-partners.md).

A kulcsfontosságú pontokat bármely platformra választja ki kell terjednie hogyan jól ismeri a felhasználói felület, a lekérdezési képességeket, az egyéni Vizualizációk és irányítópult áll rendelkezésre, és további eszközöket biztosítanak a figyelési élmény javításához. 

## <a name="next-steps"></a>További lépések

* Ismerkedés az alkalmazások szándékkal, lásd: [alkalmazás szintű esemény és a naplófájl létrehozásának](service-fabric-diagnostics-event-generation-app.md).
* Lépkedjen végig a lépéseket az alkalmazás az Application Insights beállítása [figyelése és diagnosztizálása a Service Fabric ASP.NET Core alkalmazás](service-fabric-tutorial-monitoring-aspnet.md).
* A platform és a Service Fabric biztosít Önnek, események monitorozásával kapcsolatos további információkért [Platform szintű esemény és a naplófájl létrehozásának](service-fabric-diagnostics-event-generation-infra.md).
* Az Azure Monitor naplók-integráció konfigurálása a Service Fabric jelenleg [állítsa be a fürt az Azure Monitor naplóira](service-fabric-diagnostics-oms-setup.md)
* Ismerje meg, hogyan állítható be az Azure Monitor naplóira tárolók- [Monitorozás és diagnosztika a Windows a tárolók az Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Lásd például diagnosztikai problémák és megoldások a Service fabrickel [gyakori helyzetek diagnosztizálása](service-fabric-diagnostics-common-scenarios.md)
* Tekintse meg az egyéb diagnosztikai termékek, amelyek integrálhatók a Service Fabric [diagnosztikai Service Fabric-partnerek](service-fabric-diagnostics-partners.md)
* További tudnivalók az Azure-erőforrásokhoz – általános figyelési javaslatok [ajánlott eljárások - figyelés és diagnosztika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 