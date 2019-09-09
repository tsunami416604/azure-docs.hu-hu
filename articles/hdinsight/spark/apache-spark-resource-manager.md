---
title: Apache Spark-fürt erőforrásainak kezelése az Azure HDInsight
description: Megtudhatja, hogyan használhatja a jobb teljesítmény érdekében a Spark-fürtök erőforrásainak kezelése az Azure HDInsight-ben című témakört.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: hrasheed
ms.openlocfilehash: 0d97ca91466516b8722ecca77d19078399a258f7
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814094"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Apache Spark-fürt erőforrásainak kezelése az Azure HDInsight 

Megtudhatja, hogyan érheti el az olyan felületeket, mint az [Apache Ambari](https://ambari.apache.org/) felhasználói felület, a [Apache Hadoop a fonal](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) felhasználói felülete, valamint a [Apache Spark](https://spark.apache.org/) -fürthöz társított Spark History-kiszolgáló, valamint az optimális teljesítmény érdekében a fürtkonfiguráció finomhangolása.

**Előfeltételek:**

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>A Ambari webes felhasználói felületének megnyitása

Az Apache Ambari használatával figyelheti a fürtöt, és módosíthatja a konfigurációt. További információ: [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](../hdinsight-administer-use-portal-linux.md#open-the-apache-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>A Spark History-kiszolgáló megnyitása

A Spark History Server a Spark-alkalmazások befejezéséhez és futtatásához használható webes KEZELŐFELÜLET. Ez a Spark webes felhasználói felületének kiterjesztése.

**A Spark History-kiszolgáló webes felhasználói felületének megnyitása**

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a Spark-fürtöt. További információ: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters).
2. A **Gyorshivatkozások**területen kattintson a **fürt irányítópultja**, majd a **Spark-előzmények kiszolgáló** elemre.

    ![Spark-előzmények kiszolgálója](./media/apache-spark-resource-manager/launch-history-server.png "Spark-előzmények kiszolgálója")

    Ha a rendszer kéri, adja meg a Spark-fürt rendszergazdai hitelesítő adatait. A Spark History-kiszolgáló a következő URL-címre való tallózással is megnyitható:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Cserélje `<ClusterName>` le a nevet a Spark-fürt nevére.

A Spark History-kiszolgáló webes felhasználói felülete a következőképpen néz ki:

![HDInsight Spark-előzmények kiszolgálója](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>A fonal felhasználói felületének megnyitása
A fonal felhasználói felületét használhatja a Spark-fürtön jelenleg futó alkalmazások figyelésére.

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a Spark-fürtöt. További információ: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters).
2. A **Gyorshivatkozások**területen kattintson a **fürt irányítópultja**, majd a **fonal**elemre.

    ![A fonal felhasználói felületének indítása](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]  
   > Azt is megteheti, hogy a Ambari felhasználói felületéről is elindítja a fonal felhasználói felületét. A Ambari felhasználói felületének elindításához kattintson a **fürt irányítópultja**, majd a **HDInsight-fürt irányítópultja**elemre. A Ambari felhasználói felületén kattintson a **fonal**elemre, kattintson a **Gyorshivatkozások**lehetőségre, majd az Active Resource Manager elemre, végül pedig a **Resource Manager felhasználói felület**elemre.

## <a name="optimize-clusters-for-spark-applications"></a>Fürtök optimalizálása Spark-alkalmazásokhoz
A Spark konfigurálásához használható három kulcsfontosságú paraméter az alkalmazás követelményeitől `spark.executor.instances` `spark.executor.cores`függően a, a és `spark.executor.memory`a. A végrehajtó egy Spark-alkalmazáshoz indított folyamat. A feldolgozó csomóponton fut, és feladata az alkalmazás feladatainak elvégzése. A végrehajtók és a végrehajtói méretek alapértelmezett számának kiszámítása a munkavégző csomópontok száma és a munkavégző csomópont mérete alapján történik. Ezeket az információkat a rendszer `spark-defaults.conf` a fürt fő csomópontjain tárolja.

A három konfigurációs paramétert a fürt szintjén lehet konfigurálni (a fürtön futó összes alkalmazás esetében), illetve az egyes alkalmazásokhoz is megadható.

### <a name="change-the-parameters-using-ambari-ui"></a>Paraméterek módosítása Ambari felhasználói felület használatával
1. A Ambari felhasználói felületén kattintson a **Spark**elemre, majd a **konfigurációk**elemre, és bontsa ki az **Egyéni Spark-alapértékek**elemet.

    ![Paraméterek beállítása a Ambari Custom használatával](./media/apache-spark-resource-manager/set-parameters-using-ambari.png "Paraméterek beállítása a Ambari Custom használatával")
2. Az alapértelmezett értékek kiválóan alkalmasak arra, hogy négy Spark-alkalmazás párhuzamosan fusson a fürtön. Ezeket az értékeket a felhasználói felületen módosíthatja, ahogy az alábbi képernyőképen is látható:

    ![Paraméterek beállítása a Ambari használatával](./media/apache-spark-resource-manager/set-executor-parameters.png "Paraméterek beállítása a Ambari használatával")

3. A konfigurációs módosítások mentéséhez kattintson a **Save (Mentés** ) gombra. A lap tetején a rendszer felszólítja az összes érintett szolgáltatás újraindítására. Kattintson a **Restart** (Újraindítás) elemre.

    ![Szolgáltatások újraindítása](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter notebookon futó alkalmazás paramétereinek módosítása
A Jupyter notebookon futó alkalmazások esetén a `%%configure` Magic használatával módosíthatja a konfigurációt. Ideális esetben az első kódrészlet futtatása előtt el kell végeznie az ilyen módosításokat az alkalmazás elején. Ezzel biztosíthatja, hogy a rendszer a konfigurációt a Livy-munkamenetre alkalmazza, amikor a rendszer létrehozza. Ha az alkalmazás egy későbbi szakaszában szeretné módosítani a konfigurációt, a `-f` paramétert kell használnia. Ezzel azonban az alkalmazás minden folyamata elvész.

A következő kódrészlet azt mutatja be, hogyan lehet módosítani egy Jupyter-ben futó alkalmazás konfigurációját.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

A konfigurációs paramétereket JSON-karakterláncként kell átadni, és a Magic után a következő sorban kell szerepelniük, ahogy az a példában látható oszlopban látható.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>A Spark-Submit használatával elküldött alkalmazások paramétereinek módosítása
A következő parancs egy példa arra, hogyan lehet módosítani a használatával `spark-submit`elküldött batch-alkalmazás konfigurációs paramétereit.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>A cURL használatával elküldött alkalmazások paramétereinek módosítása
A következő parancs egy példa arra, hogyan módosíthatja a cURL használatával elküldött batch-alkalmazás konfigurációs paramétereit.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>A paraméterek módosítása a Spark takarékossági kiszolgálón
A Spark takarékossági kiszolgáló JDBC/ODBC-hozzáférést biztosít egy Spark-fürthöz, és a Spark SQL-lekérdezések kiszolgálására szolgál. Eszközök, például Power BI, tabló stb. az ODBC protokoll használatával kommunikálhat a Spark takarékosság-kiszolgálóval a Spark SQL-lekérdezések Spark-alkalmazásként való végrehajtásához. Spark-fürt létrehozásakor a Spark takarékossági kiszolgáló két példánya indul el, egyet az egyes fő csomópontokon. Az egyes Spark-takarékossági kiszolgálók Spark-alkalmazásként láthatók a fonal felhasználói felületén.

A Spark takarékossági kiszolgáló a Spark dinamikus végrehajtó lefoglalását `spark.executor.instances` használja, ezért a nincs használatban. Ehelyett a Spark `spark.dynamicAllocation.minExecutors` -takarékos kiszolgáló és `spark.dynamicAllocation.maxExecutors` a végrehajtók számának megadására szolgál. A konfigurációs paraméterek `spark.executor.cores` `spark.executor.memory` a végrehajtó méretének módosítására szolgálnak. Ezeket a paramétereket a következő lépésekben látható módon módosíthatja:

* Bontsa ki a **speciális Spark-takarékosság-sparkconf** kategóriát, `spark.dynamicAllocation.minExecutors`és `spark.dynamicAllocation.maxExecutors`frissítse a `spark.executor.memory`paramétereket, és.

    A ![Spark takarékosság-kiszolgáló konfigurálása] A (./media/apache-spark-resource-manager/spark-thrift-server-1.png "Spark takarékosság-kiszolgáló konfigurálása")
* A paraméter `spark.executor.cores`frissítéséhez bontsa ki az **Egyéni Spark-takarékosság-sparkconf** kategóriát.

    A ![Spark takarékosság Server paraméter konfigurálása] A (./media/apache-spark-resource-manager/spark-thrift-server-2.png "Spark takarékosság Server paraméter konfigurálása")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>A Spark takarékosság-kiszolgáló illesztőprogram-memóriájának módosítása
A Spark-kiszolgáló illesztőprogram-memóriája a fő csomópont RAM-méretének 25%-ában van konfigurálva, ha a fő csomópont teljes RAM-mérete meghaladja a 14 GB-ot. A Ambari felhasználói felületén módosíthatja az illesztőprogram-memória konfigurációját, ahogy az alábbi képernyőképen is látható:

* A Ambari felhasználói felületén kattintson a **Spark**elemre, majd a **konfigurációk**elemre, bontsa ki a **speciális Spark-env**elemet, majd adja meg a **spark_thrift_cmd_opts**értékét.

    ![A Spark takarékosság Server RAM konfigurálása](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Spark-fürt erőforrásainak visszaigénylése
A Spark dinamikus kiosztása miatt a takarékossági kiszolgáló által felhasznált erőforrások csak a két alkalmazás-főkiszolgáló erőforrásai. Az erőforrások visszaigényléséhez le kell állítania a fürtön futó takarékossági kiszolgáló szolgáltatásait.

1. A Ambari felhasználói felületén, a bal oldali ablaktáblán kattintson a **Spark**elemre.
2. A következő lapon kattintson a **Spark takarékosság-kiszolgálók**elemre.

    A ![takarékos Kiszolgáló1 újraindítása] A (./media/apache-spark-resource-manager/restart-thrift-server-1.png "takarékos Kiszolgáló1 újraindítása")
3. Ekkor meg kell jelennie a két átjárócsomópontokkal, amelyen a Spark-takarékossági kiszolgáló fut. Kattintson a átjárócsomópontokkal egyikére.

    A ![gazdaságosság Kiszolgáló2 újraindítása] A (./media/apache-spark-resource-manager/restart-thrift-server-2.png "gazdaságosság Kiszolgáló2 újraindítása")
4. A következő oldalon az adott átjárócsomóponthoz futó összes szolgáltatás látható. A listában kattintson a Spark takarékosság-kiszolgáló melletti legördülő gombra, majd kattintson a **Leállítás**elemre.

    A ![gazdaságosság Server3 újraindítása] A (./media/apache-spark-resource-manager/restart-thrift-server-3.png "gazdaságosság Server3 újraindítása")
5. Ismételje meg ezeket a lépéseket a többi átjárócsomóponthoz is.

## <a name="restart-the-jupyter-service"></a>A Jupyter szolgáltatás újraindítása
Indítsa el a Ambari webes felhasználói felületét a cikk elején látható módon. A bal oldali navigációs panelen kattintson a **Jupyter**elemre, kattintson a **szolgáltatási műveletek**, majd **az összes újraindítása**elemre. Ezzel elindítja a Jupyter szolgáltatást az összes átjárócsomópontokkal.

![Jupyter újraindítása](./media/apache-spark-resource-manager/restart-jupyter.png "Jupyter újraindítása")

## <a name="monitor-resources"></a>Erőforrások megfigyelése
Indítsa el a fonal felhasználói felületét a cikk elején látható módon. A képernyő felső részén található fürt Metrikái táblában a **felhasznált memória** és a **memória összes** oszlopának értékét kell megnéznie. Ha a két érték be van zárva, előfordulhat, hogy nem áll rendelkezésre elegendő erőforrás a következő alkalmazás elindításához. Ugyanez vonatkozik a **használt virtuális mag** és a **virtuális mag összes** oszlopára is. Emellett a fő nézetben, ha egy alkalmazás **elfogadott** állapotban maradt, és nem a **futó** és a **sikertelen** állapotra vált, akkor ez azt is jelezheti, hogy nem kap elég erőforrást az induláshoz.

![Erőforrás-korlát](./media/apache-spark-resource-manager/resource-limit.png "Erőforrás-korlát")

## <a name="kill-running-applications"></a>Futó alkalmazások leölése
1. A fonal felhasználói felületén, a bal oldali panelen kattintson a **Futtatás**elemre. A futó alkalmazások listájából állapítsa meg a leölési alkalmazást, és kattintson az **azonosítóra**.

    ![App1 leölése](./media/apache-spark-resource-manager/kill-app1.png "App1 leölése")

2. Kattintson az **alkalmazás leölése** lehetőségre a jobb felső sarokban, majd kattintson **az OK**gombra.

    ![App2 leölése](./media/apache-spark-resource-manager/kill-app2.png "App2 leölése")

## <a name="see-also"></a>Lásd még
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Adatelemzők számára

* [Apache Spark a Machine Learningkal: A Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningkal: Az élelmiszer-vizsgálati eredmények előrejelzése a Spark in HDInsight használatával](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insight telemetria-adatelemzési szolgáltatásának használata a HDInsight-ben Apache Spark](apache-spark-analyze-application-insight-logs.md)
* [A Cafe on Azure HDInsight Spark használata elosztott mély tanuláshoz](apache-spark-deep-learning-caffe.md)

### <a name="for-apache-spark-developers"></a>Apache Spark fejlesztőknek

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek Apache Spark-fürtben HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)
