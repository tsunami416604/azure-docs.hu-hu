---
title: Az Azure Service Fabric figyelésének és diagnosztikájának áttekintése
description: Ismerje meg az Azure Service Fabric-fürtök, alkalmazások és szolgáltatások figyelését és diagnosztikát.
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: ef77810adfab213845c7824740effc3416d85407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282483"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Figyelés és diagnosztika az Azure Service Fabric számára

Ez a cikk áttekintést nyújt az Azure Service Fabric figyeléséről és diagnosztikájáról. A figyelés és a diagnosztika kritikus fontosságú a számítási feladatok fejlesztése, tesztelése és üzembe helyezése szempontjából bármilyen felhőalapú környezetben. Például nyomon követheti, hogyan használják az alkalmazásokat, a Service Fabric platform által végrehajtott műveleteket, az erőforrás-kihasználtságot a teljesítményszámlálókkal és a fürt általános állapotát. Ezeket az információkat a problémák diagnosztizálására és javítására használhatja, és megakadályozhatja azok jövőbeni előfordulását. A következő néhány szakasz röviden ismerteti a Service Fabric figyelésének egyes területeit, amelyeket az éles számítási feladatokhoz figyelembe kell venni. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Alkalmazások monitorozása
Az alkalmazásfigyelés nyomon követi az alkalmazás funkcióinak és összetevőinek használatos módját. Szeretné figyelni az alkalmazásokat, hogy megbizonyosodjon arról, hogy a felhasználókat érintő problémák at. Az alkalmazásfigyelés felelőssége az alkalmazást fejlesztő felhasználókra és annak szolgáltatásaira hárul, mivel az az alkalmazás üzleti logikája szempontjából egyedülálló. Az alkalmazások figyelése a következő esetekben lehet hasznos:
* Mekkora forgalmat tapasztal az alkalmazásom? - Szükség van-e a szolgáltatások méretezésére, hogy megfeleljen a felhasználói igényeknek, vagy hogy kezelje az alkalmazás potenciális szűk keresztmetszetét?
* Sikeresek és nyomon követhetőek a szolgáltatásom a szolgáltatáshívásokhoz?
* Milyen műveleteket hajtanak végre az alkalmazás felhasználói? - A telemetria gyűjtése irányíthatja a jövőbeli funkciók fejlesztését és az alkalmazáshibák jobb diagnosztikáját
* Az alkalmazásom kezeletlen kivételeket eredményez? 
* Mi történik a tárolókban futó szolgáltatásokon belül?

A nagy dolog az alkalmazás figyelése, hogy a fejlesztők használhatják bármilyen eszközöket és keretet szeretnének, mivel él keretében az alkalmazás! Az Azure-elemzésekkel többet is megtudhat az azure-beli alkalmazásfigyelési megoldásról – Application Insights in [Event analysis .](service-fabric-diagnostics-event-analysis-appinsights.md)
Mi is van egy tutorial, hogyan kell [beállítani ezt a .NET alkalmazások](service-fabric-tutorial-monitoring-aspnet.md). Ez az oktatóanyag bemutatja, hogyan telepíti a megfelelő eszközöket, például egyéni telemetriai adatok at az alkalmazásban, és tekintse meg az alkalmazás diagnosztikáját és telemetriai adatait az Azure Portalon. 


## <a name="platform-cluster-monitoring"></a>Platform (Fürt) figyelése
A felhasználó szabályozhatja, hogy milyen telemetriai adatok származnak az alkalmazásból, mivel a felhasználó maga írja a kódot, de mi a helyzet a Service Fabric platform diagnosztika? A Service Fabric egyik célja, hogy az alkalmazások rugalmasak legyenek a hardverhibákkal szemben. Ez a cél a platform rendszerszolgáltatásainak az infrastruktúra-problémák észlelési képességével és a fürt más csomópontjainak gyors feladatátvételi munkaterhelésekkel való észlelésével érhető el. De ebben a konkrét esetben, mi van, ha a rendszer szolgáltatások maguk kérdések? Vagy ha egy számítási feladat üzembe helyezésének vagy áthelyezésének megkísérlése során megsértik a szolgáltatások elhelyezésére vonatkozó szabályokat? A Service Fabric diagnosztikát biztosít ezekhez, és győződjön meg arról, hogy a fürtben zajló tevékenységekről tájékoztatást kap. A fürtfigyelés néhány mintaforgatókönyve a következő:

