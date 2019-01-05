---
title: Az Azure Service Fabric diagnosztizálhatja a gyakori helyzetek |} A Microsoft Docs
description: Ismerje meg az Azure Service Fabric gyakori alkalmazási helyzetek hibaelhárításához
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
ms.date: 05/16/2018
ms.author: srrengar
ms.openlocfilehash: 700295c94428021445f6cbbd84175046d57b9147
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054945"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>A Service Fabric gyakori helyzetek diagnosztizálása

Ez a cikk azt mutatja be, monitorozása és diagnosztizálása a Service Fabric területén észlelt felhasználók gyakori forgatókönyveket. A bemutatott esetekben a service fabric minden 3 réteg terjed ki: Alkalmazás, a fürt és az infrastruktúra. Egyes megoldások használja az Application Insights és a Log Analytics, az Azure figyelési eszközök, minden egyes forgatókönyv végrehajtásához. A lépéseket az egyes megoldások biztosít a felhasználóknak Bevezetés az Application Insights és a Log Analytics használata a Service Fabric kontextusában.

## <a name="prerequisites-and-recommendations"></a>Előfeltételek és javaslatok

Ebben a cikkben szereplő megoldások fogja használni a következő eszközök. Javasoljuk, hogy ezek set felfelé és a beállított rendelkezik:

