---
title: Az alkalmazásteljesítmény miatti riasztások létrehozása az Azure Monitor szolgáltatással tárolókhoz |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre egyéni Azure-riasztások a memória és CPU-felhasználást tárolók az Azure Monitor log-lekérdezések alapján.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: magoedte
ms.openlocfilehash: c275d50ab927895eca3aa018b71ab6989a4dde5a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238855"
---
# <a name="how-to-set-up-alerts-in-azure-monitor-for-container-performance-problems"></a>Tároló teljesítményproblémákat okozhat az Azure Monitor riasztásainak beállítása
A tárolók figyelők az Azure Monitor a tárolók számítási feladatainak teljesítményét vagy az Azure Container Instances szolgáltatásban üzembe helyezett, vagy az Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes-fürtök felügyelt. 

Ez a cikk ismerteti, hogyan lehet engedélyezni a riasztás, ha a processzor és memória kihasználtságáról, a fürt a csomópontok meghaladja a meghatározott küszöbértéket.

## <a name="create-alert-on-cluster-cpu-or-memory-utilization"></a>Riasztás létrehozása a fürt CPU és memória kihasználtsága
Riasztás, ha a CPU és memória kihasználtsági értéke magas fürthöz, hozzon létre egy metrikamérési riasztási szabályt, amely azon alapul, a megadott naplólekérdezések engedményt. A lekérdezések egy dátum és idő a jelenlegi történő összehasonlítása a `now` operátor és a egy órás biztonsági goes. Az összes-tárolókhoz az Azure Monitor által tárolt dátumok UTC formátumban.  

Mielőtt hozzákezdene, ha nem ismeri az Azure monitorban riasztásokat, lásd: [áttekintése a Microsoft Azure-ban riasztások](../platform/alerts-overview.md). Riasztások a log-lekérdezésekkel kapcsolatos további információkért lásd: [Naplóriasztások az Azure monitorban](../platform/alerts-unified-log.md)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **figyelő** a bal oldali ablaktáblán, az Azure Portalon. Alatt a **Insights** szakaszban jelölje be **tárolók**.    
3. Az a **figyelt fürtök** lapra, válassza ki a fürt a fürt nevére kattintva a listából.
4. A bal oldali ablaktábla a **figyelés** szakaszban jelölje be **naplók** nyissa meg az Azure Monitor naplózza a oldal, amely írható, és hajtsa végre az Azure Log Analytics-lekérdezéseket.
5. Az a **naplók** kattintson **+ Új riasztási szabály**.
6. Alatt a **feltétel** területén kattintson az előre definiált egyéni napló feltétel teljesülésekor **minden alkalommal, amikor az egyéni log Search <logic undefined>** . A **egyéni naplóbeli keresés** jeltípus automatikusan ki van jelölve a számunkra, mert a Microsoft által kezdeményezett, közvetlenül az Azure Monitor-naplók oldalán a riasztási szabály létrehozása.  
7. Illessze be az alábbi lekérdezések bármelyikét a **keresési lekérdezés** mező. A következő lekérdezés átlagos CPU-felhasználását számítja ki átlagban tag csomópontok CPU-kihasználtság percenként.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'cpuCapacityNanoCores';
    let usageCounterName = 'cpuUsageNanoCores';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

    A következő lekérdezés tag csomópontok memóriahasználat percenként átlagosan átlagos memória-felhasználását számítja ki.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'memoryCapacityBytes';
    let usageCounterName = 'memoryRssBytes';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

8. Konfigurálja a riasztást az alábbi információkkal:

    a. A **Riasztás alapja** legördülő menüből válassza a **Metrikamérés** elemet. A metrikamérés egy riasztást hoz létre a lekérdezés minden egyes objektumához, amelynek értéke meghaladja a megadott küszöbértéket.  
    b. Az a **feltétel**, jelölje be **nagyobb, mint** , és adja meg **75** egy kezdeti alaptervként **küszöbérték** vagy adjon meg egy értéket, amely megfelel a feltételek.  
    c. A **eseményindító riasztás alapja** szakaszban jelölje be **egymás utáni incidensek** a legördülő listából válassza **nagyobb, mint** adjon meg egy értéket a **2**.  
    d. A **alapján Evaluated** szakaszában, módosíthatja a **időszak** érték 60 perc. A szabály lesz 5 percenként, és az aktuális időpont az elmúlt órában belül létrehozott rekordot ad vissza. Ha az időtartamot szélesebb időközre állítja, figyelembe veheti az esetleges adatkésést, és biztosíthatja, hogy a lekérdezés adatokat ad vissza, így nem kap téves negatív választ, ha a figyelmeztetés soha nem aktiválódik. 

9. Kattintson a **Kész** gombra a riasztási szabály létrehozásának befejezéséhez.
10. Adjon meg egy nevet a riasztásból a **riasztásiszabály-névnek** mező. Adjon meg egy **leírás** részletező a riasztás-adatait, és válasszon egy megfelelő súlyossági lehetőségeket.
11. Ha a létrehozásakor azonnal aktiválni kívánja a riasztási szabályt, fogadja el a **Szabály engedélyezése a létrehozásakor** alapértelmezett értékét.
12. Az utolsó lépést, válasszon egy meglévő, vagy hozzon létre egy új **műveletcsoport**, amely biztosítja, hogy ugyanazokat a műveleteket kerül minden alkalommal, amikor egy riasztás akkor aktiválódik, és az egyes szabályokhoz határoz meg használható. Konfigurálása alapján hogyan lehet az informatikai RÉSZLEG kezeli az incidensek fejlesztési és üzemeltetési műveleteket. 
13. Kattintson a **Riasztási szabály létrehozás** gombra a riasztási szabály létrehozásának befejezéséhez. Azonnal el fog indulni.

## <a name="next-steps"></a>További lépések

* Tekintse át a [lekérdezés példák jelentkezzen](container-insights-analyze.md#search-logs-to-analyze-data) ismerje meg az előre definiált lekérdezések és a példákat, kiértékelheti és testre szabható használja az egyéb értesítési forgatókönyvek. 
* Megtudhatja, hogyan kell használni az Azure Monitor és egyéb monitorozhatja az AKS-fürt a folytatáshoz tekintse meg a [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md)