A Service Fabric az események átfogó készletét biztosítja a dobozból. Ezek [a Service Fabric-események](service-fabric-diagnostics-events.md) az EventStore-on vagy az operatív csatornán (a platform által elérhetővé tett eseménycsatornán) keresztül érhetők el. 

* Service Fabric eseménycsatornák – A Windows, Service Fabric-események érhetők el `logLevelKeywordFilters` egyetlen ETW-szolgáltató egy sor releváns közötti választáshoz használt működési és adat-& üzenetküldési csatornák - ez az a mód, amelyben a kimenő Service Fabric-eseményeket kell szűrni. Linuxon a Service Fabric-események LTTng-en keresztül érkeznek, és egyetlen storage-táblába kerülnek, ahonnan szükség szerint szűrhetők. Ezek a csatornák válogatott, strukturált eseményeket tartalmaznak, amelyek a fürt állapotának jobb megértéséhez használhatók. A diagnosztika alapértelmezés szerint engedélyezve van a fürt létrehozási ideje, amely létrehoz egy Azure Storage-táblát, ahol az események et ezek a csatornák küldi lea lekérdezésre a jövőben. 

* EventStore – Az EventStore egy olyan szolgáltatás, amelyet a platform kínál, amely a Service Fabric platformesemények elérhető a Service Fabric Explorer és a REST API-n keresztül érhető el. Megtekintheti a pillanatkép nézet, hogy mi történik a fürtben az egyes entitások, például csomópont, szolgáltatás, alkalmazás és lekérdezés az esemény ideje alapján. Az EventStore áttekintése oldalon is olvashat bővebben az [EventStore áruházról.](service-fabric-diagnostics-eventstore.md)    

![EventStore (Rendezvény)](media/service-fabric-diagnostics-overview/eventstore.png)

A megadott diagnosztika az események átfogó készlete formájában történik. Ezek [a Service Fabric-események](service-fabric-diagnostics-events.md) bemutatják a platform által különböző entitásokon, például csomópontokon, alkalmazásokon, szolgáltatásokon, partíciókon stb. A fenti utolsó forgatókönyvben, ha egy csomópont leáll, a `NodeDown` platform eseményt bocsát ki, és a választott figyelési eszköz azonnal értesítést kaphat. További gyakori `ApplicationUpgradeRollbackStarted` példák közé tartozik, vagy `PartitionReconfigured` egy feladatátvétel során. **Ugyanazok az események érhetők el Windows és Linux-fürtökön is.**

Az események et normál csatornákon keresztül küldik mind a Windows, mind a Linux rendszeren, és bármely olyan felügyeleti eszköz eltudja olvasni őket, amely támogatja ezeket. Az Azure Monitor-megoldás az Azure Monitor-naplók. Nyugodtan tudjon többet az [Azure Monitor naplók integráció,](service-fabric-diagnostics-event-analysis-oms.md) amely magában foglalja az egyéni működési irányítópult a fürthöz, és néhány minta lekérdezések, amelyekből riasztásokat hozhat létre. További fürtfigyelési koncepciók érhetők el [platformszintű eseményen és naplógeneráláson.](service-fabric-diagnostics-event-generation-infra.md)

