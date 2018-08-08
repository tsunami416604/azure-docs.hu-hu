---
title: Az Azure HDInsight-fürtök figyelése az Azure Log Analytics lekérdezési
description: Ismerje meg, hogyan lekérdezéseket futtathat az Azure Log Analytics egy HDInsight-fürtön futó feladatok figyelésére.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: jasonh
ms.openlocfilehash: 2d2de3c2e2b291ec1f5ad170350f19e9e0582eaa
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39602094"
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>A HDInsight-fürtök figyelése az Azure Log Analytics lekérdezési

Ismerje meg, néhány alapvető forgatókönyv az Azure HDInsight-fürtök figyelése az Azure Log Analytics használatával:

* [HDInsight-fürt metrikát elemezhet](#analyze-hdinsight-cluster-metrics)
* [Adott naplóüzenetek keresése](#search-for-specific-log-messages)
* [Hozzon létre miatti riasztás](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Előfeltételek

* Egy HDInsight-fürtön az Azure Log Analytics használatához konfigurált, és a HDInsight-fürtre jellemző Log Analytics felügyeleti megoldásai hozzáadni a munkaterülethez. Útmutatásért lásd: [használata az Azure Log Analytics és a HDInsight-fürtök együttes](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight-fürt metrikát elemezhet

Ismerje meg, hogyan keresse meg a HDInsight-fürthöz adott mérőszámok.

1. Nyissa meg az OMS-munkaterületet, amely kapcsolódik a HDInsight-fürtön az Azure Portalról.
2. Válassza ki a **naplóbeli keresés** csempére.
3. Írja be a következő lekérdezést a keresőmezőbe keresése az összes HDInsight-fürt úgy konfigurálva, hogy az Azure Log Analytics használatához, és válassza ki az összes rendelkezésre álló metrikák az összes metrikát **futtatása**.

        search *

    ![Keresés az összes metrikát](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "keresés az összes metrikát")

    A kimenet hasonlóan kell kinéznie:

    ![Keresés az összes metrikák kimeneti](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "kimenete metrikák keresése")

5. A bal oldali ablaktáblában alatt **típusa**, válassza ki a részletes megismerésére, és válassza ki a használni kívánt metrikát **alkalmaz**. Az alábbi képernyőfelvételen a `metrics_resourcemanager_queue_root_default_CL` típus van kiválasztva.

    > [!NOTE]
    > Előfordulhat, hogy ki kell választania a **[+] további** gombra kattintva keresse meg a keresett metrikát. Emellett a **alkalmaz** gombra a lista alján van, úgy kell görgessen lefelé való megtekintéshez.

    Figyelje meg, hogy a lekérdezés a szövegmezőben módosul, a kiemelt mezőbe az alábbi képernyőfelvételen is látható:

    ![Keresse meg az adott mérőszámok](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "adott mérőszámok keresése")

6. Hogy mélyebben Ez bizonyos metrikát. Például pontosíthatja a meglévő kimenet átlagos egy 10 perces időszakban, a fürt neve a következő lekérdezéssel kategorizálta felhasznált erőforrások alapján:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Finomítás átlagos felhasznált erőforrások alapján, helyett a következő lekérdezés használhatja alapján, ha a maximális erőforrásokat használt (valamint 90 és a 95. percentilis) a találatok szűkítése céljából egy 10 perces ablakban:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Adott naplóüzenetek keresése

Ismerje meg, hogyan keresse meg a hibaüzeneteket egy adott időszakban. A lépések Itt a csupán egy példa, hogyan lehet érkezik, a következő hibaüzenet jelenik meg az érdekelt is. Keresse meg a kívánt található hibák számára elérhető tulajdonságokat is használhatja.

1. Nyissa meg az OMS-munkaterületet, amely kapcsolódik a HDInsight-fürtön az Azure Portalról.
2. Válassza ki a **naplóbeli keresés** csempére.
3. Írja be a következő lekérdezés az összes HDInsight-fürt Azure Log Analytics használatára konfigurált összes hibaüzenetek kereséséhez, és válassza ki **futtatása**. 

         search "Error"

    A következő kimenet hasonló kimenetnek kell jelenik meg:

    ![Keressen hibákat az összes kimeneti](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "kimenete hibák keresése")

4. A bal oldali ablaktáblában alatt **típus** kategória, válassza ki egy, a mély be dig kívánt hiba típust, és válassza ki **alkalmaz**.  Figyelje meg, hogy az eredmények kifinomultabb lett csak a kiválasztott típusú a hiba megjelenítéséhez.
5. Akkor is mélyebben ezen konkrét hiba listája a bal oldali ablaktáblán rendelkezésre álló beállítások használatával. Példa:

    - Az adott munkavégző csomópont hibaüzenetek megjelenítése:

        ![Keresse meg a kimeneti hibaüzenetek](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "hibaüzenetek kimeneti keresése")

    - Hiba történt a egy bizonyos időpontban:

        ![Keresse meg a kimeneti hibaüzenetek](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "hibaüzenetek kimeneti keresése")

6. Megtekintheti az adott hiba. Választhat **[+] Továbbiak megjelenítése** a tényleges hibaüzenet jelenik meg.

    ![Keresse meg a kimeneti hibaüzenetek](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "hibaüzenetek kimeneti keresése")

## <a name="create-alerts-for-tracking-events"></a>Riasztások követési események létrehozása

Riasztás létrehozásához az első lépés, hogy a lekérdezés alapul, amely a riasztás akkor aktiválódik, érkeznek. Minden riasztás létrehozásához használni kívánt lekérdezés is használhatja.

1. Nyissa meg a Log Analytics-munkaterületet, amely kapcsolódik a HDInsight-fürtön az Azure Portalról.
2. Válassza ki a **naplóbeli keresés** csempére.
3. Futtassa a következő lekérdezést, amelyen létrehoz egy riasztást, és válassza ki a kívánt **futtatása**.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    A lekérdezés sikertelen, a HDInsight-fürtökön futó alkalmazások listájának biztosít.

4. Válassza ki **Új riasztási szabály** elemre a lap tetején.

    ![Riasztás létrehozásához adjon meg lekérdezési](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Enter lekérdezés riasztás létrehozása")

5. Az a **létrehozás szabály** ablakban adja meg a lekérdezés és egyéb részletek hozzon létre egy riasztást, és válassza a **riasztási szabály létrehozása**.

    ![Riasztás létrehozásához adjon meg lekérdezési](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Enter lekérdezés riasztás létrehozása")

Szerkesztéséhez, vagy egy meglévő riasztás törléséhez:

1. Az Azure Portalról nyissa meg a Log Analytics-munkaterületet.
2. A bal oldali menüben válassza ki a **riasztási**.
3. Válassza ki a kívánt riasztásra, szerkesztéséhez vagy törléséhez.
4. Az alábbi lehetőségek állnak rendelkezésére: **mentése**, **elveti**, **letiltása**, és **törlése**.

    ![HDInsight Log Analytics OMS – törlés szerkesztése](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

További információkért lásd: [használata a Log Analytics riasztási szabályai](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Lásd még

* [A Log Analytics használata](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [A Log Analytics riasztási szabályok létrehozása](../log-analytics/log-analytics-alerts-creating.md)
