---
title: "Azure HDInsight-fürtök figyelése az Azure Naplóelemzés lekérdezése |} Microsoft Docs"
description: "Útmutató a HDInsight-fürtök a futó feladatok figyelése az Azure Naplóelemzés lekérdezések futtatása."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: nitinme
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Lekérdezés Azure Naplóelemzés figyelése a HDInsight-fürtök (előzetes verzió)

Ebben a cikkben tekinti meg bizonyos esetekben az Azure Naplóelemzés használata Azure HDInsight-fürtök. Három leggyakoribb forgatókönyvek a következők:

* A HDInsight fürt metrikát a OMS elemzése
* A HDInsight-fürtök adott naplóbejegyzéseit keresése
* A fürtök bekövetkező események alapján figyelmeztetések létrehozása

## <a name="prerequisites"></a>Előfeltételek

* Konfigurálnia kell egy HDInsight-fürt használata az Azure Naplóelemzés. Útmutatásért lásd: [használata Azure Naplóelemzés HDInsight-fürtökkel](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* Hozzá kellett adnia a HDInsight-fürtre kezelési megoldásai az OMS-munkaterület leírtak szerint [adja hozzá a HDInsight fürt felügyeleti megoldásokat Naplóelemzési](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>A HDInsight fürt metrikát a OMS elemzése

Ez a szakasz azt lépésről lépésre bemutatjuk, amelyet meg kíván keresni az adott mérőszámok a HDInsight-fürthöz.

1. Nyissa meg az OMS-irányítópultot. Az Azure-portálon az Azure Naplóelemzés társított HDInsight-fürt panelén megnyitásához kattintson a figyelés lapon, és kattintson az **OMS irányítópult megnyitása**.

    ![Nyissa meg OMS irányítópult](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "nyitott OMS irányítópult")

2. Az OMS-irányítópult, a kezdőképernyőn kattintson **naplófájl-keresési**.

    ![Nyissa meg a keresési napló](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "nyissa meg a keresési napló")

3. A naplófájl-keresési ablakában a a **Begin Keresés itt** szövegmezőben `*` minden mérőszám az összes elérhető metrikai meghatározások Azure Naplóelemzés használatára konfigurált összes HDInsight-fürtök kereséséhez. Nyomja le az ENTER BILLENTYŰT.

    ![Összes metrikát keresési](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "összes metrikát keresése")

4. A következőhöz hasonló kimenetnek kell megjelennie.

    ![Minden metrikák kimeneti keresési](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "összes metrikák kimeneti keresése")

5. A bal oldali ablaktáblán a **típus** kategória, keresés a dig hatolva olyan kívánt mértéket. Ebben az oktatóanyagban most válasszon `metrics_resourcemanager_queue_root_default_CL`. Jelölje be a jelölőnégyzetet, hogy a mérték megfelelő, és kattintson **alkalmaz**.

    > [!NOTE]
    > Előfordulhat, hogy kell kattintania a **[+] további** találja a keresett metrika gombra. Emellett a **alkalmaz** gomb úgy kell görgessen lefelé meg szeretné tekinteni, akkor a lista alján.
    > 
    >    
    Figyelje meg, hogy a lekérdezés a szövegmezőben most módosítja egy kiemelt mezőjében az alábbi képernyőfelvételen látható:

    ![Keresse meg az adott mérőszámok](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "adott mérőszámok keresése")

6. Ön most feltárva ezt a mértéket. Például most pontosíthatja a meglévő kimenete egy 10 perc időszakban, a fürt neve szerint használt erőforrások alapján. Írja be a következő lekérdezés a lekérdezés szövegmezőben.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![Keresse meg az adott mérőszámok](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "adott mérőszámok keresése")

7. Helyett finomítása használt erőforrások alapján, a 10 perces ablakban használhatja a következő lekérdezés alapján, ha az erőforrások (valamint 90 és 95. percentilis) használt eredmények szűkítéséhez.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![Keresse meg az adott mérőszámok](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "adott mérőszámok keresése")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>Keresse meg az adott naplózási üzenetek a HDInsight-fürtök

Ez a szakasz azt lépésről lépésre bemutatjuk, hibaüzenetek jelennek meg egy adott időszakban. Az itt lépésekre mindössze egy példa a hogyan lehet megérkezik a hibaüzenet akkor is érdeklődik. Tulajdonsággal bármely elérhető található kívánt hibákat keres.

1. Nyissa meg az OMS-irányítópultot. Az Azure-portálon az Azure Naplóelemzés társított HDInsight-fürt panelén megnyitásához kattintson a figyelés lapon, és kattintson az **OMS irányítópult megnyitása**.

    ![Nyissa meg OMS irányítópult](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "nyitott OMS irányítópult")

2. Az OMS-irányítópult, a kezdőképernyőn kattintson **naplófájl-keresési**.

    ![Nyissa meg a keresési napló](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "nyissa meg a keresési napló")

3. A naplófájl-keresési ablakában a a **Begin Keresés itt** szövegmezőben `"Error"` (az idézőjelekkel együtt) az Azure Naplóelemzés használatára konfigurált összes HDInsight-fürtök összes hibaüzenet kereséséhez. Nyomja le az ENTER BILLENTYŰT.

    ![Keressen hibákat az](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "hibákat keresése")

4. A következőhöz hasonló kimenetnek kell megjelennie.

    ![Minden hibák kimeneti keresési](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "összes hibák kimeneti keresése")

5. A bal oldali ablaktáblán a **típus** kategória, keressen egy hatolva olyan dig kívánt típus. Ebben az oktatóanyagban most válasszon `log_sparkappsexecutors_CL`. Jelölje be a jelölőnégyzetet, hogy a mérték megfelelő, és kattintson **alkalmaz**.

    ![Keresse meg bizonyos hibákat](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "keresse meg a hiba")

        
6. Figyelje meg, hogy a lekérdezés a szövegmezőben most módosítja a látható a kijelölt mezőt és az eredményeket egy olyan kifinomultabb csak a kijelölt hiba megjelenítéséhez.

    ![Keresse meg bizonyos hibákat kimeneti](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "bizonyos hibákat kimeneti keresése")

7. Ön most feltárva a konkrét hiba lista a bal oldali ablaktáblán rendelkezésre álló lehetőségek segítségével. Például finomíthatja a lekérdezést, hogy egy adott munkavégző csomópont hibaüzenetek csak megtekinteni.

    ![Keresse meg bizonyos hibákat kimeneti](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "bizonyos hibákat kimeneti keresése")

8. A modult zónát a idővel úgy gondolja, hogy a hiba történt a bal oldali ablaktáblán a megfelelő időben érték kiválasztásával további.

    ![Keresse meg bizonyos hibákat kimeneti](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "bizonyos hibákat kimeneti keresése")

9. Most már áll le az adott hiba, amely keres. Kattinthat **[+] megjelenítése további** az aktuális hibaüzenetben lássunk.

    ![Keresse meg bizonyos hibákat kimeneti](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "bizonyos hibákat kimeneti keresése")

## <a name="create-alerts-to-track-events"></a>Események nyomon követéséhez értesítések

Első lépése a riasztás létrehozása, hogy a lekérdezés, amely a figyelmeztetés alapján az ügyfélszámítógépekre érkeznek. Az egyszerűség kedvéért tételezzük használja a következő lekérdezést, amely a HDInsight-fürtökön futó sikertelen alkalmazások listáját tartalmazza.

    * (Típus = metrics_resourcemanager_queue_root_default_CL) AppsFailed_d > 0 

Riasztás létrehozása kívánt lekérdezés is használhatja.

1. Nyissa meg az OMS-irányítópultot. Az Azure-portálon az Azure Naplóelemzés társított HDInsight-fürt panelén megnyitásához kattintson a figyelés lapon, és kattintson az **OMS irányítópult megnyitása**.

    ![Nyissa meg OMS irányítópult](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "nyitott OMS irányítópult")

2. Az OMS-irányítópult, a kezdőképernyőn kattintson **naplófájl-keresési**.

    ![Nyissa meg a keresési napló](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "nyissa meg a keresési napló")

3. A naplófájl-keresési ablakában a a **Begin Keresés itt** szöveg mezőbe illessze be a lekérdezést, amely a riasztás létrehozása, nyomja le az ENTER BILLENTYŰT, és kattintson szeretné a **riasztás** gombra.

    ![Riasztás létrehozása ENTER lekérdezés](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Enter lekérdezés riasztás létrehozása")

4. Az a **riasztás szabály hozzáadása** ablakban adja meg a lekérdezés és egyéb adatait, hozzon létre egy riasztást, majd **mentése**.

    ![Riasztás létrehozása ENTER lekérdezés](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Enter lekérdezés riasztás létrehozása")

    Ezen a képernyőfelvételen látható csak kapni az e-mail értesítések, ha a riasztási lekérdezés lekéri kimenettel.

5. Szerkesztheti és törölheti a meglévő riasztás is. Ehhez a bármelyik oldalt az OMS-portálon kattintson a **beállítások** ikonra.

    ![Riasztás létrehozása ENTER lekérdezés](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Enter lekérdezés riasztás létrehozása")

6. Az a **beállítások** kattintson **riasztások** hozott létre a riasztások megjelenítéséhez. Akkor is engedélyezheti vagy tiltsa le a riasztást, szerkesztheti, vagy törölje azt. További információkért lásd: [Naplóelemzési a riasztási szabályok használata](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Lásd még:

* [OMS Naplóelemzési használata](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [A Naplóelemzési riasztási szabályok létrehozása](../log-analytics/log-analytics-alerts-creating.md)
