---
title: Egy kevés a memória az Azure HDInsight Hive javítása
description: Javítsa ki a kevés a memória, a HDInsight Hive. A forgatókönyv a lekérdezés számos nagy táblák esetében.
keywords: Hiba történt, OOM, Hive memóriabeállítások kívül
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: c0017d0b0255f5b585f9d8e6f6ec2f3a12752625
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55691487"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Kevés a memória az Azure HDInsight egy Apache Hive javítása

Ismerje meg, hogyan háríthatja el a kevés a memória (OOM) az Apache Hive nagy táblák Hive memória beállításainak konfigurálásával feldolgozásakor.

## <a name="run-apache-hive-query-against-large-tables"></a>Nagy táblák Apache Hive-lekérdezés futtatásához

Egy ügyfél futtatta a Hive-lekérdezést:

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

Néhány apró sajátosságaival a lekérdezés:

* A T1 TABLE1, amely karakterlánc típusú oszlophoz számos big Data típusú táblázatot egy alias.
* Más táblák nem, big Data típusú, de rendelkezik sok oszlop.
* Minden tábla csatlakozik egymáshoz, bizonyos esetekben több oszlopból álló TABLE1 és mások.

A Hive-lekérdezést a a3 méretű HDInsight-fürtön 24 csomópont 26 percet vett igénybe. Az ügyfél a következő figyelmeztető üzeneteket észrevette:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Az Apache Tez végrehajtómotor használatával. Ugyanazon lekérdezés 15 percig futott, és ezután a következő hibát okozta:

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

A hiba továbbra is nagyobb méretű virtuális gépet (például D12) használatakor.


## <a name="debug-the-out-of-memory-error"></a>A kevés a memória hibakeresése

A támogatás és a mérnöki munkacsoportok együtt található az a kevés a memória okozó problémák egyike volt egy [ismert probléma az Apache JIRA-hibajegy leírt](https://issues.apache.org/jira/browse/HIVE-8306):

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesn't take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

A **hive.auto.convert.join.noconditionaltask** a hive-site.xml fájl állították be **igaz**:

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

Valószínűleg térképen való csatlakozás lett okát a halommemória a Java terület, a memória hiba. A blogbejegyzésben leírtaknak megfelelően [HDInsight a Hadoop Yarn memória beállításainak](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), a Tez végrehajtómotor van használt halommemória felhasznált lemezterület ténylegesen a Tez-tároló tartozik. Az alábbi képen a Tez tároló memória ismertető témakörben talál.

![Tez tároló memória diagramja: Hive-elfogyott a memória](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

A blogbejegyzés javasol, a következő két memória beállításainak megadása a tároló memória halommemória: **hive.tez.container.size** és **hive.tez.java.opts**. Tapasztalataink a memóriából fakadó kivétel kívüli nem jelenti a tároló mérete túl kicsi. Azt jelenti, hogy a Java halommemória (hive.tez.java.opts) mérete túl kicsi. Ezért, amikor nincs elég memória látja, próbálja meg növelni **hive.tez.java.opts**. Szükség esetén szükség lehet növelni a **hive.tez.container.size**. A **java.opts** beállítás körülbelül 80 %-a legyen **container.size**.

> [!NOTE]  
> A beállítás **hive.tez.java.opts** mindig kisebbnek kell lennie **hive.tez.container.size**.
> 
> 

Mivel egy D12 gép 28GB memóriával rendelkezik, használja a tároló mérete 10 GB-os (10240MB), és a 80 %-os rendel java.opts döntöttünk:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Az új beállítások a lekérdezés sikeresen futtatta-e legfeljebb 10 perc alatt.

## <a name="next-steps"></a>További lépések

OOM hibaüzenet nem feltétlenül jelenti azt, a tároló mérete túl kicsi. Ehelyett konfigurálnia kell a memória beállításait, hogy a halommemória mérete nő, és a tároló memória mérete legalább 80 %-át. Hive-lekérdezések optimalizálása, lásd: [optimalizálása Apache Hive-lekérdezések a HDInsight Apache hadoop](hdinsight-hadoop-optimize-hive-query.md).
