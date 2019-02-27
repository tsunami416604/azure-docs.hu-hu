---
title: Naplózza a lekérdezést az Azure Monitor Azure HDInsight-fürtök figyelése
description: Ismerje meg, hogyan futtathat lekérdezéseket a HDInsight-fürtben futó feladatok figyelése az Azure Monitor-naplók.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: e1187867fc9da9a89f92d7b321c8703ee7a8a407
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889256"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Lekérdezés az Azure Monitor naplózza a HDInsight-fürtök figyelése

Ismerje meg, néhány alapvető forgatókönyv az Azure HDInsight-fürtök figyelése az Azure Monitor naplóira használatával:

* [HDInsight-fürt metrikát elemezhet](#analyze-hdinsight-cluster-metrics)
* [Adott naplóüzenetek keresése](#search-for-specific-log-messages)
* [Hozzon létre miatti riasztás](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

* Egy HDInsight-fürtön az Azure Monitor naplók konfigurált, és a HDInsight-fürtre jellemző az Azure Monitor naplók figyelési megoldások a munkaterülethez hozzáadott. Útmutatásért lásd: [használata az Azure Monitor naplózza a HDInsight-fürtökkel](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight-fürt metrikát elemezhet

Ismerje meg, hogyan keresse meg a HDInsight-fürthöz adott mérőszámok.

1. Nyissa meg a Log Analytics-munkaterületet, amely kapcsolódik a HDInsight-fürtön az Azure Portalról.
2. Válassza ki a **naplóbeli keresés** csempére.
3. Írja be a következő lekérdezést a keresőmezőbe keresése az összes HDInsight-fürtök úgy konfigurálva, hogy az Azure Monitor naplóira, és válassza az összes rendelkezésre álló metrikák minden metrika **futtatása**.

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

1. Nyissa meg a Log Analytics-munkaterületet, amely kapcsolódik a HDInsight-fürtön az Azure Portalról.
2. Válassza ki a **naplóbeli keresés** csempére.
3. Írja be a következő lekérdezés az összes HDInsight-fürt az Azure Monitor naplóira használatára konfigurált összes hibaüzenetek kereséséhez, és válassza ki **futtatása**. 

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
4. A következő lehetőségek közül választhat: **Mentés**, **elveti**, **letiltása**, és **törlése**.

    ![HDInsight az Azure Monitor-naplók Szerkesztés – törlés](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

További információkért lásd: [létrehozása, megtekintése és kezelése az Azure Monitor használatával metrikákhoz kapcsolódó riasztások](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Lásd még

* [OMS Log Analytics: Nézettervező](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Létrehozása, megtekintése és kezelése az Azure Monitor használatával metrikákhoz kapcsolódó riasztások](../azure-monitor/platform/alerts-metric.md)