* [A Service Fabric Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* [Az Azure-diagnosztika engedélyezése a fürtön](service-fabric-diagnostics-event-aggregation-wad.md)
* [Log Analytics-munkaterület beállítása](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics-ügynök nyomon követheti a teljesítményszámlálók](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Hogyan tekinthetem meg nem kezelt kivételeket az alkalmazásom?

1. Keresse meg az alkalmazás konfigurálva van az Application Insights-erőforrást.
2. Kattintson a *keresési* bal felső sarokban található. Ezután kattintson a következő panelen a szűrőt.

    ![Mesterséges Intelligencia – áttekintés](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Látni fogja a sok különböző típusú eseményeket (nyomkövetéseket, kérelmek, egyéni események). Válassza ki a "Kivétel" szűrőként.

    ![AI-szűrő lista](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    A lista kivétel elemre kattintva megtekintheti további részletek, így a szolgáltatáskörnyezet, a Service Fabric Application Insights SDK használatakor.

    ![AI-kivétel](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Hogyan tekinthetem meg HTTP-hívások használatban vannak a szolgáltatások?

1. Az azonos Application Insights-erőforrás a "kérések" kivételek helyett szűrheti és intézett összes kérelem megtekintése
2. A Service Fabric Application Insights SDK használatakor az egymáshoz kapcsolódó szolgáltatások ábrázolása látható, és a sikeres és sikertelen kérések száma. A bal oldalon kattintson az "Alkalmazás-hozzárendelés"

    ![AI Alkalmazástérkép panel](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI Alkalmazástérkép](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Az alkalmazástérkép további információért látogasson el a [Alkalmazástérkép dokumentációja](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Hogyan hozhatok létre riasztást, amikor egy csomópont leáll

1. Csomópont-események nyomon követi a Service Fabric-fürt által. Keresse meg a Service Fabric-elemzés megoldás erőforrást nevű **ServiceFabric(NameofResourceGroup)**
2. Kattintson a "Összegzés" című panel alján a diagramon

    ![Log Analytics megoldás](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Van itt számos diagramok és csempék megjelenítése a különböző mérőszámokat. Kattintson az egyik a diagramok és időt vesz igénybe, a naplóbeli keresés. Itt lekérdezheti, ha bármely fürthöz kapcsolódó események és teljesítményszámlálók.
4. Adja meg a következő lekérdezést. Az alábbi eseményazonosítók találhatók a [csomópont események referencia](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. Kattintson az "Új riasztási szabály" tetején, és mostantól bármikor esemény érkezik alapján ez a lekérdezés, kapni fog egy riasztást a választott kommunikációs metódus az.

    ![Log Analytics új riasztás](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Hogyan lehet szeretnék kapni az alkalmazás frissítési visszagörgetése?

1. Az azonos Naplókeresés ablakot, mielőtt a adja meg a következő lekérdezés frissítési visszagörgetése. Alatt található alábbi eseményazonosítók [események alkalmazásreferencia](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
    ```

2. Kattintson az "Új riasztási szabály" tetején, és mostantól bármikor esemény érkezik alapján ez a lekérdezés, kapni fog egy riasztást.

## <a name="how-do-i-see-container-metrics"></a>Hogyan ellenőrizhetem tárolómetrikák?

Ugyanabban a nézetben az összes gráf látni fogja a teljesítmény, a tárolók néhány csempét. A Log Analytics-ügynököket kell és [Tárolómonitorozási megoldás](service-fabric-diagnostics-oms-containers.md) esetében ezek a csempék adatokkal való feltöltéséhez.

![Log Analytics Tárolómetrikák](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Az eszköz telemetriai **belül** a tárolóban, hozzá kell adnia a [Application Insights nuget-csomag tárolók](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Teljesítményszámlálók figyelése?

1. Miután hozzáadta a Log Analytics-ügynököket a fürt, hozzá kell nyomon követni kívánt teljesítményszámlálókat. Keresse meg a portal – a Log Analytics-munkaterület oldalán a munkaterület lap jelenik meg a bal oldali menüben a megoldás oldaláról.

    ![Log Analytics munkaterület lap](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Amint a munkaterület lap, kattintson a "Speciális beállítások" kifejezésre a azonos bal oldali menüben.

    ![A log Analytics speciális beállításai](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Kattintson az adatok > Windows-teljesítményszámlálók (Data > Linux rendszerű gépek Linux-teljesítményszámlálók) elindításához a Log Analytics-ügynökön keresztül a csomópontok specifikus számlálókat gyűjti össze. Példa a formátum számlálók hozzáadása

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`
    * `Service Fabric Service(*)\\Average milliseconds per request`

    A rövid útmutatóban VotingData és VotingWeb a folyamat nevét használja, így ezek a számlálók követési láthatóhoz hasonló

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![Log Analytics gyűjthető Teljesítményszámlálókra](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Ezzel lehetővé teszi az infrastruktúra hogyan kezeli a számítási feladatokat, és erőforrás-használat alapján vonatkozó riasztásokat állíthat be. Például – érdemes riasztást állít be, ha a teljes processzorhasználat 5 % alatti vagy feletti 90 %-os megfelelően. Ehhez használja a számláló neve "%-ban a processzoron." Sikerült ezt úgy teheti meg az alábbi lekérdezés a riasztási szabály létrehozása:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Hogyan nyomon követheti a Reliable Services és az Actors teljesítményét?

Az alkalmazások a Reliable Services vagy az Actors teljesítmény nyomon követése, hozzá kell adnia, valamint a Service Fabric-Aktor, Aktormetódus, szolgáltatás vagy szolgáltatás metódus számlálókat. A fenti forgatókönyv szerint hasonló módon ezek a számlálók is hozzáadhat, példa a megbízható szolgáltatás és az aktor teljesítményszámlálók hozzáadása a Log Analytics:

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Ellenőrizze a Reliable-teljesítményszámlálók teljes listáját az alábbi hivatkozásokra [szolgáltatások](service-fabric-reliable-serviceremoting-diagnostics.md) és [Actors](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>További lépések

* [A mesterséges Intelligencia riasztásokat állíthat be](../azure-monitor/app/alerts.md) szeretne értesítést kapni a teljesítmény vagy a használati változásai
* [Intelligens detektálás az Application Insights](../azure-monitor/app/proactive-diagnostics.md) hajtja végre a proaktív elemzésre a telemetriát küld a mesterséges Intelligencia figyelmezteti a felhasználót, mert ez teljesítményproblémákat okozhat
* További információ a Log Analytics [riasztási](../log-analytics/log-analytics-alerts.md) , ezzel elősegítve az észlelési és a diagnosztikát.
* A helyszíni fürtök esetén a Log Analytics-átjárót nyújt (http-továbbítás Proxy), amelyek segítségével adatokat küldeni a Log Analytics. Tudjon meg többet arról, hogy a [internetelérés nélküli számítógépek csatlakoztatása a Log Analytics használata a Log Analytics-átjáró](../azure-monitor/platform/gateway.md)
* Ismerkedjen meg a [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciók a Log Analytics részeként érhető el
* A Log Analytics és mit kínál részletes áttekintést, olvassa el [Mi az a Log Analytics?](../operations-management-suite/operations-management-suite-overview.md)
