---
title: Azure HDInsight-fürtök figyelése az Azure Naplóelemzés lekérdezése |} Microsoft Docs
description: Útmutató a HDInsight-fürtök a futó feladatok figyelése az Azure Naplóelemzés lekérdezések futtatása.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 61467d702f3123085fd7e067a8d56c30331c5bc6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters"></a>A HDInsight-fürtök figyelése az Azure Naplóelemzés lekérdezése

Megtudhatja, hogyan Azure Naplóelemzés a figyelheti az Azure HDInsight-fürtök néhány alapvető forgatókönyv:

* [A HDInsight fürt metrikáinak elemzése](#analyze-hdinsight-cluster-metrics)
* [Adott naplóüzenetek keresése](#search-for-specific-log-messages)
* [Hozzon létre miatti riasztás](#create-alerts-for-tracking-events)

## <a name="prerequisites"></a>Előfeltételek

* Konfigurálnia kell egy HDInsight-fürt használata az Azure Naplóelemzés. Útmutatásért lásd: [használata Azure Naplóelemzés HDInsight-fürtökkel](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* Hozzá kellett adnia a HDInsight fürt-specifikus felügyeleti megoldásokat a [Naplóelemzési](../operations-management-suite/operations-management-suite-overview.md) a munkaterület [adja hozzá a HDInsight fürt felügyeleti megoldásokat Naplóelemzési](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics"></a>A HDInsight fürt metrikáinak elemzése

Megtudhatja, hogyan keresse meg a HDInsight-fürthöz adott mérőszámok.

1. Nyissa meg a HDInsight-fürtöt, amely a társított Azure Log Analytics az Azure portálon.
2. Kattintson a **figyelés**, és kattintson a **nyitott OMS irányítópult**.

    ![Nyissa meg OMS irányítópult](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "nyitott OMS irányítópult")

2. Kattintson a **naplófájl-keresési** a bal oldali menüben.

    ![Nyissa meg a keresési napló](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "nyissa meg a keresési napló")

3. Keresse meg az összes elérhető metrikai meghatározások használatára konfigurálva Azure Naplóelemzés, majd nyomja le az összes HDInsight-fürtök összes metrikát a keresőmezőbe írja be a következő lekérdezés **ENTER**.

        `search *` 

    ![Összes metrikát keresési](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "összes metrikát keresése")

    A kimeneti kell következőhöz hasonló:

    ![Minden metrikák kimeneti keresési](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "összes metrikák kimeneti keresése")

5. A bal oldali ablaktáblán a **típus**, jelöljön ki egy metrikát hatolva olyan átrágniuk, és kattintson a kívánt **alkalmaz**. Az alábbi képernyőfelvételen látható a `metrics_resourcemanager_queue_root_default_CL` típus van kiválasztva. 

    > [!NOTE]
    > Szükség lehet annak, kattintson a **[+] további** találja a keresett metrika gombra. Emellett a **alkalmaz** gomb úgy kell görgessen lefelé meg szeretné tekinteni, akkor a lista alján.
    > 
    >    

    Figyelje meg, hogy megváltozik-e a lekérdezés a szövegmezőben egy kiemelt mezőjében az alábbi képernyőfelvételen látható:

    ![Keresse meg az adott mérőszámok](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "adott mérőszámok keresése")

6. A feltárva ezt a mértéket. Például pontosíthatja a meglévő kimenete egy 10 perces időszakban, a fürt neve a következő lekérdezéssel kategorizálta használt erőforrások alapján:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

7. Helyett finomítása használt erőforrások alapján, segítségével a következő lekérdezés alapján, ha az erőforrások (valamint 90 és 95. percentilis) használt eredmények szűkítéséhez 10 perces ablakban:

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>Adott naplóüzenetek keresése

Megtudhatja, hogyan keresse meg a hibaüzeneteket adott időszak alatt történjen. Az itt lépésekre mindössze egy példa a hogyan lehet megérkezik a hibaüzenet akkor is érdeklődik. Tulajdonsággal bármely elérhető található kívánt hibákat keres.

1. Nyissa meg a HDInsight-fürtöt, amely a társított Azure Log Analytics az Azure portálon.
2. Kattintson a **figyelés**, és kattintson a **nyitott OMS irányítópult**.

    ![Nyissa meg OMS irányítópult](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "nyitott OMS irányítópult")

2. Kattintson az OMS-portálon, a kezdőképernyőn a **naplófájl-keresési**.

    ![Nyissa meg a keresési napló](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "nyissa meg a keresési napló")

3. Típus a következő lekérdezés az összes HDInsight-fürtök Azure Naplóelemzés használatára konfigurált összes hibaüzenet kereséséhez, és nyomja le az **ENTER**. 

         search "Error"

    A következő kimeneti hasonló kimenetnek kell jelenik meg:

    ![Minden hibák kimeneti keresési](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "összes hibák kimeneti keresése")

5. A bal oldali ablaktáblán a **típus** kategória, válassza ki egy hiba, amelyet szeretne hatolva olyan átrágniuk, és kattintson **alkalmaz**.  Figyelje meg, az eredmények kifinomultabb lett csak a kijelölt hiba megjelenítése.
7. Ön feltárva a konkrét hiba lista a bal oldali ablaktáblán rendelkezésre álló lehetőségek segítségével. Például: 

    - A munkavégző csomópont hibaüzenetek megtekintéséhez:

        ![Keresse meg bizonyos hibákat kimeneti](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "bizonyos hibákat kimeneti keresése")

    - Hiba történt az egyes egyszerre:

        ![Keresse meg bizonyos hibákat kimeneti](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "bizonyos hibákat kimeneti keresése")

9. Az adott hiba megtekintéséhez. Kattinthat **[+] megjelenítése további** az aktuális hibaüzenetben lássunk.

    ![Keresse meg bizonyos hibákat kimeneti](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "bizonyos hibákat kimeneti keresése")

## <a name="create-alerts-for-tracking-events"></a>Hozzon létre riasztásokat események nyomon követése

Első lépése a riasztás létrehozása, hogy a lekérdezés, amely a figyelmeztetés alapján az ügyfélszámítógépekre érkeznek. Az egyszerűség kedvéért tételezzük használja a következő lekérdezést, amely a HDInsight-fürtökön futó sikertelen alkalmazások listáját tartalmazza.

    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

Riasztás létrehozása kívánt lekérdezés is használhatja.

1. Nyissa meg a HDInsight-fürtöt, amely a társított Azure Log Analytics az Azure portálon.
2. Kattintson a **figyelés**, és kattintson a **nyitott OMS irányítópult**.

    ![Nyissa meg OMS irányítópult](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "nyitott OMS irányítópult")

2. Kattintson az OMS-portálon, a kezdőképernyőn a **naplófájl-keresési**.

    ![Nyissa meg a keresési napló](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "nyissa meg a keresési napló")

3. Futtassa a következő lekérdezést, amely a riasztás létrehozása, és nyomja le az kívánt **ENTER**.

        metrics_resourcemanager_queue_root-default-CL | where AppsFailed_d > 0

4. Kattintson a **riasztási** az oldal tetején.

    ![Riasztás létrehozása ENTER lekérdezés](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Enter lekérdezés riasztás létrehozása")

4. Az a **riasztás szabály hozzáadása** ablakban adja meg a lekérdezés és egyéb adatait, hozzon létre egy riasztást, majd **mentése**.

    ![Riasztás létrehozása ENTER lekérdezés](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Enter lekérdezés riasztás létrehozása")

    A képernyőfelvételen látható e-mail értesítést küld a riasztási lekérdezés visszaadja egy kimeneti konfigurációját.

5. Szerkesztheti és törölheti a meglévő riasztás is. Ehhez a bármelyik oldalt az OMS-portálon kattintson a **beállítások** ikonra.

    ![Riasztás létrehozása ENTER lekérdezés](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Enter lekérdezés riasztás létrehozása")

6. Az a **beállítások** kattintson **riasztások** hozott létre a riasztások megjelenítéséhez. Akkor is engedélyezheti vagy tiltsa le a riasztást, szerkesztheti, vagy törölje azt. További információkért lásd: [Naplóelemzési a riasztási szabályok használata](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Lásd még

* [A Naplóelemzési használata](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [A Naplóelemzési riasztási szabályok létrehozása](../log-analytics/log-analytics-alerts-creating.md)
