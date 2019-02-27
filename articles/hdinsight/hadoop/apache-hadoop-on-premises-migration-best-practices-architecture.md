---
title: A helyszíni Apache Hadoop-fürtök áttelepítése az Azure HDInsight - architektúra ajánlott eljárásai
description: Ismerje meg, hogy áttelepítése a helyszíni Hadoop-fürtöket az Azure HDInsight architektúra ajánlott eljárásai.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: f1e2b9dfc329e67d94fba998a01d593b992ba90f
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886808"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>A helyszíni Apache Hadoop-fürtök áttelepítése az Azure HDInsight - architektúra ajánlott eljárásai

Ez a cikk biztosítja az architektúra az Azure HDInsight rendszerek javaslatok. Ez azt egy olyan sorozat részét, amely ajánlott eljárásokat, amelyek segítik az Azure HDInsight áttelepítése a helyszíni Apache Hadoop-rendszerekhez biztosít.

## <a name="use-multiple-workload-optimized-clusters"></a>Több munkaterhelés optimalizált fürtök használata

Számos helyszíni Apache Hadoop üzemelő áll egy nagy fürt, amely támogatja a több számítási feladatokhoz. Ez egy fürtön összetettek lehetnek, és az egyes szolgáltatásokhoz való együttműködésének biztosítása értelmeznie előírhatja. Migrálás a helyszíni Azure HDInsight Hadoop-fürtöket követel meg megközelítést.

Egy adott típusú számítási feladatok használatáért az Azure HDInsight-fürtök lettek kialakítva. Mivel a tároló több fürt is megoszthatók, hozható létre több számítási feladat optimalizált számítási fürtök különböző feladatok igényeinek kielégítése érdekében. Mindegyik fürttípus rendelkezik az adott munkaterhelés optimális konfigurációt. Az alábbi táblázat a támogatott fürttípusok a HDInsight és a megfelelő számítási feladatokhoz.

|**Számítási feladat**|**HDInsight-fürt típusa**|
|---|---|
|Kötegelt feldolgozás (ETL / ELT)|Hadoop, Spark|
|Adatraktározás|Hadoop, Spark, interaktív lekérdezés|
|IoT / Streamelési|Kafka, Storm, Spark|
|NoSQL tranzakciós feldolgozást kínál|HBase|
|Interaktív és gyorsabb, memórián belüli gyorsítótár-lekérdezések|Interaktív lekérdezés|
|Adatelemzés|ML Services, Spark|

Az alábbi táblázat a különböző módszereket, amelyek segítségével hozzon létre egy HDInsight-fürtön.

