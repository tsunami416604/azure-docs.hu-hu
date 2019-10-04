---
title: Azure Monitor naplók lekérdezése az Azure HDInsight-fürtök figyeléséhez
description: Megtudhatja, hogyan futtathat lekérdezéseket Azure Monitor naplókon a HDInsight-fürtön futó feladatok figyeléséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 51344ff7381b6392870b1fd0e331eed38a33915d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103522"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor naplók lekérdezése HDInsight-fürtök figyeléséhez

Ismerkedjen meg az Azure HDInsight-fürtök figyelésére szolgáló Azure Monitor-naplók használatával kapcsolatos alapvető forgatókönyvekkel:

* [HDInsight-fürt metrikáinak elemzése](#analyze-hdinsight-cluster-metrics)
* [Adott naplóüzenetek keresése](#search-for-specific-log-messages)
* [Esemény-riasztások létrehozása](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

Konfigurálnia kell egy HDInsight-fürtöt, hogy Azure Monitor naplókat használjon, és hozzáadta a HDInsight-fürthöz tartozó Azure Monitor naplók figyelési megoldásait a munkaterülethez. Útmutatásért lásd: [Azure monitor naplók használata HDInsight-fürtökkel](hdinsight-hadoop-oms-log-analytics-tutorial.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>HDInsight-fürt metrikáinak elemzése

Megtudhatja, hogyan kereshet konkrét mérőszámokat a HDInsight-fürthöz.

1. Nyissa meg a HDInsight-fürthöz társított Log Analytics munkaterületet a Azure Portal.
1. Válassza ki a **naplóbeli keresés** csempét.
1. Írja be a következő lekérdezést a keresőmezőbe, hogy megkeresse az összes rendelkezésre álló metrikát a Azure Monitor naplók használatára konfigurált összes HDInsight-fürthöz, majd válassza a **Futtatás**lehetőséget.

        search *

    ![Apache Ambari Analytics keresés az összes mérőszámban](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Keresés az összes mérőszámban")

    A kimenetnek a következőképpen kell kinéznie:

    ![log Analytics-keresés az összes metrika](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Keresés az összes mérőszám kimenetében")

1. A bal oldali ablaktábla **típus**területén válassza ki azt a metrikát, amelyet mélyen be szeretne ásni, majd válassza az **alkalmaz**lehetőséget. Az alábbi képernyőfelvételen a `metrics_resourcemanager_queue_root_default_CL` típus van kiválasztva.

    > [!NOTE]  
    > Előfordulhat, hogy a **[+] további** gombot kell kiválasztania a keresett metrika megkereséséhez. Emellett az **Apply (alkalmaz** ) gomb a lista alján található, így a megtekintéshez le kell görgetni.

    Figyelje meg, hogy a szövegmezőben lévő lekérdezés a következő képernyőképen látható Kiemelt mezőben jelenik meg:

    ![log Analytics-keresés – specifikus mérőszámok](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Konkrét mérőszámok keresése")

1. Az adott metrika mélyebb kiásása. Megadhatja például a meglévő kimenetet a 10 perces intervallumban használt erőforrások átlaga alapján, a fürt neve szerint kategorizálva a következő lekérdezés használatával:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

1. A felhasznált erőforrások átlaga alapján történő finomítás helyett a következő lekérdezéssel finomíthatja az eredményeket, ha a maximális erőforrást (valamint a 90-es és a 95. percentilis-t) használja egy 10 perces ablakban:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Adott naplóüzenetek keresése

Megtudhatja, hogyan tekintheti meg a hibaüzeneteket egy adott időszak során. Az alábbi lépések csak egy példát mutatnak arra, hogy miként lehet megérkezni a kívánt hibaüzenetre. Bármilyen olyan tulajdonságot használhat, amely elérhető a keresett hibák kereséséhez.

1. Nyissa meg a HDInsight-fürthöz társított Log Analytics munkaterületet a Azure Portal.
2. Válassza ki a **naplóbeli keresés** csempét.
3. Írja be a következő lekérdezést a Azure Monitor naplók használatára konfigurált összes HDInsight-fürt összes hibaüzenetének megkereséséhez, majd válassza a **Futtatás**lehetőséget.

         search "Error"

    A következő kimenethez hasonló kimenetnek kell megjelenni:

    ![Naplóbeli keresési hibák Azure Portal](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Keresés az összes hiba kimenetében")

4. A bal oldali ablaktábla **típus** kategóriája területen válasszon ki egy olyan hibát, amelyet mélyen be szeretne ásni, majd válassza az **alkalmaz**lehetőséget.  Figyelje meg, hogy a rendszer finomítja az eredményeket, hogy csak a kiválasztott típus hibáját jelenítse meg.

5. A bal oldali panelen elérhető lehetőségek segítségével mélyebbre is kihasználhatja a hibák listáját. Példa:

    - Egy adott munkavégző csomópont által küldött hibaüzenetek megtekintéséhez:

        ![Adott hibák keresése output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Adott hibák keresése output1")

    - Egy adott időpontban észlelt hiba:

        ![Adott hibák keresése output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Adott hibák keresése output2")

6. Az adott hiba megtekintéséhez. A **[+]** lehetőség kiválasztásával megtekintheti a tényleges hibaüzenetet.

    ![Adott hibák keresése output3](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Adott hibák keresése output3")

## <a name="create-alerts-for-tracking-events"></a>Riasztások létrehozása a követési eseményekhez

A riasztás létrehozásának első lépése egy olyan lekérdezés megérkezése, amely alapján a riasztás aktiválva van. Bármely olyan lekérdezést használhat, amelyhez riasztást szeretne létrehozni.

1. Nyissa meg a HDInsight-fürthöz társított Log Analytics munkaterületet a Azure Portal.
2. Válassza ki a **naplóbeli keresés** csempét.
3. Futtassa a következő lekérdezést, amelyen riasztást szeretne létrehozni, majd válassza a **Futtatás**lehetőséget.

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    A lekérdezés a HDInsight-fürtökön futó sikertelen alkalmazások listáját tartalmazza.

4. Az oldal tetején válassza az **új riasztási szabály** lehetőséget.

    ![Lekérdezés megadása alert1 létrehozásához](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Lekérdezés megadása alert1 létrehozásához")

5. A **szabály létrehozása** ablakban adja meg a lekérdezést és egyéb adatokat a riasztás létrehozásához, majd válassza a **riasztási szabály létrehozása**lehetőséget.

    ![Lekérdezés megadása alert2 létrehozásához](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Lekérdezés megadása alert2 létrehozásához")

Meglévő riasztás szerkesztése vagy törlése:

1. Nyissa meg az Log Analytics munkaterületet a Azure Portal.
2. A bal oldali menüben válassza a **riasztás**elemet.
3. Válassza ki a szerkeszteni vagy törölni kívánt riasztást.
4. A következő lehetőségek közül választhat: **Mentés**, **Elvetés**, **Letiltás**és **Törlés**.

    ![HDInsight Azure Monitor naplók riasztás törlésének szerkesztése](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

További információ: [metrikus riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../azure-monitor/platform/alerts-metric.md).

## <a name="see-also"></a>Lásd még

* [Egyéni nézetek létrehozása a Azure Monitor View Designer használatával](../azure-monitor/platform/view-designer.md)
* [Metrikai riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](../azure-monitor/platform/alerts-metric.md)
