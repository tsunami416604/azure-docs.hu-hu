---
title: Külső metaadat-tárolók – Azure HDInsight használata
description: Külső metaadat-tárolók használata a HDInsight-fürtökkel.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.author: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: a2c992a47e40a4f8764f5950c65bb90f1cd9e066
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045143"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Az Azure HDInsight külső metaadat-tárolók használata

A Hive-metaadattár a HDInsight Hadoop-architektúra alapvető része. A metaadattár a központi séma tárház, amely más big data-hozzáférés eszközök, például a Spark, interaktív lekérdezések (LLAP), Presto vagy Pig által használható. HDInsight Hive-metaadattár használja az Azure SQL Database.

![HDInsight Hive-metaadatok Store architektúra](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Beállíthat egy metaadattár a HDInsight-fürtök számára két módja van:

* [Alapértelmezett metaadattárat](#default-metastore)
* [Egyéni-metaadattár](#custom-metastore)

## <a name="default-metastore"></a>Alapértelmezett metaadattárat

Alapértelmezés szerint a HDInsight egy metaadattár minden fürt típusú látja. Helyette megadhat egy egyéni metaadattár. Az alapértelmezett metaadattárat az alábbiakat tartalmazza:
- További költségek nélkül. HDInsight látja el egy metaadattár minden fürt típussal, minden további költség nélkül.
- Egyes alapértelmezett metaadattárat a fürt életciklusa részét képezi. Ha töröl egy fürtöt, hogy metaadattár és metaadatok is törlődik.
- Az alapértelmezett metaadattárat nem osztható meg más fürtökkel.
- Az alapértelmezett metaadattárat használja az alapszintű Azure SQL DB, amely esetében a 5 DTU (adatbázis-tranzakciós egységek).
Az alapértelmezett metaadattárat jellemzően viszonylag egyszerű olyan számítási feladatok, amelyek több fürt nem igényelnek, és nem kell megőrzi a fürt életciklusa túli metaadatok szolgál.


## <a name="custom-metastore"></a>Egyéni-metaadattár

HDInsight emellett támogatja az egyéni metaadattárakat, javasolt az éles fürtökben:
- A saját Azure SQL Database a metaadattár adja meg.
- A metaadattár életciklusának nem kötődik fürtök életciklusa úgy hozhat létre és fürtök törlése az metaadatok elvesztése nélkül. Metaadatokat, például a Hive-sémák törli, majd hozza létre újból a HDInsight-fürt után is megmaradnak.
- Egy egyéni metaadattár több fürtöt és fürttípusok csatolása a metaadattár teszi lehetővé. Ha például egyetlen metaadattár keresztül megoszthatók interaktív lekérdezés, a Hive és a Spark-fürtök a HDInsight.
- A metaadattár (Azure SQL Database) alapján úgy dönt, a teljesítményszint járó költségeket kell fizetnie.
- Igény szerint skálázhatja fel a metaadattár.


![HDInsight Hive-metaadatok Store használati eset](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>Egy egyéni metaadattár válassza ki a fürt létrehozása során

Egy korábban létrehozott Azure SQL Database a fürt a fürt létrehozása során mutathat, vagy konfigurálhatja az SQL Database, a fürt létrehozása után. Ez a beállítás meg van adva a tároló > Metaadattár beállításai létrehozásakor egy új Hadoop, Spark vagy interaktív Hive-fürt Azure Portal használatával.

![HDInsight Hive-metaadatok Store az Azure Portalon](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Is hozzáadhat további fürtök egy egyéni metaadattár az Azure portálon vagy az Ambari-konfigurációk (Hive-> Speciális)

![HDInsight Hive-metaadatok Store Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Hive-metaadattár ajánlott eljárások

Az alábbiakban néhány általános HDInsight Hive metaadattár ajánlott eljárásokat:

- Ez segít a külön számítási erőforrások (a futó fürt) és a metaadatok (a metaadattár tárolva) használja, amikor csak lehetséges, egy egyéni metaadattár.
- Indítsa el az S2 szint, amely 50 DTU és 250 GB-os tárhelyet biztosít. Ha a szűk keresztmetszet, az adatbázis is méretezhető.
- Győződjön meg arról, hogy a létrehozott egy HDInsight-fürt verziója nem megosztott HDInsight-fürt különböző verzióin átívelő metaadattár. Különböző Hive-verziók különböző sémákkal használja. Például egy metaadattár nem oszthatja meg a Hive-1.2-es és a Hive 2.1-fürtökkel.
- Rendszeresen készítsen biztonsági másolatot az egyéni metaadattár.
- A metaadattár és a HDInsight-fürt tartsa ugyanabban a régióban.
- A metaadattár a teljesítmény és rendelkezésre állás az Azure SQL-adatbázis figyelési eszközök, például az Azure portal vagy az Azure Log Analytics használatával figyelheti.

## <a name="oozie-metastore"></a>Oozie-Metaadattár

Az Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli.  Az Oozie támogatja a Hadoop-feladatok Apache MapReduce, a Pig, Hive és mások.  Az Oozie-metaadattár és befejezett munkafolyamatok adatainak tárolására használja. Az Oozie használata a teljesítmény növelése érdekében használhatja az Azure SQL Database, egy egyéni metaadattár. A metaadattár a fürt törlése után is megadhatja az Oozie feladat adatokhoz való hozzáférést.

Az Azure SQL Database-Oozie-metaadattár létrehozásával kapcsolatos útmutatóért lásd: [Oozie használata a munkafolyamatokhoz](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>További lépések

- [A Hadoop, Spark, Kafka és több HDInsight-fürtök beállítása](./hdinsight-hadoop-provision-linux-clusters.md)