|**Eszköz**|**Böngészőalapú**|**Parancssor**|**REST API**|**SDK**|
|---|---|---|---|---|
|[Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Az Azure parancssori felület (ver 1.0-s)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Az Azure Resource Manager-sablonok](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

További információkért tekintse meg a cikket [típusok a HDInsight-fürt](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Átmeneti igény szerinti fürtök használata

HDInsight-fürtök hosszú időn át lehet, hogy nyissa meg a fel nem használt. Az erőforrás-használati díjak megtakarítása érdekében, HDInsight támogatja az igény szerinti átmeneti fürtök, amely a számítási feladat sikeres befejeződése után lehet törölni.

A fürt törlésekor a kapcsolódó tárfiók és a külső metaadat nem lesznek eltávolítva. A fürt később újra hozható létre a tárfiókok azonos és a metaadat-tárolók.

Az Azure Data Factory segítségével igény szerinti HDInsight-fürtök létrehozásának ütemezése. További információkért tekintse meg a cikket [igény szerinti Apache Hadoop-fürtök létrehozása az Azure Data Factory használatával HDInsight](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Leválaszthatja a tárterületet a számítási

Tipikus helyszíni Hadoop üzemelő használja ugyanazokat a gépek adattárolási és az adatok feldolgozása. Mert azok ugyanarra a szalagra, számítási és tárolási kell méretezhető együtt.

A HDInsight-fürtökön tároló nem helyezhetők ugyanarra a szalagra számítási kell és vagy az Azure storage, Azure Data Lake Storage vagy mindkettőben. Elválasztás tárterületet a számítási a következő előnyökkel jár:

- Fürtök közötti adatmegosztás.
- Átmeneti fürtökkel, mivel az adatok nem függ a fürt használatát.
- Alacsonyabb tárolási költségek.
- Tároló méretezése, és külön-külön számítási.
- Az adatreplikációt a régiók között elosztva.

A számítási Azure-régióban elválasztása a számítási és tárolási járó teljesítményi költségeket csökkenti a tárfiók erőforrásainak közelében való létrehozása. Nagy sebességű hálózat hatékonnyá a számítási csomópontok az Azure storage lévő adatok eléréséhez.

## <a name="use-external-metadata-stores"></a>Külső metaadat-tárolók használata


Vannak, amelyek együttműködnek a HDInsight-fürtök két fő metaadattárakat: [Az Apache Hive](https://hive.apache.org/) és [Apache Oozie](https://oozie.apache.org/). A Hive-metaadattár a központi séma tárház, amely a Hadoop, Spark, az LLAP, beleértve a Presto adatokat feldolgozó motorokkal, és az Apache Pig által használható. Az Oozie-metaadattár tárolja a folyamatban lévő és befejezett Hadoop-feladatok ütemezési adatait és állapotát.


HDInsight az Azure SQL Database, az Oozie és a Hive-metaadattárak. A HDInsight-fürtök metaadattár beállításához két módja van:

1. Alapértelmezett metaadattárat

    - További költségek nélkül.
    - Metaadattár törlődik a fürt törlésekor.
    - Metaadattár eltérő fürtök között nem lehet megosztani.
    - Használja az alapszintű Azure SQL DB, amely öt dtu-k esetében.

1. Egyéni külső metaadattár

    - Adja meg egy külső Azure SQL Database a metaadattár.
    - Fürtöket hozhat létre és metaadatok, mint például a Hive séma Oozie feladat részletes elvesztése nélkül.
    - Egyetlen metaadattár db megoszthatók a különféle típusú fürtök.
    - Metaadattár igény szerint skálázhatók.
    - További információkért lásd: [külső metaadat-tárolók az Azure HDInsight használata](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Ajánlott eljárások a Hive-Metaadattár

Egy HDInsight Hive-metaadattár ajánlott eljárások a következők:

- Használjon egy külön számítási erőforrások és a metaadatok egyéni külső metaadattár.
- Indítsa el az S2 szintű Azure SQL-példány, amely 50 DTU és 250 GB-os tárhelyet biztosít. Ha a szűk keresztmetszet, az adatbázis is méretezhető.
- Ne ossza meg a létrehozott egy HDInsight-fürt verziója és a egy eltérő verziójú fürtök együttes metaadattár. Különböző Hive-verziók különböző sémákkal használja. A metaadattár például nem oszthatók meg a Hive-1.2-es és a Hive 2.1-fürtökkel.
- Rendszeresen készítsen biztonsági másolatot az egyéni metaadattár.
- A metaadattár és a HDInsight-fürt tartsa ugyanabban a régióban.
- A metaadattár a teljesítmény és rendelkezésre állás az Azure SQL-adatbázis figyelési eszközökkel, például az Azure Portalon vagy az Azure Monitor-naplók használatával figyelheti.
- Hajtsa végre a **elemzése tábla** létrehozni statisztikákat a táblák és oszlopok kötelezőként parancsot. Például: `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Ajánlott eljárások a különböző számítási feladatok

- Érdemes megfontolni az LLAP-fürtöt interaktív Hive-lekérdezések és a továbbfejlesztett válaszidő [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) Hive 2.0, amely lehetővé teszi a memórián belüli gyorsítótárazáshoz, a lekérdezések új szolgáltatása. LLAP teszi akár sokkal gyorsabb Hive-lekérdezések [26 x gyorsabb, mint a Hive-1.x bizonyos esetekben](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Spark-feladatok Hive-feladatok helyett érdemes lehet.
- Fontolja meg, és cserélje le az impala-alapú lekérdezések LLAP-lekérdezéseket.
- Fontolja meg, és cserélje le a MapReduce-feladatok Spark-feladatok.
- Fontolja meg, és cserélje le a közel valós idejű batch-feladatok a Spark-feladatok a Spark strukturált Stream használata.
- Fontolja meg az adatok előkészítése az Azure Data Factory (ADF) 2.0 használatát.
- Fontolja meg az Ambari fürt felügyeletéhez.
- Módosítsa az adattárolás helyszíni HDFS-ből WASB vagy az ADLS vagy az ADFS parancsfájlok feldolgozás céljából.
- Fontolja meg a Ranger RBAC használata a Hive-táblák és a naplózás.
- Cosmos DB MongoDB vagy Cassandra helyett érdemes lehet.

## <a name="next-steps"></a>További lépések

Olvassa el az oktatóanyag-sorozatban a következő cikkben:

- [Az Azure HDInsight Hadoop-áttelepítési helyszíni infrastruktúra ajánlott eljárásai](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
