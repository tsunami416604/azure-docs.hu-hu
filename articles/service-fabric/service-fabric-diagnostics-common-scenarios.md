---
title: Az Azure Service Fabric gyakori forgatókönyvek diagnosztizálása
description: Ismerje meg az Azure Service Fabric-alkalmazások gyakori figyelési és diagnosztikai forgatókönyveit.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3c7f027bad71d48db5fba002f778f23db8225fa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906948"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Gyakori forgatókönyvek diagnosztizálása a Service Fabric segítségével

Ez a cikk bemutatja a gyakori forgatókönyvek felhasználók találkozott a figyelés és a service fabric diagnosztika területén. A bemutatott forgatókönyvek a szolgáltatásháló mind a 3 rétegét lefedik: alkalmazás, fürt és infrastruktúra. Minden megoldás az Application Insights és az Azure Monitor naplók, Azure figyelési eszközök, az egyes forgatókönyvek teljesítéséhez. Az egyes megoldások lépései bemutatják a felhasználóknak, hogyan használhatják az Application Insights és az Azure Monitor naplókat a Service Fabric környezetében.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Előfeltételek és ajánlások

A jelen cikkben szereplő megoldások a következő eszközöket fogják használni. Javasoljuk, hogy állítsa be és konfigurálja ezeket:

* [Application Insights a Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [Az Azure Diagnosztika engedélyezése a fürtön](service-fabric-diagnostics-event-aggregation-wad.md)
* [Log Analytics-munkaterület beállítása](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics-ügynök a teljesítményszámlálók nyomon követéséhez](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Hogyan láthatom a nem kezelt kivételeket az alkalmazásomban?

1. Keresse meg az Application Insights-erőforrást, amelyhez az alkalmazás konfigurálva van.
2. Kattintson a *Keresés* gombra a bal felső sarokban. Ezután kattintson a szűrő a következő panelen.

    ![AI – áttekintés](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Látni fogja, sok típusú események (nyomkövetések, kérések, egyéni események). Szűrőként válassza a "Kivétel" lehetőséget.

    ![AI-szűrőlista](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Egy kivételre kattintva a listában további részleteket, beleértve a szolgáltatás környezetében, ha a Service Fabric Application Insights SDK használata.

    ![AI kivétel](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Hogyan tekinthetem meg, hogy mely HTTP-hívásokat használják a szolgáltatásaimban?

1. Ugyanebben az Application Insights-erőforrásban a kivételek helyett szűrheti a "kérelmeket", és megtekintheti az összes kérést
2. Ha a Service Fabric Application Insights SDK- t használja, láthatja az egymáshoz kapcsolódó szolgáltatások vizuális ábrázolását, valamint a sikeres és sikertelen kérelmek számát. A bal oldalon kattintson az "Alkalmazástérkép"

    ![AI App](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![Map Blade AI alkalmazás térkép](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Az alkalmazás térképére vonatkozó további információkért látogasson el az [Alkalmazástérkép dokumentációjára](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Hogyan hozhatok létre riasztást, ha egy csomópont leáll?

1. A csomóponteseményeket a Service Fabric-fürt követi nyomon. Keresse meg a Service Fabric Analytics szolgáltatásfabric-megoldásforrás nevű **ServiceFabric(NameofResourceGroup)**
2. Kattintson a grafikon alján a penge című "Összefoglaló"

    ![Az Azure Monitor naplóinak megoldása](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Itt számos grafikon és csempe jeleníti meg a különböző mutatókat. Kattintson az egyik grafikonok, és elviszi a Log Search. Itt lekérdezheti a fürteseményeket vagy teljesítményszámlálókat.
4. Írja be a következő lekérdezést. Ezek az eseményazonosítók a [Csomópontesemények hivatkozásában](service-fabric-diagnostics-event-generation-operational.md#application-events) találhatók.

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. Kattintson az "Új riasztási szabály" a tetején, és most bármikor egy esemény érkezik alapján ezt a lekérdezést, akkor kap egy riasztást a választott kommunikációs módszer.

    ![Az Azure Monitor naplózza az új riasztást](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Hogyan kaphatok értesítést az alkalmazásfrissítés visszaállításáról?

1. Ugyanabban a Naplókeresési ablakban, mint mielőtt beírna a következő lekérdezést a frissítés imázsának visszaállításához. Ezek az eseményazonosítók az [Alkalmazásesemények hivatkozása](service-fabric-diagnostics-event-generation-operational.md#application-events) alatt találhatók.

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Kattintson az "Új riasztási szabály" a tetején, és most bármikor egy esemény érkezik alapján ezt a lekérdezést, akkor kap egy riasztást.

## <a name="how-do-i-see-container-metrics"></a>Hogyan láthatom a tárolómetrikákat?

Ugyanebben a nézetben az összes grafikonok, látni fog néhány csempe a tárolók teljesítményét. A naplóelemzési ügynök és a [tárolófigyelési megoldás](service-fabric-diagnostics-oms-containers.md) feltöltéséhez szükség van.

![Log Analytics-tároló metrikái](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>A **tárolón belülről** származó telemetriai adatok at kell hozzáadnia az [Application Insights nuget csomagot a tárolókhoz.](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios)

## <a name="how-can-i-monitor-performance-counters"></a>Hogyan figyelhetem a teljesítményszámlálókat?

1. Miután hozzáadta a Log Analytics-ügynököt a fürthöz, hozzá kell adnia a követni kívánt teljesítményszámlálókat. Keresse meg a Log Analytics munkaterületének a portálon található lapját – a megoldás lapjáról a munkaterület lap a bal oldali menüben található.

    ![Log Analytics-munkaterület lap](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Miután a munkaterület oldalán, kattintson a "Speciális beállítások" ugyanebben a bal oldali menüben.

    ![Log Analytics – speciális beállítások](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Kattintson a Data > Windows teljesítményszámlálók (Data > Linux teljesítményszámlálók Linux gépekhez) a Log Analytics-ügynöksegítségével speciális számlálók gyűjtésének megkezdéséhez a csomópontokról. Íme néhány példa a számlálók hozzáadásának formátumára

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     A rövid útmutatóban a VotingData és a VotingWeb a használt folyamatnevek, így ezeknek a számlálóknak a nyomon követése

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Log Analytics perf számlálók](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Ez lehetővé teszi, hogy az infrastruktúra hogyan kezeli a számítási feladatokat, és az erőforrás-kihasználtság alapján releváns riasztásokat állíthat be. Például – érdemes lehet beállítani egy riasztást, ha a processzor teljes kihasználtsága meghaladja a 90% vagy 5% alatt. Az ehhez használt számlálónév a "% processzoridő". Ezt úgy teheti meg, hogy riasztási szabályt hoz létre a következő lekérdezéshez:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Hogyan követhetem nyomon a Megbízható szolgáltatások és színészek teljesítményét?

A megbízható szolgáltatások vagy az alkalmazások szereplői teljesítményének nyomon követéséhez a Service Fabric aktor, aktor metódus, szolgáltatás és a szolgáltatásmetódus számlálók is gyűjtse össze. Íme néhány példa a megbízható szolgáltatás- és aktor teljesítményszámlálók gyűjtésére

>[!NOTE]
>A Service Fabric teljesítményszámlálóit jelenleg nem gyűjtheti a Log Analytics-ügynök, de más diagnosztikai megoldások is gyűjthetik [őket.](service-fabric-diagnostics-partners.md)

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Ellenőrizze ezeket a hivatkozásokat a megbízható [szolgáltatások](service-fabric-reliable-serviceremoting-diagnostics.md) és szereplők teljesítményszámlálóinak teljes [listájáért](service-fabric-reliable-actors-diagnostics.md)

## <a name="next-steps"></a>További lépések

* [Gyakori kódcsomag-aktiválási hibák kivizsgálása](./service-fabric-diagnostics-code-package-errors.md)
* Riasztások beállítása a a [a kban,](../azure-monitor/app/alerts.md) hogy értesítést kapjon a teljesítmény vagy a használat változásairól
* [Az Application Insights intelligens észlelése](../azure-monitor/app/proactive-diagnostics.md) proaktív elemzést végez a a mi-nek küldött telemetriai adatokról, hogy figyelmeztesse Önt a lehetséges teljesítményproblémákra
* További információ az Azure [alerting](../log-analytics/log-analytics-alerts.md) Monitor naplóiról, amelyek az észlelés és a diagnosztika elősegítésére figyelmeztetnek.
* A helyszíni fürtök, az Azure Monitor naplók egy átjáró (HTTP továbbító proxy) kínál, amely adatok küldésére használható az Azure Monitor naplók. Erről a [Log Analytics-átjáró használatával internet-hozzáféréssel nem rendelkező számítógépek csatlakoztatása az Azure Monitor-naplókhoz](../azure-monitor/platform/gateway.md)
* Ismerkedjen meg az Azure Monitor naplóinak részeként kínált [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciókkal
* Az Azure Monitor-naplók részletesebb áttekintését és az általa ajánlott ajánlatokat a [Mi az Azure Monitor-naplók című témakörben talál?](../operations-management-suite/operations-management-suite-overview.md)
