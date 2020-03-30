---
title: Az Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban
description: Ismerje meg, hogyan kezelheti a Spark-fürtök erőforrásait az Azure HDInsightban a jobb teljesítmény érdekében.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 3aab89f86dcd48328771cd0fda03d1c9de4bc2c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932110"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Az Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban

Ismerje meg, hogyan érheti el az olyan felületeket, mint az [Apache Ambari](https://ambari.apache.org/) UI, az [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ui és az [Apache Spark-fürthöz](https://spark.apache.org/) társított [Spark History Server,](./apache-azure-spark-history-server.md) és hogyan hangolhatja be a fürtkonfigurációt az optimális teljesítmény érdekében.

## <a name="open-the-spark-history-server"></a>A Spark-előzmények kiszolgálójának megnyitása

A Spark History Server a Spark-alkalmazások befejeződött és futó webes felhasználói felülete. Ez a Spark webes felhasználói felületének kiterjesztése. További információt a [Spark Előzmények kiszolgáló című témakörben talál.](./apache-azure-spark-history-server.md)

## <a name="open-the-yarn-ui"></a>A fonal felhasználói felületének megnyitása

A YARN felhasználói felület segítségével figyelheti a Spark-fürtön jelenleg futó alkalmazásokat.

1. Az [Azure Portalon](https://portal.azure.com/)nyissa meg a Spark-fürt. További információt a Fürtök listája és megjelenítése című témakörben [talál.](../hdinsight-administer-use-portal-linux.md#showClusters)

2. A **Fürt irányítópultjai között**válassza a **Fonal**lehetőséget. Amikor a rendszer kéri, adja meg a Spark-fürt rendszergazdai hitelesítő adatait.

    ![Launch YARN felhasználói felület](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Másik lehetőségként az Ambari felhasználói felületéről is elindíthatja a YARN felhasználói felületet. Az Ambari felhasználói felületén keresse meg a **YARN** > **gyorshivatkozások** > **aktív** > **erőforrás-kezelőjének felhasználói felületét.**

## <a name="optimize-clusters-for-spark-applications"></a>Fürtök optimalizálása Spark-alkalmazásokhoz

A Spark-konfigurációhoz az alkalmazás követelményeitől függően használható `spark.executor.instances` `spark.executor.cores`három `spark.executor.memory`fő paraméter a , és a. A Végrehajtó egy Spark-alkalmazáshoz indított folyamat. A munkavégző csomóponton fut, és felelős az alkalmazás feladatainak elvégzéséért. A végrehajtók alapértelmezett számát és az egyes fürtök végrehajtói méreteit a munkavégző csomópontok száma és a munkavégző csomópont mérete alapján számítja ki a rendszer. Ez az információ `spark-defaults.conf` a fürtfőcsomópontokon tárolódik.

A három konfigurációs paraméter konfigurálható a fürt szintjén (a fürtön futó összes alkalmazáshoz), vagy megadható minden egyes alkalmazáshoz is.

### <a name="change-the-parameters-using-ambari-ui"></a>A paraméterek módosítása az Ambari felhasználói felülethasználatával

1. Az Ambari felhasználói felületről keresse meg a **Spark2** > Configs Custom spark2-defaults (Az Ambari felhasználói felületét) a Spark2 Configs**Custom spark2-defaults (Konfigurációs konfigurációk egyéni szikra2-alapértelmezett) területre.****Configs** > 

    ![Paraméterek beállítása ambari egyéni használatával](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Paraméterek beállítása ambari egyéni használatával")

1. Az alapértelmezett értékek jó, hogy négy Spark-alkalmazások futnak egyidejűleg a fürtön. Ezeket az értékeket a felhasználói felületről módosíthatja, ahogy az az alábbi képernyőképen látható:

    ![Paraméterek beállítása ambari használatával](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Paraméterek beállítása ambari használatával")

1. A **konfigurációs** módosítások mentéséhez válassza a Mentés gombot. A lap tetején a rendszer felszólítja az összes érintett szolgáltatás újraindítására. Válassza **az Újraindítás**lehetőséget.

    ![Szolgáltatások újraindítása](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter notebookban futó alkalmazás paramétereinek módosítása

A Jupyter notebookban futó alkalmazások esetében `%%configure` a mágiával módosíthatja a konfigurációt. Ideális esetben az alkalmazás elején, az első kódcella futtatása előtt végre kell hajtania ezeket a módosításokat. Ezzel biztosítja, hogy a konfiguráció a Livy-munkamenetre, amikor létrejön, a rendszer alkalmazza. Ha módosítani szeretné a konfigurációt az alkalmazás egy későbbi `-f` szakaszában, a paramétert kell használnia. Ezzel azonban az alkalmazás minden előrehaladása elvész.

A következő kódrészlet bemutatja, hogyan módosíthatja a Jupyterben futó alkalmazások konfigurációját.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

A konfigurációs paramétereket JSON-karakterláncként kell átadni, és a mágia után a következő sorban kell lenniük, amint az a példaoszlopban látható.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>A spark-submit használatával benyújtott pályázat paramétereinek módosítása

A következő parancs egy példa arra, hogyan lehet módosítani a kötegelt alkalmazás konfigurációs paramétereit, amelyet a használatával `spark-submit`küld el.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>CURL használatával benyújtott alkalmazás paramétereinek módosítása

A következő parancs egy példa arra, hogyan lehet módosítani a konfigurációs paramétereket egy kötegelt alkalmazás, amely a cURL használatával küldött.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Ezeket a paramétereket a Spark Thrift Server kiszolgálón módosíthatja

A Spark Thrift Server JDBC/ODBC hozzáférést biztosít egy Spark-fürthöz, és a Spark SQL-lekérdezések kiszolgálására szolgál. Az olyan eszközök, mint a Power BI, a Tableau és így tovább, az ODBC protokoll használatával kommunikálnak a Spark Thrift Server rel a Spark SQL-lekérdezések Spark-alkalmazásként történő végrehajtásához. Spark-fürt létrehozásakor a Spark Thrift-kiszolgáló két példánya elindul, mindegyik fő csomóponton egy. Minden Spark Takarékosság-kiszolgáló Spark-alkalmazásként látható a YARN felhasználói felületén.

A Spark Thrift Server a Spark `spark.executor.instances` dinamikus végrehajtói allokációját használja, és így a nincs használva. Ehelyett a Spark Thrift Server a végrehajtók számát használja, `spark.dynamicAllocation.maxExecutors` és `spark.dynamicAllocation.minExecutors` megadja a végrehajtók számát. A konfigurációs `spark.executor.cores`paraméterek `spark.executor.memory` , és a végrehajtó méretének módosítására szolgálnak. Ezeket a paramétereket az alábbi lépéseknek látható módon módosíthatja:

* A paraméterek frissítéséhez bontsa ki az **Advanced spark2-thrift-sparkconf** `spark.dynamicAllocation.maxExecutors` `spark.dynamicAllocation.minExecutors`kategóriát.

    ![Spark takarékossági kiszolgáló konfigurálása](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Spark takarékossági kiszolgáló konfigurálása")

* A paraméterek frissítéséhez bontsa ki az **Egyéni szikra2-takarékosság-sparkconf** kategóriát `spark.executor.cores`. `spark.executor.memory`

    ![A Spark takarékossági kiszolgáló paraméterének konfigurálása](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "A Spark takarékossági kiszolgáló paraméterének konfigurálása")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>A Spark Thrift-kiszolgáló illesztőprogram-memóriájának módosítása

A Spark Thrift Server illesztőprogram-memóriája a főcsomópont RAM-méretének 25%-ára van konfigurálva, feltéve, hogy a fejcsomópont teljes RAM-mérete nagyobb, mint 14 GB. Az Ambari felhasználói felületsegítségével módosíthatja az illesztőprogram memóriakonfigurációját, ahogy az a következő képernyőképen látható:

Az Ambari felhasználói felületén keresse meg a **Spark2** > **Configs** > **Advanced spark2-env**. Ezután adja meg az **spark_thrift_cmd_opts**értékét.

## <a name="reclaim-spark-cluster-resources"></a>Spark-fürt erőforrásainak visszaszerzése

A Spark dinamikus lefoglalása miatt a takarékossági kiszolgáló által felhasznált erőforrások csak a két alkalmazásfőkiszolgáló erőforrásai. Az erőforrások visszaszerzéséhez le kell állítania a fürtön futó Takarékosság-kiszolgáló szolgáltatásokat.

1. Az Ambari felhasználói felületén a bal oldali ablaktáblában válassza a **Spark2**lehetőséget.

2. A következő lapon válassza a **Spark2 takarékossági kiszolgálók lehetőséget.**

    ![Takarékossági kiszolgáló újraindítása1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Takarékossági kiszolgáló újraindítása1")

3. Látnia kell azt a két headnode-t, amelyen a Spark2 thrift-kiszolgáló fut. Válassza ki az egyik headnodes.

    ![Takarékossági kiszolgáló újraindítása2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Takarékossági kiszolgáló újraindítása2")

4. A következő oldalon az adott csomóponton futó összes szolgáltatás szerepel. A listában válassza a Spark2 Thrift Server melletti legördülő gombot, majd a **Leállítás**gombot.

    ![Takarékossági kiszolgáló újraindítása3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Takarékossági kiszolgáló újraindítása3")
5. Ismételje meg ezeket a lépéseket a másik headnode is.

## <a name="restart-the-jupyter-service"></a>A Jupyter szolgáltatás újraindítása

Indítsa el az Ambari Web felhasználói felületét a cikk elején látható módon. A bal oldali navigációs ablakban válassza a **Jupyter**lehetőséget, válassza **a Szolgáltatási műveletek**lehetőséget, majd válassza az Összes **újraindítása**lehetőséget. Ez elindítja a Jupyter szolgáltatást az összes headnodes.This starts the Jupyter service on all the headnodes.

![Jupyter újraindítása](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Jupyter újraindítása")

## <a name="monitor-resources"></a>Erőforrások megfigyelése

Indítsa el a fonal felhasználói felületét a cikk elején látható módon. A fürtmetrikák táblázatban a képernyő tetején ellenőrizze a **Felhasznált memória** és a **Memória összesoszlop** értékeit. Ha a két érték közel van, előfordulhat, hogy nincs elég erőforrás a következő alkalmazás elindításához. Ugyanez vonatkozik a **használt virtuális magok** ra és a virtuális magok összes **oszlopra.** Is, a fő nézetben, ha van egy alkalmazás maradt **elfogadott** állapotban, és nem átmenet **a FUTÁS,** sem **sikertelen** állapotban, ez is azt jelzi, hogy nem kap elég erőforrást kezdeni.

![Erőforráskorlát](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Erőforráskorlát")

## <a name="kill-running-applications"></a>A futó alkalmazások megölése

1. A Fonal felhasználói felületén a bal oldali panelen válassza a **Futás**lehetőséget. A futó alkalmazások listájából határozza meg a leölendő alkalmazást, és válassza ki az **azonosítót.**

    ![Kill alkalmazás1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kill alkalmazás1")

2. Válassza a Jobb felső sarokban válassza a **Ölési alkalmazás lehetőséget,** majd kattintson az **OK gombra.**

    ![Kill alkalmazás2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Kill alkalmazás2")

## <a name="see-also"></a>Lásd még

* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Adatelemzőknek

* [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insight telemetriai adatok elemzése az Apache Spark használatával a HDInsightban](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Apache Spark-fejlesztőknek

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight eszközök beépülő moduljával távolról debugelje az Apache Spark alkalmazásokat](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsighton](apache-spark-zeppelin-notebook.md)
* [A Jupyter notebookhoz elérhető kernelek az Apache Spark-fürtHDInsighthoz](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)
