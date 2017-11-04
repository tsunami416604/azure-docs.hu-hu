---
title: "Hibaelhárítás Spark on Azure HDInsight segítségével |} Microsoft Docs"
description: "Az Apache Spark és az Azure HDInsight kapcsolatos gyakori kérdésekre adott válaszok."
keywords: "Az Azure HDInsight Spark, gyakran ismételt kérdések, hibaelhárítási útmutató, gyakori problémákat, Alkalmazáskonfiguráció, Ambari"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: 25D89586-DE5B-4268-B5D5-CC2CE12207ED
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: b75aca7d9f78cb425099cc33034b44a80d7b6b81
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-spark-by-using-azure-hdinsight"></a>Hibaelhárítás Spark on Azure HDInsight segítségével

A legfőbb problémákat és azok megoldásait ismerje meg az Apache Ambari az Apache Spark Payload van jelen használatakor.

## <a name="how-do-i-configure-a-spark-application-by-using-ambari-on-clusters"></a>Hogyan konfigurálhatom a Spark-alkalmazások fürtök az Ambari használatával?

### <a name="resolution-steps"></a>Megoldási lépések

A konfigurációs értékeket az eljárás végrehajtásához a Hdinsightban korábban beállított. Határozza meg, melyik Spark konfigurációk kell állítani, és milyen értékeket, olvassa el [mi okozza a Spark OutofMemoryError Alkalmazáskivétel](#what-causes-a-spark-application-outofmemoryerror-exception). 

1. A fürtök, jelölje ki **Spark2**.

    ![Válassza ki a fürtöt a listából](./media/apache-troubleshoot-spark/update-config-1.png)

2. Válassza ki a **Configs** fülre.

    ![Válassza ki a Configs lap](./media/apache-troubleshoot-spark/update-config-2.png)

3. Válassza a konfigurációk közül, **egyéni-spark2-alapértelmezett**.

    ![Válassza ki az egyéni-spark-alapértelmezései](./media/apache-troubleshoot-spark/update-config-3.png)

4. Keresse meg a beállítás, amely át kell állítania, mint például **spark.executor.memory**. Ebben az esetben értékének **4608m** túl nagy.

    ![Válassza ki a spark.executor.memory mező](./media/apache-troubleshoot-spark/update-config-4.png)

5. Adja meg az értéket az ajánlott beállítás. Az érték **2048m** ezt a beállítást ajánlott.

    ![2048 m értékének módosítása](./media/apache-troubleshoot-spark/update-config-5.png)

6. Mentse az értéket, és mentse a konfigurációt. Az eszköztáron válassza **mentése**.

    ![A beállítás és konfiguráció mentése](./media/apache-troubleshoot-spark/update-config-6a.png)

    Ha a konfigurációkat kezeléséről értesítést kap. Tekintse meg az elemeket, és adja **mégis folytatni**. 

    ![Válassza ki mégis folytatni](./media/apache-troubleshoot-spark/update-config-6b.png)

    Írjon megjegyzést a konfigurációs módosításokat, majd válassza ki **mentése**.

    ![Adja meg a végrehajtott változtatásokat Megjegyzés](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Amikor egy konfigurációs mentésekor kéri indítsa újra a szolgáltatást. Válassza ki **indítsa újra a**.

    ![Válassza ki az újraindítást](./media/apache-troubleshoot-spark/update-config-7a.png)

    Erősítse meg az újraindítást.

    ![Válassza ki erősítse meg, indítsa újra az összes](./media/apache-troubleshoot-spark/update-config-7b.png)

    Futó folyamatok tekintheti meg.

    ![Tekintse át a futó folyamatok](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Konfigurációk is hozzáadhat. Válassza a konfigurációk közül, **egyéni-spark2-alapértelmezett**, majd válassza ki **tulajdonság hozzáadása**.

    ![Válassza ki a tulajdonság hozzáadása](./media/apache-troubleshoot-spark/update-config-8.png)

9. Adja meg az új tulajdonság. Egy-egy tulajdonság az egyes beállítások, például az adatok típusát egy párbeszédpanel segítségével adhat meg. Vagy több tulajdonságok adhatók soronként egy definíciót használatával. 

    Ebben a példában a **spark.driver.memory** tulajdonságot definiálták, értékű **4g**.

    ![Új tulajdonság megadása](./media/apache-troubleshoot-spark/update-config-9.png)

10. A konfiguráció mentéséhez, majd indítsa újra a szolgáltatást, 6 és 7 lépésben leírtak szerint.

Ezek a változások fürt kiterjedő, de a Spark feladat elküldése felülbírálható.

### <a name="additional-reading"></a>További olvasnivaló

[Spark feladat elküldése a HDInsight-fürtökön](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hogyan konfigurálhatom a Spark-alkalmazások fürtökön Jupyter notebook használatával?

### <a name="resolution-steps"></a>Megoldási lépések

1. Határozza meg, melyik Spark konfigurációk kell állítani, és milyen értékeket, olvassa el [mi okozza a Spark OutofMemoryError Alkalmazáskivétel](#what-causes-a-spark-application-outofmemoryerror-exception).

2. A Jupyter notebook első cellájában után a **%% konfigurálása** irányelv, érvényes JSON formátumban adja meg a Spark-konfigurációk. A tényleges értékek módosítása szükséges:

    ![A konfiguráció hozzáadása](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>További olvasnivaló

[Spark feladat elküldése a HDInsight-fürtökön](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-livy-on-clusters"></a>Hogyan konfigurálhatom a Spark-alkalmazások fürtökön Livy használatával?

### <a name="resolution-steps"></a>Megoldási lépések

1. Határozza meg, melyik Spark konfigurációk kell állítani, és milyen értékeket, olvassa el [mi okozza a Spark OutofMemoryError Alkalmazáskivétel](#what-causes-a-spark-application-outofmemoryerror-exception). 

2. Küldje el a külső alkalmazás Livy a REST-ügyfelek, például cURL használatával. Használjon a következőhöz hasonló parancsot. A tényleges értékek módosítása szükséges:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>További olvasnivaló

[Spark feladat elküldése a HDInsight-fürtökön](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters"></a>Hogyan konfigurálhatók a alkalmazás használatával spark-elküldeni egy Spark-fürtökön?

### <a name="resolution-steps"></a>Megoldási lépések

1. Határozza meg, melyik Spark konfigurációk kell állítani, és milyen értékeket, olvassa el [mi okozza a Spark OutofMemoryError Alkalmazáskivétel](#what-causes-a-spark-application-outofmemoryerror-exception).

2. A következőhöz hasonló parancs használatával indítsa el a spark-rendszerhéjat. A tényleges érték a konfigurációk módosítása, szükség esetén: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>További olvasnivaló

[Spark feladat elküldése a HDInsight-fürtökön](https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-a-spark-application-outofmemoryerror-exception"></a>OutofMemoryError Alkalmazáskivétel mi okozza a Spark?

### <a name="detailed-description"></a>Részletes leírás

A Spark-alkalmazások nem sikerül, a következő típusú nem kezelt kivételekkel:

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

Ennek a kivételnek ennek legvalószínűbb oka, hogy nincs elég memória a halommemória a Java virtuális gépek (JVMs) van lefoglalva. Ezek JVMs végrehajtója, vagy az illesztőprogramok végrehajtása a Spark alkalmazás részeként. 

### <a name="resolution-steps"></a>Megoldási lépések

1. Határozza meg a maximális méretét az adatok a Spark alkalmazás kezeli. Egy becslés, a bemeneti adatok, a köztes adatokat a bemeneti adatok átalakítása által létrehozott és a kimeneti adatok, ha az alkalmazás további van a köztes adatok átalakítása előállított maximális mérete alapján végezheti el. Ez a folyamat az ismétlődő lehet, ha egy kezdeti formális becslés nem hajtható végre. 

2. Győződjön meg arról, hogy a HDInsight-fürt, amely használni fogja memória és a külső alkalmazás befogadásához magok elegendő erőforrással rendelkezik-e. A YARN felhasználói felületen az értékek a fürt metrikák szakasza megtekintésével segítségével meghatározhatja a **használt memória** vs. **Memória összesen**, és **VCores használt** vs. **VCores összesen**.

3. A következő Spark konfigurációt állítson be megfelelő értékekre, amely nem haladhatja meg a rendelkezésre álló memória és -magok 90 %-át. Az értékek is a Spark-alkalmazások memóriaigényének belül kell lennie: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Az összes végrehajtója teljes memóriahasználatát kiszámítása: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   A kiszámítása a teljes memória méretét az illesztőprogram használják:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>További olvasnivaló

- [Spark memória – áttekintés](http://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [A Spark on HDInsight-fürt alkalmazások hibakeresése](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Lásd még:
[Hibaelhárítás az Azure HDInsight segítségével](../../hdinsight/hdinsight-troubleshoot-guide.md)

