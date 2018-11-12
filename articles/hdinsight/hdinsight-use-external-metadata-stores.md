---
title: Külső metaadat-tárolók – Azure HDInsight használata
description: Külső metaadat-tárolók használata a HDInsight-fürtökkel.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 288ee46e9a5741a49ddcec1ef155c6f08b7b6cbc
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016172"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Az Azure HDInsight külső metaadat-tárolók használata

A Hive-metaadattár a HDInsight Hadoop-architektúra alapvető része. A metaadattár a központi séma tárház, amely más big data-hozzáférés eszközök, például a Spark, interaktív lekérdezések (LLAP), Presto vagy Pig által használható. HDInsight Hive-metaadattár használja az Azure SQL Database.

![HDInsight Hive-metaadatok Store architektúra](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Beállíthat egy metaadattár a HDInsight-fürtök számára két módja van:

* [Alapértelmezett metaadattárat](#default-metastore)
* [Egyéni-metaadattár](#custom-metastore)

## <a name="default-metastore"></a>Alapértelmezett metaadattárat

Alapértelmezés szerint a HDInsight-metaadattár létrehoz minden fürt típusú. Helyette megadhat egy egyéni metaadattár. Az alapértelmezett metaadattárat az alábbiakat tartalmazza:
- További költségek nélkül. HDInsight-metaadattár hoz létre minden egyes fürttípus Önnek semmilyen további költségek nélkül.
- Egyes alapértelmezett metaadattárat a fürt életciklusa részét képezi. Ha töröl egy fürtöt, a megfelelő metaadattár és azok metaadatait is törlődik.
- Az alapértelmezett metaadattárat nem osztható meg más fürtökkel.
- Az alapértelmezett metaadattárat használja az alapszintű Azure SQL DB, amely esetében a öt DTU (adatbázis-tranzakciós egységek).
Az alapértelmezett metaadattárat jellemzően viszonylag egyszerű olyan számítási feladatok, amelyek több fürt nem igényelnek, és nem kell megőrzi a fürt életciklusa túli metaadatok szolgál.


## <a name="custom-metastore"></a>Egyéni-metaadattár

HDInsight emellett támogatja az egyéni metaadattárakat, javasolt az éles fürtökben:
- A saját Azure SQL Database a metaadattár adja meg.
- A metaadattár életciklusának nem kötődik fürtök életciklusa úgy hozhat létre és fürtök törlése az metaadatok elvesztése nélkül. Metaadatokat, például a Hive-sémák törli, majd hozza létre újból a HDInsight-fürt után is megmaradnak.
- Egy egyéni metaadattár több fürtöt és fürttípusok csatolása a metaadattár teszi lehetővé. Ha például egyetlen metaadattár keresztül megoszthatók interaktív lekérdezés, a Hive és a Spark-fürtök a HDInsight.
- A metaadattár (Azure SQL Database) alapján úgy dönt, a teljesítményszint járó költségeket kell fizetnie.
- Igény szerint skálázhatja fel a metaadattár.

![HDInsight Hive-metaadatok Store használati eset](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>Egy egyéni metaadattár válassza ki a fürt létrehozása során

Egy korábban létrehozott Azure SQL Database a fürt a fürt létrehozása során mutathat, vagy konfigurálhatja az SQL Database, a fürt létrehozása után. Ez a beállítás meg van adva a tároló > Metaadattár beállításai létrehozásakor egy új Hadoop, Spark vagy interaktív Hive-fürt Azure Portal használatával.

![HDInsight Hive-metaadatok Store az Azure Portalon](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Is hozzáadhat további fürtök egy egyéni metaadattár az Azure portálon vagy az Ambari-konfigurációk (Hive-> Speciális)

![HDInsight Hive-metaadatok Store Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Hive-metaadattár ajánlott eljárások

Az alábbiakban néhány általános HDInsight Hive metaadattár ajánlott eljárásokat:

- Egy egyéni metaadattár, amikor csak lehetséges, különálló számítási erőforrások (a futó fürt) és a metaadatok (a metaadattár tárolva) használja.
- Indítsa el az S2 szint, amely 50 DTU és 250 GB-os tárhelyet biztosít. Ha a szűk keresztmetszet, az adatbázis is méretezhető.
- Ha azt tervezi, hogy több HDInsight-fürtök külön adatok eléréséhez, egy különálló adatbázis használata az egyes fürtökön metaadattár. Ha megoszt egy metaadattár több HDInsight-fürtök között, az azt jelenti, hogy a fürtök használata a azonos metaadatokat és mögöttes felhasználói adatok fájlok.
- Rendszeresen készítsen biztonsági másolatot az egyéni metaadattár. Az Azure SQL Database automatikusan létrehozza a biztonsági mentések, de a biztonsági másolatok megőrzési időtartamon változik. További információkért lásd: [további információ az automatikus SQL Database biztonsági mentéseinek](../sql-database/sql-database-automated-backups.md).
- Keresse meg a metaadattár és a HDInsight-fürt ugyanabban a régióban, a legmagasabb szintű teljesítmény és a legalacsonyabb hálózati kimenő forgalom költségeit.
- A metaadattár a teljesítmény és rendelkezésre állás az Azure SQL-adatbázis figyelési eszközök, például az Azure portal vagy az Azure Log Analytics használatával figyelheti.
- Létrehozásakor egy új, magasabb szintű verzióját az Azure HDInsight egy meglévő egyéni metaadattár adatbázison, a rendszer a séma frissítése a metaadattár, amely nem vonható vissza anélkül, hogy az adatbázis biztonsági másolatból történő visszaállítását.
- Ha megoszt egy metaadattár több fürt között, győződjön meg arról az összes fürt ugyanazt a HDInsight-verziót. Különböző Hive-verziók különböző metaadattár adatbázissémák használja. Például egy metaadattár nem oszthat meg Hive 1.2 és a Hive 2.1-es verziójú fürtök között. 

## <a name="oozie-metastore"></a>Oozie-Metaadattár

Az Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli.  Az Oozie támogatja a Hadoop-feladatok Apache MapReduce, a Pig, Hive és mások.  Az Oozie-metaadattár és befejezett munkafolyamatok adatainak tárolására használja. Az Oozie használata a teljesítmény növelése érdekében használhatja az Azure SQL Database, egy egyéni metaadattár. A metaadattár a fürt törlése után is megadhatja az Oozie feladat adatokhoz való hozzáférést.

Az Azure SQL Database-Oozie-metaadattár létrehozásával kapcsolatos útmutatóért lásd: [Oozie használata a munkafolyamatokhoz](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>További lépések

- [A Hadoop, Spark, Kafka és több HDInsight-fürtök beállítása](./hdinsight-hadoop-provision-linux-clusters.md)
