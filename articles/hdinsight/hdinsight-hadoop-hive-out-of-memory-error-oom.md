---
title: Hive-memória-hiba javítása az Azure HDInsightban
description: Javítsa ki a Hive memóriazavarát a HDInsightban. Az ügyfél forgatókönyve egy lekérdezés sok nagy táblában.
keywords: memóriazavar, OOM, Hive-beállítások
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: add55c29bb93d8dce9ad69bd9850a1db02ea5afe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687767"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Apache Hive memóriaből való kijavítása az Azure HDInsightban

Ismerje meg, hogyan javíthatja ki az Apache Hive memória (OOM) hibáját nagy táblák feldolgozásakor a Hive-memória beállításainak konfigurálásával.

## <a name="run-apache-hive-query-against-large-tables"></a>Apache Hive-lekérdezés futtatása nagy táblákon

Egy ügyfél hive-lekérdezést futtatott:

```sql
SELECT
    COUNT (T1.COLUMN1) as DisplayColumn1,
    …
    …
    ….
FROM
    TABLE1 T1,
    TABLE2 T2,
    TABLE3 T3,
    TABLE5 T4,
    TABLE6 T5,
    TABLE7 T6
where (T1.KEY1 = T2.KEY1….
    …
    …
```

A lekérdezés néhány árnyalata:

* A T1 egy tábla, a TABLE1 aliasa, amely sok STRING oszloptípussal rendelkezik.
* Más táblák nem olyan nagyok, de sok oszloppal rendelkeznek.
* Minden tábla csatlakozik egymáshoz, egyes esetekben több oszlopot TABLE1 és mások.

A Hive-lekérdezés 26 percet vett igénybe egy 24 csomópontos A3 HDInsight-fürtön. Az ügyfél a következő figyelmeztető üzeneteket vette észre:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Az Apache Tez végrehajtási motor használatával. Ugyanaz a lekérdezés 15 percig futott, majd a következő hibát dobta:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

A hiba egy nagyobb virtuális gép (például D12) használata esetén is megmarad.

## <a name="debug-the-out-of-memory-error"></a>Hibakeresés a memórián kívüli hiba

Támogató és mérnöki csapataink együtt találták meg az egyik problémát, amely a memórián kívüli hibát okozta, az [Apache JIRA-ban leírt ismert probléma](https://issues.apache.org/jira/browse/HIVE-8306)volt:

"Amikor a hive.auto.convert.join.join.noconditionaltask = igaz, ellenőrizzük a noconditionaltask.size-ot, és ha a táblázatméretek összege a térképillesztésben kisebb, mint a noconditionaltask.size, a terv térképillesztést hoz létre, a probléma ezzel az, hogy a számítás nem veszi figyelembe véve a különböző HashTable implementáció által bevezetett többletterhelést, ha a bemeneti méretek összege kisebb, mint egy kis különbözeti lekérdezés ekvivam által iratt költség, és az OOM-ot érinti."

A **hive.auto.convert.join.noconditionaltask** a hive-site.xml fájlban **true**értékre lett állítva:

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

Valószínű, hogy a térképcsatlakozás okozta a Java Heap Space memóriahibát. Amint azt a blogbejegyzést [Hadoop fonal memória beállításait HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), amikor Tez végrehajtási motor takarja a halomterület használt ténylegesen tartozik a Tez konténer. Tekintse meg az alábbi képet, amely leírja a Tez tároló memóriáját.

![Tez tárolómemória-diagram: A hive memóriahibával](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Ahogy a blogbejegyzés is sugallja, a következő két memóriabeállítás határozza meg a halommemória tárolómemóriáját: **hive.tez.container.size** és **hive.tez.java.opts**. Tapasztalataink szerint a memórián kívüli kivétel nem jelenti azt, hogy a tároló mérete túl kicsi. Ez azt jelenti, hogy a Java halomméret (hive.tez.java.opts) túl kicsi. Tehát, ha látod, elfogyott a memória, akkor próbálja meg növelni **hive.tez.java.opts**. Szükség esetén előfordulhat, hogy növelnie kell **a hive.tez.container.size .** A **java.opts** beállítás nak körülbelül 80% -nak kell lennie a **container.size.d.-nek.**

> [!NOTE]  
> A **hive.tez.java.opts** beállításnak mindig kisebbnek kell lennie, mint **a hive.tez.container.size**.

Mivel a D12-es gép 28 GB memóriával rendelkezik, úgy döntöttünk, hogy 10 GB (10240 MB) méretű tárolót használunk, és 80%-ot rendelünk a java.opts fájlhoz:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Az új beállításokkal a lekérdezés sikeresen futott 10 perc alatt.

## <a name="next-steps"></a>További lépések

OOM-hiba beszerzése nem feltétlenül jelenti azt, hogy a tároló mérete túl kicsi. Ehelyett úgy kell konfigurálnia a memóriabeállításokat, hogy a halommemória mérete növekedjön, és a tárolómemória méretének legalább 80%-a legyen. A Hive-lekérdezések optimalizálása az [Apache Hadoop optimalizálása a HDInsightban az Apache Hadoop-lekérdezések optimalizálása.](hdinsight-hadoop-optimize-hive-query.md)