### <a name="health-monitoring"></a>Állapotfigyelés
A Service Fabric platform tartalmaz egy állapotmodellt, amely bővíthető állapotjelentést biztosít a fürtben lévő entitások állapotáról. Minden csomópont, alkalmazás, szolgáltatás, partíció, replika vagy példány folyamatosan javítható állapottal rendelkezik. Az állapot lehet "OK", "Figyelmeztetés" vagy "Hiba". A Service Fabric-eseményeket a fürt által különböző entitások és állapotok számára az egyes entitások melléknévként elvégzett műveletekként kell gondolni. Minden alkalommal, amikor egy adott entitás állapota átmenetek, egy esemény is kibocsátják. Így a figyelési eszközben ugyanúgy beállíthatja az állapotesemények lekérdezéseit és riasztásait, mint bármely más esemény. 

Emellett azt is lehetővé tesszük a felhasználók felülírják az entitások állapotát. Ha az alkalmazás megy keresztül egy frissítést, és érvényesítési tesztek sikertelen, írhat a Service Fabric Health az állapot api-t, jelezve, hogy az alkalmazás már nem kifogástalan, és a Service Fabric automatikusan visszaállítja a frissítést! Az állapotmodellről a Service [Fabric állapotfigyelési bevezetését](service-fabric-health-introduction.md)

