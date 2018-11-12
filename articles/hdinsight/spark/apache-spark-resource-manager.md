---
title: Az Azure HDInsight az Apache Spark-fürt erőforrásainak kezelése
description: Ismerje meg, hogyan használható a jobb teljesítmény érdekében az Azure HDInsight Spark-fürtök erőforrásainak kezelése.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: 166d52322504e57e01a4bd798252fc956c1a3e44
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014584"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Az Azure HDInsight az Apache Spark-fürt erőforrásainak kezelése 

Ismerje meg, hogyan lehet hozzáférni a felületek, például az Ambari felhasználói felületén, a YARN felhasználói felületén és a Spark-Előzménykiszolgáló a Spark-fürthöz társított, és a fürt konfigurációját az optimális teljesítmény hangolása.

**Előfeltételek:**

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Nyissa meg az Ambari webes Felülettel

Az Apache Ambari segítségével figyelheti a fürt és a konfigurációs módosítások. További információkért lásd: [kezelése Hadoop-fürtök a HDInsight az Azure portal használatával](../hdinsight-administer-use-portal-linux.md#open-the-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Nyissa meg a Spark-Előzménykiszolgáló

Spark-Előzménykiszolgáló a webes felhasználói felület a befejezett és futó Spark-alkalmazások. A Spark a webes felhasználói felület kiterjesztése.

**A Spark előzmények kiszolgáló webes felhasználói felület megnyitása**

1. Az a [az Azure portal](https://portal.azure.com/), nyissa meg a Spark-fürtön. További információkért lásd: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. A **Gyorshivatkozások**, kattintson a **fürt irányítópultja**, és kattintson a **Spark-Előzménykiszolgáló**

    ![Spark-Előzménykiszolgáló](./media/apache-spark-resource-manager/launch-history-server.png "Spark-Előzménykiszolgáló")

    Amikor a rendszer kéri, adja meg a rendszergazdai hitelesítő adatokat a Spark-fürtön. A Spark-Előzménykiszolgáló megnyitásához keresse meg a következő URL-címe:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Cserélje le <ClusterName> az a Spark-fürt neve.

A Spark-Előzménykiszolgáló webes felhasználói felület hasonlóan néz ki:

![HDInsight Spark-Előzménykiszolgáló](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Nyissa meg a Yarn felhasználói felületén
Használhatja a YARN felhasználói felületén, a Spark-fürtön futó alkalmazások figyeléséhez.

1. Az a [az Azure portal](https://portal.azure.com/), nyissa meg a Spark-fürtön. További információkért lásd: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. A **Gyorshivatkozások**, kattintson a **fürt irányítópultja**, és kattintson a **YARN**.

    ![Indítsa el a YARN felhasználói felületén](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Másik lehetőségként az Ambari felhasználói felületén, a YARN felhasználói felületén is megnyithatja. Az Ambari felhasználói felületén indításához kattintson **fürt irányítópultja**, és kattintson a **HDInsight-fürt irányítópultja**. Az Ambari felhasználói felületén kattintson **YARN**, kattintson a **Gyorshivatkozások**, kattintson az aktív erőforrás-kezelő, majd **erőforrás-kezelő felhasználói felületén**.
   >
   >

## <a name="optimize-clusters-for-spark-applications"></a>Spark-alkalmazások esetében optimalizálása
A három fő paraméterek alkalmazás követelményeitől függően a Spark-konfigurációhoz használható `spark.executor.instances`, `spark.executor.cores`, és `spark.executor.memory`. Az Executor egy folyamat egy Spark-alkalmazás esetén. A feldolgozó csomóponton fut, és felelős az alkalmazáshoz tartozó feladatok elvégzését. Az alapértelmezett száma végrehajtóval és az egyes fürtök végrehajtó mérete számítása munkavégző csomópontok és a munkavégző csomópont méretét száma alapján történik. Ez az információ a tárolt `spark-defaults.conf` a fő fürtcsomópontokon.

A három kvórumbeállítási mód paraméterek (az összes alkalmazáshoz a fürtön futó) a fürt szintjén konfigurálhatók, vagy minden egyes alkalmazáshoz adható meg.

### <a name="change-the-parameters-using-ambari-ui"></a>Az Ambari felhasználói felületén a paraméterek módosítása
1. Az Ambari felhasználói felületén kattintson a **Spark**, kattintson a **Configs**, majd **egyéni spark-alapértelmezett**.

    ![Az Ambari paramétereinek megadása](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Az alapértelmezett értékei négy Spark-alkalmazások egyidejű futtatását a fürt rendelkezik a megfelelő. Ezeket az értékeket módosíthatja a felhasználói felületen, az alábbi képernyőképen látható módon:

    ![Az Ambari paramétereinek megadása](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Kattintson a **mentése** konfiguráció módosításainak mentéséhez. Az oldal tetején lévő kéri, indítsa újra az érintett szolgáltatásokat. Kattintson a **Restart** (Újraindítás) elemre.

    ![Szolgáltatások újraindítása](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>A Jupyter notebook-ben futó alkalmazás paramétereinek módosítása
Alkalmazások futtatása a Jupyter notebookot, használhatja a `%%configure` Magic Quadrant a konfigurációs módosításokat. Ideális esetben módosítania kell az ilyen, az alkalmazás futtatása az első kódcella előtt elején. Ezáltal biztosítható, hogy a konfiguráció alkalmazása a Livy-munkamenethez, amikor létrejön. Ha azt szeretné, módosíthatja a konfigurációt, az alkalmazás egy későbbi szakaszban kell használnia a `-f` paraméter. Azonban így az alkalmazás folyamatban lévő elvész.

Az alábbi kódrészlet bemutatja, hogyan módosítható a Jupyter-ben futó alkalmazás konfigurációja.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Konfigurációs paramétereket kell átadni, egy JSON-karakterlánc, és későbbinek kell lennie a következő sorban a Magic Quadrant, ahogyan az a példában oszlop.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Módosítsa a paramétereket, az alkalmazás használatával elküldött spark-submit szkripttel
A következő parancs a példa bemutatja, hogyan módosíthatja a konfigurációs paraméterek használatával elküldött kötegelt alkalmazásokhoz `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>A cURL használatával kérelem paramétereinek módosítása
A következő parancsot a példa bemutatja, hogyan módosíthatja a cURL használatával elküldött batch-alkalmazás konfigurációs paraméterei.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Ezek a paraméterek a Spark Thrift-kiszolgáló módosítása
Spark Thrift-kiszolgáló JDBC/ODBC hozzáférést biztosít a Spark-fürt és a Spark SQL-lekérdezések szolgál. Eszközök, például a Power BI, Tableau stb. ODBC-protokoll használatával kommunikálnak a Spark SQL-lekérdezések végrehajtásához egy Spark-alkalmazás, Spark Thrift-kiszolgáló. Spark-fürt létrehozásakor a Spark Thrift-kiszolgáló két példánya elindulnak, egy fő a csomópontokra. Minden Spark Thrift-kiszolgáló, egy Spark-alkalmazás a YARN felhasználói felületén jelenik meg.

Spark Thrift-kiszolgáló használ a Spark végrehajtó dinamikus kiosztási és így a `spark.executor.instances` nincs használatban. Ehelyett használja a Spark Thrift-kiszolgáló `spark.dynamicAllocation.minExecutors` és `spark.dynamicAllocation.maxExecutors` a végrehajtó számának megadása. A konfigurációs paramétereket `spark.executor.cores` és `spark.executor.memory` végrehajtó méretének módosításához használatos. Módosíthatja ezeket a paramétereket, ahogyan az az alábbi lépéseket:

* Bontsa ki a **spark-thrift-sparkconf speciális** a kategóriát, frissítse a paramétereket `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, és `spark.executor.memory`.

    ![Spark thrift-kiszolgáló konfigurálása](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Bontsa ki a **spark-thrift-sparkconf egyéni** a kategóriát, frissítse a paraméter `spark.executor.cores`.

    ![Spark thrift-kiszolgáló konfigurálása](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Az illesztőprogram memória, a Spark Thrift-kiszolgáló módosítása
Spark Thrift-kiszolgáló illesztőprogram memóriát 25 %-a fő csomópontot RAM-MAL konfigurált, feltéve, 14 GB-nál nagyobb a fő csomópont teljes memória méretét. Használhatja az Ambari felhasználói felületén a illesztőprogram memória beállításainak módosításához, az alábbi képernyőképen látható módon:

* Az Ambari felhasználói felületén kattintson a **Spark**, kattintson **Configs**, bontsa ki a **spark-env speciális**, és adja meg az értékét **spark_thrift_cmd_opts**.

    ![Spark thrift-kiszolgáló RAM-MAL konfigurálása](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Spark-fürt erőforrásainak felszabadítása
Spark dinamikus kiosztási, mert a thrift-kiszolgáló által felhasznált erőforrások kizárólag olyan az erőforrásokat a két alkalmazás főkiszolgálóhoz. Ezeket az erőforrásokat felszabadítani, le kell állítania a fürtön futó Thrift-kiszolgáló szolgáltatás.

1. Az Ambari felhasználói felület, a bal oldali ablaktáblában kattintson **Spark**.
2. A következő oldalon kattintson a **Spark Thrift-kiszolgáló**.

    ![Indítsa újra a thrift-kiszolgáló](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. A két átjárócsomópontokra, amelyeken a Spark Thrift-kiszolgáló fut-e megjelennie. Kattintson az egyik ezután az átjárócsomópontokról.

    ![Indítsa újra a thrift-kiszolgáló](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. A következő lap felsorolja az adott átjárócsomópontjával futó összes szolgáltatás. A listában kattintson a legördülő menü gombra, Spark Thrift-kiszolgáló mellett, és kattintson **leállítása**.

    ![Indítsa újra a thrift-kiszolgáló](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Ismételje meg ezeket a lépéseket a többi átjárócsomópontjával is.

## <a name="restart-the-jupyter-service"></a>A Jupyter-szolgáltatás újraindítása
Indítsa el az Ambari webes felhasználói Felületet, ahogyan az a cikk elején. A bal oldali navigációs ablaktáblán kattintson a **Jupyter**, kattintson a **szolgáltatás műveletek**, és kattintson a **indítsa újra az összes**. A Jupyter-szolgáltatás az összes átjárócsomópontokkal elindul.

![Indítsa újra a Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Jupyter újraindítása")

## <a name="monitor-resources"></a>Erőforrások megfigyelése
Indítsa el a Yarn felhasználói felületén, ahogyan az a cikk elején. Fürtmetrikák táblában a képernyő fölött, ellenőrizze az értékeket **felhasznált memória** és **memória teljes** oszlopokat. Zárja be a két érték esetén a nem feltétlenül elegendő erőforrás a következő alkalmazás elindításához. Ugyanez vonatkozik a **használatban lévő virtuális magok** és **virtuális magok összesen** oszlopokat. Is, a fő megtekintheti, ha egy alkalmazás időpontjáig maradtak a **ELFOGADVA** állapotát, és azokat nem transitioning **FUTÓ** sem **sikertelen** állapotba, ez is lehet arra utal, amely azt nem sikerül elindítani erőforrásokkal.

![Erőforrás-korlát](./media/apache-spark-resource-manager/resource-limit.png "Erőforráskorlátot")

## <a name="kill-running-applications"></a>A Kill futó alkalmazások
1. A Yarn felhasználói felületén, a bal oldali panelen kattintson a **futó**. Határozza meg az alkalmazás le fognak állni, és kattintson az alkalmazások listájában, a **azonosító**.

    ![Az App1 Kill](./media/apache-spark-resource-manager/kill-app1.png "Kill App1")

2. Kattintson a **Kill alkalmazás** a jobb felső sarokban, majd **OK**.

    ![A Kill App2](./media/apache-spark-resource-manager/kill-app2.png "Kill App2")

## <a name="see-also"></a>Lásd még
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Az adatelemzők számára

* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insights telemetriai adatainak elemzése a Spark on HDInsight használatával](apache-spark-analyze-application-insight-logs.md)
* [Caffe elosztott deep learning az Azure HDInsight Spark használata](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Spark-fejlesztőknek

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)
