---
title: Működése OutOfMemoryError kivétel az Azure HDInsight Apache Spark
description: Működése OutOfMemoryError kivétel az Azure HDInsight Apache Spark
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/26/2019
ms.openlocfilehash: ac360312fdb3c4a238e6280872bc8c8b548e8544
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620986"
---
# <a name="scenario-outofmemoryerror-exception-for-apache-spark-in-azure-hdinsight"></a>Forgatókönyv: Működése OutOfMemoryError kivétel az Azure HDInsight Apache Spark

Ez a cikk a Apache Spark-összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A Apache Spark alkalmazás nem tudott kezelni egy működése OutOfMemoryError kezeletlen kivételt. A következőhöz hasonló hibaüzenet jelenhet meg:

```error
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

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

## <a name="cause"></a>Ok

A kivétel legvalószínűbb oka az, hogy nincs elég halom memória. A Spark-alkalmazás végrehajtók vagy illesztőprogramokként való futtatásakor elegendő Java Virtual Machines-(JVM-) halom memóriát igényel.

## <a name="resolution"></a>Megoldás:

1. Határozza meg a Spark-alkalmazás által kezelendő adatok maximális méretét. Becsülje meg a méretet a bemeneti adatok, a bemeneti adatok átalakításával előállított köztes adatok és a köztes adatok további átalakításával létrehozott kimeneti adatok maximális mérete alapján. Ha a kezdeti becslés nem elegendő, növelje a méretet kis mértékben, és ismételje meg a memória hibáit.

1. Győződjön meg arról, hogy a használni kívánt HDInsight-fürtnek elegendő memória-erőforrás áll a rendelkezésére, és elegendő maggal rendelkezik a Spark-alkalmazás elhelyezéséhez. Ennek meghatározásához a fürt YARN felhasználói felületén, a fürt metrikáit ismertető szakaszban tekintse meg a felhasznált memória és a teljes memória, valamint a használatban lévő virtuális magok és az összes virtuális mag értékét.

    ![a fonal alapmemóriájának nézete](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Állítsa be a következő Spark-konfigurációkat a megfelelő értékekre. Az alkalmazásra vonatkozó követelmények elosztása a fürt rendelkezésre álló erőforrásaival. Ezek az értékek nem haladhatják meg a rendelkezésre álló memória és magok 90%-át a fonal alapján megtekintve, és meg kell felelnie a Spark-alkalmazás minimális memória-követelményének is:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Az összes végrehajtó által használt memória összesen =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Az illesztőprogram által használt memória összesen =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* A [Spark memória-kezelési áttekintése](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [A Spark-alkalmazás hibakeresése a HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)-fürtökön.

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők. [@AzureSupport](https://twitter.com/azuresupport)

* Ha további segítségre van szüksége, támogatási kérést küldhet a Azure Portaltól [](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