![SFX egészségügyi irányítópult](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Házőrzők
Általában egy figyelő egy külön szolgáltatás, amely képes nézni az állapot és a terhelés a szolgáltatások között, ping végpontok, és a fürt ben lévő bármi állapotának jelentésére. Ez segíthet megelőzni azolyan hibákat, amelyek egyetlen szolgáltatás nézete alapján nem észlelhetők. Watchdogs is egy jó hely a fogadó kódot, amely végrehajtja a javítási műveletek et felhasználói beavatkozás nélkül (például a naplófájlok tisztítása a tárolóban bizonyos időközönként). Megtalálható a minta watchdog szolgáltatás végrehajtása [itt](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Infrastruktúra (teljesítmény) figyelése
Most, hogy lefedtük az alkalmazás és a platform diagnosztikát, honnan tudjuk, hogy a hardver a várt módon működik? Az alapul szolgáló infrastruktúra figyelése kulcsfontosságú része a fürt állapotának és az erőforrás-kihasználtság megértésének. A rendszer teljesítményének mérése számos tényezőtől függ, amelyek a számítási feladatoktól függően szubjektívek lehetnek. Ezeket a tényezőket általában teljesítményszámlálók on keresztül mérik. Ezek a teljesítményszámlálók különböző forrásokból származhatnak, beleértve az operációs rendszert, a .NET keretrendszert vagy magát a Service Fabric platformot. Néhány olyan forgatókönyv, amelyben hasznosak lennének,

* Hatékonyan kihasználom a hardvert? Szeretné használni a hardver90%- os CPU vagy 10% CPU. Ez jól jön a fürt méretezése, vagy az alkalmazás folyamatainak optimalizálása során.
* Megjósolhatom proaktív módon az infrastrukturális problémákat? - sok problémát megelőznek a hirtelen változások (cseppek) a teljesítmény, így a teljesítmény számlálók, mint a hálózati I / O és a CPU-használat előre jelzi és diagnosztizálja a problémákat proaktív módon.

Az infrastruktúra szintjén összegyűjtendő teljesítményszámlálók listája a [Teljesítmény mérőszámok](service-fabric-diagnostics-event-generation-perf.md)ban található. 

A Service Fabric teljesítményszámlálókat is biztosít a Megbízható szolgáltatások és az Actors programozási modellekhez. Ha ezek közül a modellek valamelyikét használja, ezek a teljesítményszámlálók információkat tartalmaznak annak érdekében, hogy a szereplők megfelelően forogjanak felfelé és lefelé, vagy hogy a megbízható szolgáltatási kérelmek et elég gyorsan kezelik. További információ: [Figyelés megbízható szolgáltatás átirányító](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) és [teljesítményfigyelés megbízható szereplők.](service-fabric-reliable-actors-diagnostics.md#performance-counters) 

Az Azure Monitor megoldás ezek összegyűjtése az Azure Monitor naplók, csakúgy, mint a platform szintű figyelés. A Log [Analytics-ügynök](service-fabric-diagnostics-oms-agent.md) segítségével gyűjtse össze a megfelelő teljesítményszámlálókat, és tekintse meg őket az Azure Monitor naplóiban.

## <a name="recommended-setup"></a>Ajánlott telepítés
Most, hogy már ment át az egyes területa figyelési és példaforgatókönyvek, itt van egy összefoglaló az Azure figyelési eszközök és beállítása szükséges az összes fenti területek figyeléséhez. 

* Alkalmazásfigyelés [az Application Insights](service-fabric-tutorial-monitoring-aspnet.md) segítségével
* Fürtfigyelés [a Diagnosztikai ügynök](service-fabric-diagnostics-event-aggregation-wad.md) és az Azure Monitor [naplóival](service-fabric-diagnostics-oms-setup.md)
* Infrastruktúra figyelése az [Azure Monitor naplóival](service-fabric-diagnostics-oms-agent.md)

Az [itt](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) található minta ARM-sablont is használhatja és módosíthatja az összes szükséges erőforrás és ügynök üzembe helyezésének automatizálásához. 

## <a name="other-logging-solutions"></a>Egyéb naplózási megoldások

Bár az általunk ajánlott két megoldás, az [Azure Monitor-naplók](service-fabric-diagnostics-event-analysis-oms.md) és [az Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) beépített integráció a Service Fabric, sok esemény kiírt atw szolgáltatók on keresztül, és bővíthető más naplózási megoldások. Azt is meg kell vizsgálnia a [rugalmas verem](https://www.elastic.co/products) (különösen akkor, ha azt fontolgatja, hogy fut egy fürt offline környezetben), [Dynatrace,](https://www.dynatrace.com/)vagy bármely más platformon a preferencia. Van egy lista az integrált partnerek [elérhető itt](service-fabric-diagnostics-partners.md).

A kiválasztott platformok legfontosabb pontjai között szerepelnie kell annak, hogy mennyire kényelmes a felhasználói felület, a lekérdezési képességek, a rendelkezésre álló egyéni vizualizációk és irányítópultok, valamint az általuk a figyelési élmény fokozása érdekében biztosított további eszközök. 

## <a name="next-steps"></a>További lépések

* Az alkalmazások eszközvitelének megkezdéséről az [Alkalmazásszintű esemény és a napló létrehozása .](service-fabric-diagnostics-event-generation-app.md)
* A lépéseket, hogy az Application Insights az alkalmazás hoz létre a [figyelő vel, és diagnosztizálni egy ASP.NET Core alkalmazás Service Fabric.](service-fabric-tutorial-monitoring-aspnet.md)
* További információ a platform figyeléséről és a Service Fabric által [platformszintű esemény- és naplógenerálási](service-fabric-diagnostics-event-generation-infra.md)eseményekről.
* Konfigurálja az Azure Monitor naplóinak integrációját a Service Fabric szolgáltatással az [Azure Monitor-naplók beállítása fürthöz](service-fabric-diagnostics-oms-setup.md)
* Megtudhatja, hogyan állíthatja be az Azure Monitor naplókat a figyelőtárolókfigyelési [tárolókhoz – Figyelés és diagnosztika a Windows-tárolókhoz az Azure Service Fabricben.](service-fabric-tutorial-monitoring-wincontainers.md)
* Tekintse meg a példa diagnosztikai problémákat és megoldásokat a Service Fabric-tel a [gyakori forgatókönyvek diagnosztizálása](service-fabric-diagnostics-common-scenarios.md)
* Tekintse meg a Service Fabric szolgáltatásfabricdiagnosztikai partnereinek egyéb diagnosztikai [termékeit](service-fabric-diagnostics-partners.md)
* Ismerje meg az Azure-erőforrásokkal kapcsolatos általános figyelési javaslatokat – [Gyakorlati tanácsok – Figyelés és diagnosztika.](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) 