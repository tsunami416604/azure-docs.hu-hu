---
title: Az Azure Service Fabric diagnosztizálása gyakori helyzetek |} Microsoft Docs
description: Gyakori forgatókönyvek az Azure Service Fabric hibaelhárítása
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
ms.openlocfilehash: bd7a7e0288ced0219a0600034b273d1acba6b09b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655241"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Diagnosztizálja a Service Fabric gyakori helyzetek

Ez a cikk felhasználók merült fel a figyelés és a Service Fabric diagnosztika terén gyakori forgatókönyvet mutat. A bemutatott esetekben fedik le a service fabric minden 3 rétegének: alkalmazás, a fürt és az infrastruktúra. Egyes megoldások használja az Application Insights és Naplóelemzési, Azure Hálózatfigyelő eszközök, minden egyes forgatókönyv végrehajtásához. A lépéseket az egyes megoldások nyújt a felhasználóknak bevezetést keretén belül a Service Fabric Application Insights és Naplóelemzési használatával.

## <a name="prerequisites-and-recommendations"></a>Előfeltételek és javaslatok

Ebben a cikkben szereplő megoldások a következő eszközöket fogja használni. Javasoljuk, hogy a ezen készletét, konfigurálni és naprakész:

* [A Service Fabric Application insights szolgáltatással](service-fabric-tutorial-monitoring-aspnet.md)
* [A fürt Azure Diagnostics engedélyezése](service-fabric-diagnostics-event-aggregation-wad.md)
* [Állítsa be az OMS Naplóelemzési munkaterület](service-fabric-diagnostics-oms-setup.md)
* [OMS-ügynököt teljesítményszámlálók követéséről](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Honnan látom kezeletlen kivételek az alkalmazásban?

1. Nyissa meg az Application Insights-erőforrást, amely az alkalmazás van konfigurálva.
2. Kattintson a *keresési* a bal felső sarokban. Kattintson a szűrő a következő panelen.

    ![AI áttekintése](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Nagy mennyiségű típusú eseményeket (nyomkövetések, kérelmek, egyéni események) jelenik meg. Válassza ki a "Kivétel" szűrőként.

    ![AI szűrőlista](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Kattintson egy kivételt a listában, vessen egy pillantást a Service Fabric Application Insights SDK használata, többek között a szolgáltatási környezet további részleteket.

    ![AI kivétel](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Hogyan tekinthetem meg HTTP-hívások szerepelnek a szolgáltatások?

1. Az azonos Application Insights-erőforrást, a kivételek helyett "kérelmek" szűrje és vonatkozó összes kérelmet megtekintése
2. A Service Fabric Application Insights SDK használ, ha látható a szolgáltatások kapcsolódik egy másik vizuális ábrázolását, és a sikeres és sikertelen kérelmek száma. A bal oldalon kattintson az "Alkalmazás-hozzárendelés"

    ![AI App térkép panel](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI App térkép](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Az alkalmazás-hozzárendelés további információkért látogasson el a [alkalmazás-hozzárendelés dokumentáció](../application-insights/app-insights-app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Hogyan hozható létre riasztást, ha egy csomópont leáll

1. A Service Fabric-fürt csomópont események követi. Keresse meg a Service Fabric Analytics nevű megoldás erőforrás **ServiceFabric(NameofResourceGroup)**
2. Kattintson a "Összefoglaló" című témakört a panel alján a diagramra

    ![OMS-megoldás](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Itt, hogy számos diagramokat és különböző metrikák megjelenítése mozaikok. Kattintson a diagramokon és időt vesz igénybe, a naplófájl-keresési. Itt lekérheti fürthöz kapcsolódó események és teljesítményszámlálók.
4. Adja meg a következő lekérdezést. Ezek eseményazonosítók találhatók a [csomópont események referencia](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. A lap tetején kattintson az "Új riasztást szabály", és most bármikor esemény érkezik alapján a lekérdezés, kap egy riasztás a kiválasztott módszernek.

    ![Új OMS-riasztás](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Hogyan tudom során a rendszer értesíti az alkalmazás frissítési visszagörgetése?

1. Az azonos naplófájl-keresési ablak, előtt adja meg a következő lekérdezés frissítési visszagörgetése. Ezek eseményazonosítók alatt találhatók [alkalmazás események referencia](service-fabric-diagnostics-event-generation-operational.md#application-events)

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
    ```

2. A lap tetején kattintson az "Új riasztást szabály", és most bármikor esemény érkezik alapján a lekérdezés, kapni fog egy riasztást.

## <a name="how-do-i-see-container-metrics"></a>Hogyan tekinthető meg a tároló metrikák?

A diagramokon az azonos nézetben jelenik meg a tároló teljesítményének egyes csempék. Az OMS-ügynököt kell és [tároló figyelésére szolgáló megoldás](service-fabric-diagnostics-oms-containers.md) ezeket a csempék adatokkal való feltöltéséhez.

![OMS tároló metrikák](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Az eszközre vonatkozó telemetriai adatokat a **belül** a tárolóban kell hozzáadni a [tárolók az Application Insights nuget-csomagja](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios).

## <a name="how-can-i-monitor-performance-counters"></a>Hogyan figyelhetők a teljesítményszámlálók?

1. Miután hozzáadta az OMS-ügynököt a fürt hozzá kell adnia az egyes teljesítményszámlálókra szeretné nyomon követni. A portál – az OMS-munkaterület oldalra léphet a munkaterület lap jelenik meg a bal oldali menüben a megoldás oldalról.

    ![OMS-munkaterület lap](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Ha a munkaterületet lapon tartózkodik, kattintson a "Speciális beállítások" az azonos bal oldali menüjében.

    ![Speciális beállítások OMS](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Kattintson az adatok > Windows-teljesítményszámlálók (adatok > Linux teljesítményszámlálók a Linux-gépek) keresztül az OMS-ügynököt a csomópontok specifikus számlálókat begyűjtése elindításához. Példa a hozzáadni kívánt számlálók formátuma

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`
    * `Service Fabric Service(*)\\Average milliseconds per request`

    A gyorsindító VotingData és VotingWeb is a folyamat nevét használja, így ezek a számlálók követési néz

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![OMS teljesítményszámlálói](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Ezzel engedélyezi, hogy tekintse meg az infrastruktúra hogyan kezeli a munkaterhelések, és erőforrás-használat alapján vonatkozó riasztások beállítását. Érdemes lehet például – riasztás beállításához, ha a teljes processzorhasználat hibernálva 90 % feletti vagy alatti 5 %. A számláló neve, ehhez használja az "kihasználtsága (%). Sikerült ezt úgy teheti meg a következő lekérdezés riasztási szabály létrehozása:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Hogyan nyomon követheti a Reliable Services és szereplője teljesítménye?

Az alkalmazások a Reliable Services vagy szereplője teljesítmény nyomon követése, adja hozzá, valamint a Service Fabric szereplő, a Aktormetódus, a szolgáltatás és a szolgáltatás metódus számlálókat. A fenti forgatókönyv szerint hasonló módon ezeket a számlálókat is hozzáadhat, példa a megbízható szolgáltatás és szereplő teljesítményszámlálók hozzáadása a OMS

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Ellenőrizze a megbízható a teljesítményszámlálók teljes listáját az alábbi hivatkozások [szolgáltatások](service-fabric-reliable-serviceremoting-diagnostics.md) és [Actors](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>További lépések

* [Állítson be riasztásokat a AI](../application-insights/app-insights-alerts.md) teljesítmény vagy a használati változásaira vonatkozó értesítést
* [Az Application Insights az észlelést intelligens](../application-insights/app-insights-proactive-diagnostics.md) hajt végre egy proaktív figyelmezteti a felhasználót, mert ez teljesítményproblémákat okozhat AI küldött telemetriai adatok elemzése
* További tudnivalók az OMS szolgáltatáshoz [riasztási](../log-analytics/log-analytics-alerts.md) észlelési és diagnosztika.
* A helyi fürthöz OMS szeretnék adatokat küldeni a OMS használható átjáró (http-továbbítás Proxy) kínál. További tájékoztatást talál, amely a [internetkapcsolattal nem rendelkező számítógépek kapcsolódásához az OMS-be az OMS-átjáró](../log-analytics/log-analytics-oms-gateway.md)
* Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési
* A Naplóelemzési, és mi kínál részletesebb áttekintését, olvassa el [Naplóelemzési újdonságai?](../operations-management-suite/operations-management-suite-overview.md)
