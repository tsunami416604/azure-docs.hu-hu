---
title: "Hárítsa el a kevés a memória az Azure HDInsight Hive |} Microsoft Docs"
description: "Javítsa ki a kevés a memória hdinsight Hive. A forgatókönyv az a lekérdezés sok nagy táblák között."
keywords: "Hiba történt, a memória, Hive memóriabeállításait kívül"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 7bce3dff-9825-4fa0-a568-c52a9f7d1dad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/17/2017
ms.author: jgao
ms.openlocfilehash: da1247070ade11f78b505524f5e970e18eb16d10
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="fix-a-hive-out-of-memory-error-in-azure-hdinsight"></a>Hárítsa el a kevés a memória az Azure HDInsight Hive

Kevés a memória a struktúra kijavításához folyamat nagy táblák konfigurálásával memóriabeállításokat struktúra.

## <a name="run-hive-query-against-large-tables"></a>Nagy táblák Hive-lekérdezések futtatásához

Az ügyfél Hive-lekérdezések futtatása:

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

A lekérdezés néhány apró:

* A T1 nagy táblához, TABLE1, amelynek karakterlánc típusú oszlopokat rengeteg alias.
* Más táblák, amely nem nagy, de rendelkezik sok oszlopot.
* Minden olyan táblát csatlakozik egymáshoz, bizonyos esetekben több oszlopból álló TABLE1 és mások számára.

A Hive-lekérdezés befejezéséhez 24 csomóponton a3 méretű HDInsight-fürt 26 percet vett igénybe. Az ügyfél a következő figyelmeztető üzenetek első fellépése:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

A Tez végrehajtómotor használatával. Ugyanabban a lekérdezésben 15 percig futott, és ezután váltott ki. a következő hiba:

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

A hiba továbbra is, a nagyobb virtuális gépek (például D12) használatakor.


## <a name="debug-the-out-of-memory-error"></a>A kevés a memória hibakeresése

A támogatási és a mérnöki munkacsoportok együtt találta az egyik a kevés a memória, amely egy [ismert probléma az Apache JIRA ismertetett](https://issues.apache.org/jira/browse/HIVE-8306):

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

A **hive.auto.convert.join.noconditionaltask** a hive-site.xml fájl értékre volt állítva **igaz**:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
              Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
              If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
              specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
      </property>

Valószínűleg térkép illesztési okozta a Java halommemória terület a memóriahiba. A blogbejegyzésben leírtaknak [hdinsight Hadoop Yarn memóriabeállításait](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), a Tez végrehajtó motorja a halommemória használt használt lemezterület ténylegesen a Tez tárolóhoz tartozik. Tekintse meg az alábbi képen a Tez tároló memória leíró.

![Tez tároló memória diagramja: kevés a memória struktúra](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

A blogbejegyzés javasol, a következő két memória beállítások megadása a tároló memóriát a halommemória: **hive.tez.container.size** és **hive.tez.java.opts**. A felhasználói élmény, a memória kivétel kívüli nem jelenti a tároló mérete túl kicsi. Az azt jelenti, hogy a Java halommemória mérete (hive.tez.java.opts) értéke túl kicsi. Így kevés a memória jelenik meg, amikor megpróbálja növelése **hive.tez.java.opts**. Szükség esetén lehetséges, hogy növelje **hive.tez.container.size**. A **java.opts** beállítás körülbelül 80 %-át kell **container.size**.

> [!NOTE]
> A beállítás **hive.tez.java.opts** mindig kisebbnek kell lennie **hive.tez.container.size**.
> 
> 

Mivel a D12 gépek 28GB memória, a tároló mérete 10 GB-os (10240MB) használja, és 80 % rendel java.opts döntöttünk:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Az új beállításokkal a lekérdezés sikeresen futtatta-e a 10 perc múlva.

## <a name="next-steps"></a>Következő lépések

A memória a hiba nem feltétlenül jelenti azt a tároló mérete túl kicsi. Ehelyett az memória beállításokat kell konfigurálnia, hogy a halommemória mérete nő, és a tároló memória mérete legalább 80 %-át. Hive-lekérdezések optimalizálása, lásd: [a hdinsight Hadoop Hive optimalizálása lekérdezések](hdinsight-hadoop-optimize-hive-query.md).