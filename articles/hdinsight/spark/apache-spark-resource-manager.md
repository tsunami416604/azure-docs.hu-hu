---
title: "Az Azure HDInsight az Apache Spark-fürt erőforrásainak kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan használja a jobb teljesítmény érdekében az Azure HDInsight Spark-fürtjei erőforrásainak kezelése."
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: jgao
ms.openlocfilehash: b2208f0553ce62be054409a415723445733708d4
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Az Azure HDInsight az Apache Spark-fürt erőforrásainak kezelése 

Útmutató: a felületek, mint az Ambari felhasználói felület, YARN felhasználói felületen és a Spark-fürthöz társított Spark előzmények kiszolgáló eléréséhez, és a fürt konfigurálása az optimális teljesítmény hangolására.

**Előfeltételek:**

* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Nyissa meg az Ambari webes felhasználói felület
1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)** > **HDInsight Clusters** (HDInsight-fürtök).
2. A Spark-fürt, kattintson a **irányítópult**. Amikor a rendszer kéri, adja meg a rendszergazdai hitelesítő adatokat a Spark-fürtön.

    ![Indítsa el az Ambari](./media/apache-spark-resource-manager/hdinsight-launch-cluster-dashboard.png "erőforrás-kezelő indítása")
3. Ez kell az Ambari webes felhasználói felületén, nyissa meg a képernyőfelvételen látható módon.

    ![Ambari webes felhasználói felület](./media/apache-spark-resource-manager/ambari-web-ui.png "Ambari webes felhasználói felület")   

## <a name="open-the-spark-history-server"></a>Nyissa meg a Spark-előzmények kiszolgáló
1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton).
2. A fürt paneljén alatt **Gyorshivatkozások**, kattintson a **fürt irányítópult**. Az a **fürt irányítópult** panelen kattintson a **Spark előzmények Server**.

    ![Spark előzmények Server](./media/apache-spark-resource-manager/launch-history-server.png "Spark előzmények kiszolgáló")

    Amikor a rendszer kéri, adja meg a rendszergazdai hitelesítő adatokat a Spark-fürtön.

## <a name="open-the-yarn-ui"></a>Nyissa meg a Yarn felhasználói felületen
A Spark-fürtön a jelenleg futó alkalmazások figyeléséhez használhatja a YARN felhasználói felületen.

