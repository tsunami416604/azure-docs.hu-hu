---
title: A Spark on Azure HDInsight hibaelhárítása
description: Az Apache Spark és az Azure HDInsight használatához kapcsolatos gyakori kérdésekre adott válaszok.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: aad35aa7a958e8bdaf1479d1ffbbad5bf213d46a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339244"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Hibaelhárítás az Apache Spark az Azure HDInsight segítségével

Ismerje meg a leggyakoribb problémák és azok megoldásait az használatakor [Apache Spark](https://spark.apache.org/) hasznos adatot [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Egy Apache Spark-alkalmazás konfigurálása az Apache Ambari-fürtökön

### <a name="resolution-steps"></a>A megoldás lépései

A Spark konfigurációs értékek hangolásával Apache Spark-alkalmazás OutofMemoryError kivételt elkerülése érdekében. A következő lépésekkel alapértelmezett Spark konfigurációs értékeket az Azure HDInsight: 

1. Fürtök listájából válassza ki **Spark2**.

    ![Válassza ki a fürt listából](./media/apache-troubleshoot-spark/update-config-1.png)

2. Válassza ki a **Configs** fülre.

    ![Válassza ki a Configs lap](./media/apache-troubleshoot-spark/update-config-2.png)

3. A konfigurációk, jelölje ki **egyéni-spark2 – alapértelmezett**.

    ![Válassza ki az egyéni – a spark-alapértelmezései](./media/apache-troubleshoot-spark/update-config-3.png)

4. Keresse meg a beállítás, amely kell beállítani, mint például **spark.executor.memory**. Ebben az esetben értékét **4608m** túl magas.

    ![Válassza ki a spark.executor.memory mezőt](./media/apache-troubleshoot-spark/update-config-4.png)

5. Állítsa az értékét az ajánlott beállítás. Az érték **2048m** állítja ezt a beállítást javasoljuk.

    ![Módosítsa az értéket a 2048m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Mentse az értéket, és mentse a konfigurációt. Válassza az eszköztár **mentése**.

    ![A beállítás és konfiguráció mentése](./media/apache-troubleshoot-spark/update-config-6a.png)

    Ha a figyelmet igénylő konfigurációk értesítést kap. Jegyezze fel az elemeket, és válassza ki **folytatja ennek ellenére**. 

    ![Válassza ki folytatja ennek ellenére](./media/apache-troubleshoot-spark/update-config-6b.png)

    Írjon megjegyzést a konfigurációs változásokról, és válassza ki **mentése**.

    ![Adja meg a végzett módosítások kapcsolatos megjegyzés](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Amikor egy konfigurációs mentette, a rendszer kéri, indítsa újra a szolgáltatást. Válassza ki **indítsa újra a**.

    ![Válassza az újraindítás](./media/apache-troubleshoot-spark/update-config-7a.png)

    Erősítse meg az újraindítást.

    ![Indítsa újra az összes jóváhagyás kijelölése](./media/apache-troubleshoot-spark/update-config-7b.png)

    A futó folyamatok tekintheti meg.

    ![Tekintse át a futó folyamatok](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Konfigurációkat adhat hozzá. Konfigurációk listájából válassza ki **egyéni-spark2 – alapértelmezett**, majd válassza ki **tulajdonság hozzáadása**.

    ![Válassza ki a tulajdonság hozzáadása](./media/apache-troubleshoot-spark/update-config-8.png)

9. Adjon meg egy új tulajdonság. Egyetlen tulajdonság meghatározhatja az egyes beállítások, például az adattípus párbeszédpanel használatával. Vagy több tulajdonságok adhatók soronként egy definíció használatával. 

    Ebben a példában a **spark.driver.memory** tulajdonság értékkel van definiálva **4g**.

    ![Adja meg az új tulajdonság](./media/apache-troubleshoot-spark/update-config-9.png)

10. A konfiguráció mentéséhez, és indítsa újra a 6 és 7 lépésben ismertetett módon.

Ezek a változások fürtre kiterjedő, de a Spark-feladat elküldésekor felülbírálható.

### <a name="additional-reading"></a>További olvasnivaló

[A HDInsight-fürtökön az Apache Spark-feladat küldése](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Egy Apache Spark-alkalmazás konfigurálása a fürtök Jupyter notebook használatával

### <a name="resolution-steps"></a>A megoldás lépései

1. Határozza meg, melyik Spark konfigurációk kell beállítani, és milyen értékeket lásd: Mi az Apache Spark-alkalmazás OutofMemoryError kivétel okozza.

2. Az első olyan cellára, a Jupyter notebookot az után a **%% konfigurálása** irányelv, érvényes JSON formátumban adja meg a Spark-konfigurációkat. A tényleges értékek módosítása szükséges:

    ![Konfiguráció hozzáadása](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>További olvasnivaló

[A HDInsight-fürtökön az Apache Spark-feladat küldése](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Egy Apache Spark-alkalmazás konfigurálása fürtökön Apache Livy használatával

### <a name="resolution-steps"></a>A megoldás lépései

1. Határozza meg, melyik Spark konfigurációk kell beállítani, és milyen értékeket lásd: Mi az Apache Spark-alkalmazás OutofMemoryError kivétel okozza. 

2. Küldje el a Livy-, Spark-alkalmazás REST-ügyfél, például a cURL használatával. Használjon az alábbihoz hasonló parancsot. A tényleges értékek módosítása szükséges:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>További olvasnivaló

[A HDInsight-fürtökön az Apache Spark-feladat küldése](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hogyan konfigurálhatom egy Apache Spark, az alkalmazás a spark-submit fürtökön?

### <a name="resolution-steps"></a>A megoldás lépései

1. Határozza meg, melyik Spark konfigurációk kell beállítani, és milyen értékeket lásd: Mi az Apache Spark-alkalmazás OutofMemoryError kivétel okozza.

2. Indítsa el a spark-shell az alábbihoz hasonló parancs használatával. Szükség szerint módosítsa a konfiguráció a tényleges érték: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>További olvasnivaló

[A HDInsight-fürtökön az Apache Spark-feladat küldése](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-an-apache-spark-application-outofmemoryerror-exception"></a>Az Apache Spark-alkalmazás OutofMemoryError kivételt mi okozza?

### <a name="detailed-description"></a>Részletes leírás

A Spark-alkalmazás meghibásodik, a nem kezelt kivételek a következő típusú:

```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Lehetséges ok

A kivétel legvalószínűbb oka az, hogy a nem elegendő halommemória a Java virtuális gépekhez (JVMs) van lefoglalva. Ezek JVMs vannak vagy illesztőprogramként indít el illesztőprogramokat a Spark-alkalmazás részeként. 

### <a name="resolution-steps"></a>A megoldás lépései

1. Határozza meg a maximális méretet, az adatok a Spark alkalmazás kezeli. A bemeneti adatok az előállított a bemeneti adatok átalakításával keletkező köztes adatok és a kimeneti adatokat, amelyek jön létre, amikor az alkalmazás további formálja át a köztes adatok maximális mérete alapján egy találgatásos teheti meg. Ez a folyamat az ismétlődő lehet, ha nem választja ki egy hivatalos kezdeti becslésre. 

2. Győződjön meg arról, hogy a HDInsight-fürtöt fog használni, memória és a Spark-alkalmazás maggal elegendő erőforrással rendelkezik-e. Megadhatja, hogy ez a fürt metrikáit ismertető szakaszban a YARN felhasználói felületén, az értékek megtekintésével **felhasznált memória** vs. **Teljes memória**, és **használatban lévő virtuális magok** vs. **Virtuális magok összesen**.

3. Állítsa be a következő Spark konfigurációk megfelelő értékekre, amely nem haladhatja meg a rendelkezésre álló memória és magok 90 %-át. Az értékeket is a Spark-alkalmazás memóriakövetelményei belül kell lennie: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    A teljes memória, összes végrehajtóval által használt számítása: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Az illesztőprogram által használt a teljes memória számítása:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>További olvasnivaló

- [Az Apache Spark memória – áttekintés](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Egy HDInsight-fürtön egy Apache Spark-alkalmazás hibakeresése](https://web.archive.org/web/20190112152909/ https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Lásd még:
[Hibaelhárítás az Azure HDInsight segítségével](../../hdinsight/hdinsight-troubleshoot-guide.md)
