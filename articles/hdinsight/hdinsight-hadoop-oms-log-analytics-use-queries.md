---
title: Az Azure Monitor naplóinak lekérdezése az Azure HDInsight-fürtök figyeléséhez
description: Megtudhatja, hogyan futtathat lekérdezéseket az Azure Monitor naplóiban a HDInsight-fürtön futó feladatok figyeléséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: 65e85548420116bdfcab87fe9f81a20e66226beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74803809"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Az Azure Monitor naplóinak lekérdezése a HDInsight-fürtök figyeléséhez

Ismerje meg az Azure HDInsight-fürtök figyeléséhez használt Azure Monitor-naplók néhány alapvető forgatókönyvét:

* [HDInsight-fürtmetrikák elemzése](#analyze-hdinsight-cluster-metrics)
* [Eseményriasztások létrehozása](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

Konfigurálnia kell egy HDInsight-fürtöt az Azure Monitor-naplók használatához, és hozzá kell adnia a HDInsight-fürtspecifikus Azure Monitor-naplók figyelési megoldásait a munkaterülethez. További információt az [Azure Monitor naplók használata HDInsight-fürtökkel .](hdinsight-hadoop-oms-log-analytics-tutorial.md)

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight-fürtmetrikák elemzése

Ismerje meg, hogyan kereshet konkrét metrikákat a HDInsight-fürthöz.

1. Nyissa meg a Log Analytics munkaterületet, amely a HDInsight-fürthöz van társítva az Azure Portalról.
1. **Az Általános csoportban**válassza **a Naplók**lehetőséget.
1. Írja be a következő lekérdezést a keresőmezőbe az Azure Monitor-naplók használatára konfigurált összes HDInsight-fürt összes elérhető metrika összes metrikája kereséséhez, majd válassza a **Futtatás lehetőséget.** Itt tekintheti meg a művelet eredményét.

    ```kusto
    search *
    ```

    ![Az Apache Ambari elemzések az összes mutatót keresik](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Keresés az összes mutatóban")

1. A bal oldali menüben válassza a **Szűrő** lapot.

1. A **Típus csoportban**válassza a **Szívverés**lehetőséget. Ezután válassza **& Futtatás alkalmazása**lehetőséget.

    ![log analytics keresés konkrét mérőszámok](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Adott mérőszámok keresése")

1. Figyelje meg, hogy a szövegmezőben lévő lekérdezés a következőre változik:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. A bal oldali menüben elérhető lehetőségek segítségével mélyebbre áshat. Példa:

    - Adott csomópontnaplóinak megtekintése:

        ![Adott hibák kimenetének keresése1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Adott hibák kimenetének keresése1")

    - Naplók megtekintése bizonyos időpontokban:

        ![Adott hibák keresése kimenet2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Adott hibák keresése kimenet2")

1. Válassza **& Futtatás alkalmazása** és az eredmények áttekintése lehetőséget. Azt is vegye figyelembe, hogy a lekérdezés frissítve lett:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>További mintalekérdezések

Mintalekérdezés a 10 perces időközben használt erőforrások átlaga alapján, fürtnév szerint csoportosítva:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

A felhasznált erőforrások átlaga alapján történő finomítás helyett a következő lekérdezéssel finomíthatja az eredményeket a maximális erőforrások felhasználása (valamint a 90. és 95. percentilis) alapján egy 10 perces ablakban:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Értesítések létrehozása az események nyomon követéséhez

A riasztás létrehozásának első lépése egy olyan lekérdezés, amely alapján a riasztás aktiválódik. Bármilyen lekérdezést használhat, amelyet riasztást szeretne létrehozni.

1. Nyissa meg a Log Analytics munkaterületet, amely a HDInsight-fürthöz van társítva az Azure Portalról.
1. **Az Általános csoportban**válassza **a Naplók**lehetőséget.
1. Futtassa a következő lekérdezést, amelyen riasztást szeretne létrehozni, majd válassza a **Futtatás**lehetőséget.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    A lekérdezés a HDInsight-fürtökön futó sikertelen alkalmazások listáját tartalmazza.

1. Válassza az **Új figyelmeztetési szabály** lehetőséget a lap tetején.

    ![Írjon be lekérdezést a riasztás létrehozásához1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Írjon be lekérdezést a riasztás létrehozásához1")

1. A **Szabály létrehozása** ablakban adja meg a lekérdezést és az egyéb részleteket a riasztás létrehozásához, majd válassza **a Figyelmeztetési szabály létrehozása**lehetőséget.

    ![Írjon be lekérdezést a riasztás létrehozásához2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Írjon be lekérdezést a riasztás létrehozásához2")

### <a name="edit-or-delete-an-existing-alert"></a>Meglévő riasztás szerkesztése vagy törlése

1. Nyissa meg a Log Analytics munkaterületet az Azure Portalon.

1. A bal oldali menü **Figyelés**területén válassza a **Riasztások**lehetőséget.

1. A lap tetejére válaszd a **Riasztási szabályok kezelése**lehetőséget.

1. Jelölje ki a szerkesztni vagy törölni kívánt riasztást.

1. A következő lehetőségek közül választhat: **Mentés,** **Elvetés,** **Letiltás**és **Törlés**.

    ![HDInsight Azure Monitor naplók riasztás törlés szerkesztés](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

További információ: [Metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával című témakörben.](../azure-monitor/platform/alerts-metric.md)

## <a name="see-also"></a>Lásd még

* [A naplólekérdezések első lépései az Azure Monitorban](../azure-monitor/log-query/get-started-queries.md)
* [Egyéni nézetek létrehozása a Nézettervező használatával az Azure Monitorban](../azure-monitor/platform/view-designer.md)