1. A fürt paneljén kattintson **fürt irányítópult**, és kattintson a **YARN**.

    ![Indítsa el a YARN felhasználói felületen](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Másik lehetőségként is indítja el a YARN felhasználói felületen a az Ambari felhasználói Felületéről. Indítsa el az Ambari felhasználói felületén, a fürt paneljén kattintson a **fürt irányítópult**, és kattintson a **HDInsight fürt irányítópult**. Az Ambari felhasználói felületén kattintson **YARN**, kattintson a **Gyorshivatkozások**, kattintson az aktív erőforrás-kezelő, majd **erőforrás-kezelő felhasználói felületén**.
   >
   >

## <a name="the-optimum-cluster-configuration-to-run-spark-applications"></a>A Spark-alkalmazások futtatására optimális fürtkonfiguráció
A három legfontosabb paraméterek alkalmazás követelményeitől függően a Spark-konfigurációhoz használható `spark.executor.instances`, `spark.executor.cores`, és `spark.executor.memory`. Egy művelettípus végrehajtója az a folyamat egy Spark-alkalmazáshoz elindítva. A munkavégző csomópont fut, és az alkalmazás feladatokat hajthat végre felelős. Az alapértelmezett számú végrehajtója és a végrehajtó használatos egyes fürtök a feldolgozó csomópontok és a munkavégző csomópont méretének száma alapján van kiszámítva. Ezeket az információkat tárolja `spark-defaults.conf` központi fürtcsomópontokon.

A három konfigurációs paraméterek beállítható, hogy a fürt szintjén (az a fürtön futó összes alkalmazást), vagy minden egyes alkalmazáshoz adható meg.

### <a name="change-the-parameters-using-ambari-ui"></a>Az Ambari felületen paraméterek módosítása
1. Az Ambari felhasználói felületén kattintson a **Spark**, kattintson a **Contigs**, majd bontsa ki a **egyéni spark-alapértelmezett**.

    ![A megadott paraméterek Ambari használatával](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Az alapértelmezett értékek megfelelőek 4 Spark-alkalmazások egyidejű futtatását a fürt hozzá. Ezeket az értékeket módosíthatja a felhasználói felületről, alább látható módon.

    ![A megadott paraméterek Ambari használatával](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Kattintson a **mentése** menteni a konfigurációs módosításait. A lap tetején kéri az érintett szolgáltatások újraindítására. Kattintson a **indítsa újra a**.

    ![Szolgáltatások újraindítása](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter notebook alkalmazás paramétereinek módosítása
Az alkalmazások a Jupyter notebook, használhatja a `%%configure` magic a konfigurációs módosításokat. Ideális esetben meg kell nyitnia a változások az alkalmazást, mielőtt újra lefuttatja az első kódcella elején. Ez biztosítja, hogy a konfiguráció alkalmazása a Livy munkamenethez, ha végrehajtásakor létrejön. Ha módosítani szeretné a konfigurációt az alkalmazásban egy későbbi időpontban szeretné, használnia kell a `-f` paraméter. Azonban, ennek során az alkalmazás az összes folyamatban lévő el fog veszni.

Az alábbi kódrészletben láthatja, Jupyter alkalmazás konfigurációjának módosítása.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Konfigurációs paraméterek a JSON karakterláncként kell átadnia, és későbbinek kell lennie a következő sorban a Bűvös példa oszlopában látható módon.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>A paraméterekkel rendelkező kérelem használata spark-nyújt módosítása
A következő parancs a példa bemutatja, hogyan módosíthatja a konfigurációs paraméterek használatával küldött kötegelt alkalmazások `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>A cURL használatával kérelem paramétereinek módosítása
A következő parancs a példa bemutatja, hogyan módosíthatja a konfigurációs paraméterek használata cURL használatával küldött kötegelt alkalmazáshoz.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="how-do-i-change-these-parameters-on-a-spark-thrift-server"></a>Hogyan változtathatom meg ezeket a paramétereket a Spark Thrift-kiszolgáló?
A Spark Thrift-kiszolgáló JDBC-/ ODBC hozzáférést biztosít a Spark-fürt és Spark SQL-lekérdezések használatával. Eszközök, például a Power BI-ban Tableau stb. ODBC protokoll segítségével kommunikálnak a Spark Thrift-kiszolgáló Spark SQL-lekérdezések végrehajtása a Spark-alkalmazásként. Spark-fürt létrehozásakor a Spark Thrift-kiszolgáló két példánya indulnak el, egy központi a csomópontokra. Minden egyes Spark Thrift-kiszolgáló a Spark-alkalmazásként a YARN felhasználói felületen látható.

A Spark Thrift-kiszolgáló használ a Spark dinamikus végrehajtó foglalási, így a `spark.executor.instances` nem használatos. Ehelyett használja a Spark Thrift-kiszolgáló `spark.dynamicAllocation.minExecutors` és `spark.dynamicAllocation.maxExecutors` a végrehajtó számának megadásához. A konfigurációs paraméterek `spark.executor.cores` és `spark.executor.memory` végrehajtó méretének szolgál. Ezek a paraméterek módosíthatja, ahogy az az alábbi lépéseket.

* Bontsa ki a **spark-thrift-sparkconf speciális** a paraméterek frissítéséhez kategória `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors`, és `spark.executor.memory`.

    ![A Spark thrift-kiszolgáló konfigurálása](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Bontsa ki a **spark-thrift-sparkconf egyéni** frissíteni a paraméter kategória `spark.executor.cores`.

    ![A Spark thrift-kiszolgáló konfigurálása](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="how-do-i-change-the-driver-memory-of-the-spark-thrift-server"></a>Hogyan változtathatom meg a illesztőprogram memóriát a Spark Thrift-kiszolgáló?
A Spark Thrift-kiszolgáló illesztőprogram memória van konfigurálva 25 %-át az átjárócsomópont RAM memória méretét, feltéve, az átjárócsomópont összesített RAM mérete 14GB-nál nagyobb. Az Ambari felhasználói felület segítségével módosíthatja az illesztőprogram memóriakövetelménye alább látható módon.

* Az Ambari felhasználói felületén kattintson a **Spark**, kattintson a **Configs**, bontsa ki a **spark-env speciális**, és adja meg a következő **spark_thrift_cmd_opts**.

    ![A Spark thrift-kiszolgáló RAM konfigurálása](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="i-do-not-use-bi-with-spark-cluster-how-do-i-take-the-resources-back"></a>BI nem Spark-fürt használata. Hogyan tudom vissza igénybe az erőforrásokat?
Spark dinamikus foglalási használjuk, mert az csak thrift-kiszolgáló által felhasznált erőforrások a két alkalmazás főkiszolgálók erőforrásokat. Ezeket az erőforrásokat visszaigénylésének le kell állítania a Thrift-kiszolgáló szolgáltatás fut a fürtön.

1. Az Ambari felhasználói felületén, a bal oldali ablaktáblán kattintson **Spark**.
2. A következő oldalon kattintson **Spark Thrift kiszolgálók**.

    ![Indítsa újra a thrift-kiszolgáló](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Meg kell jelennie a két headnodes, amelyen fut a Spark Thrift-kiszolgáló. Kattintson a headnodes.

    ![Indítsa újra a thrift-kiszolgáló](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. A következő lap felsorolja az adott headnode futó összes szolgáltatás. A listában kattintson a legördítő gomb melletti Spark Thrift-kiszolgáló, majd **leállítása**.

    ![Indítsa újra a thrift-kiszolgáló](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Ismételje meg ezeket a lépéseket a más headnode, valamint a.

## <a name="my-jupyter-notebooks-are-not-running-as-expected-how-can-i-restart-the-service"></a>A Jupyter notebookok nem elvárt módon futnak. Hogyan újraindíthatja a szolgáltatást?
Indítsa el az Ambari webes felhasználói felületén, ahogy fent látható. Kattintson a bal oldali navigációs ablak **Jupyter**, kattintson a **szolgáltatás műveletek**, és kattintson a **indítsa újra az összes**. A Jupyter szolgáltatás elindítja az összes headnodes.

    ![Restart Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Restart Jupyter")

## <a name="how-do-i-know-if-i-am-running-out-of-resources"></a>Hogyan állapítható meg, hogy ha erőforrások fut-e?
A Yarn felhasználói felületének indítása, a fentiek szerint. Fürt metrikáinak tábla a képernyő fölött, ellenőrizze az értékeket **használt memória** és **memória teljes** oszlopok. Ha a 2 érték rendkívül szoros, a nem feltétlenül elegendő erőforrás a következő alkalmazás indításához. Ugyanez érvényes a **VCores használt** és **VCores összesen** oszlopok. Is, a fő nézetben, ha egy alkalmazás tartózkodott a **elfogadott** állapotát, és nem változik a **futtató** sem **sikertelen** állapotba kerül, ennek oka is lehet utal, hogy nem sikerül elindítani erőforrásokkal.

    ![Resource Limit](./media/apache-spark-resource-manager/resource-limit.png "Resource Limit")

## <a name="how-do-i-kill-a-running-application-to-free-up-resource"></a>Hogyan kill a szabadítson fel erőforrás egy futó alkalmazást?
1. A Yarn felhasználói felületen, a bal oldali panelen kattintson a **futtató**. Határozza meg az alkalmazás szakítva, és kattintson a futó alkalmazások listájában, a **azonosító**.

    ![Az App1 Kill](./media/apache-spark-resource-manager/kill-app1.png "App1 leállítása")

2. Kattintson a **Kill alkalmazás** jobb felső sarokban, majd kattintson **OK**.

    ![Kill App2](./media/apache-spark-resource-manager/kill-app2.png "App2 leállítása")

## <a name="see-also"></a>Lásd még:
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Az adatok elemző

* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insights telemetriai adatainak elemzése a Spark on HDInsight használatával](apache-spark-analyze-application-insight-logs.md)
* [Az Azure HDInsight Spark Caffe elosztott mély tanulási használata](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>A Spark-fejlesztőknek

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Spark Streaming: A Spark on HDInsight használata valós idejű streamelési alkalmazások összeállítására](apache-spark-eventhub-streaming.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)
