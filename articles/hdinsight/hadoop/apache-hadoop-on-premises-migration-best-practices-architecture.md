---
title: 'Architektúra: Helyszíni Apache Hadoop az Azure HDInsight'
description: Ismerje meg a helyszíni Hadoop-fürtök Azure HDInsightba való áttelepítésével kapcsolatos gyakorlati tanácsok at.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 2d0d5bb871612bc5e16a26eb49808c39661ffb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934692"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Telepítse át a helyszíni Apache Hadoop-fürtöket az Azure HDInsightba – az építészet ajánlott gyakorlatai

Ez a cikk az Azure HDInsight-rendszerek architektúráját ismerteti. Ez egy olyan sorozat része, amely gyakorlati tanácsokkal segíti a helyszíni Apache Hadoop-rendszerek Azure HDInsightba való áttelepítését.

## <a name="use-multiple-workload-optimized-clusters"></a>Több, munkaterhelésre optimalizált fürt használata

Számos helyszíni Apache Hadoop-üzemegyetlen nagy fürtből áll, amely számos számítási feladatot támogat. Ez az egyetlen fürt összetett lehet, és az egyes szolgáltatások számára kompromisszumokat tehet szükségessé, hogy minden együtt működjön. A helyszíni Hadoop-fürtök áttelepítése az Azure HDInsightba a megközelítés megváltoztatását igényli.

Az Azure HDInsight-fürtök egy adott típusú számítási használatra vannak tervezve. Mivel a tároló több fürt között is megosztható, több, munkaterhelésre optimalizált számítási fürt et is létrehozhat, hogy megfeleljen a különböző feladatok igényeinek. Minden fürttípus rendelkezik az adott munkaterhelés optimális konfigurációjának. Az alábbi táblázat a HDInsight támogatott fürttípusait és a megfelelő munkaterheléseket sorolja fel.

|Számítási feladat|HDInsight-fürt típusa|
|---|---|
|Kötegelt feldolgozás (ETL / ELT)|Hadoop, Szikra|
|Adatraktározás|Hadoop, Spark, Interaktív lekérdezés|
|IoT / Streamelés|Kafka, Vihar, Szikra|
|NoSQL tranzakciós feldolgozás|HBase|
|Interaktív és gyorsabb lekérdezések a memórián belüli gyorsítótárazásával|Interaktív lekérdezés|
|Adattudomány|ML szolgáltatások, Spark|

Az alábbi táblázat a HDInsight-fürt létrehozásához használható különböző módszereket mutatja be.

|Eszköz|Böngésző alapú|Parancssor|REST API|SDK|
|---|---|---|---|---|
|[Azure-portál](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure-adatgyár](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (ver 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[Curl](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Azure Resource Manager-sablonok](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

További információt a [HDInsight fürttípusai című cikkben talál.](../hadoop/apache-hadoop-introduction.md)

## <a name="use-transient-on-demand-clusters"></a>Tranziens igény szerinti fürtök használata

A HDInsight-fürtök hosszú ideig használaton kívüliek lehetnek. Az erőforrásköltségek csökkentése érdekében a HDInsight támogatja az igény szerinti átmeneti fürtöket, amelyek a munkaterhelés sikeres befejezése után törölhetők.

Fürt törlésekor a társított tárfiók és a külső metaadatok nem törlődnek. A fürt később újra létrehozható ugyanazzal a tárfiókkal és metatárolóval.

Az Azure Data Factory igény szerinti HDInsight-fürtök létrehozásának ütemezésére használható. További információt az [Igény szerinti Apache Hadoop-fürtök létrehozása a HDInsightban](../hdinsight-hadoop-create-linux-clusters-adf.md)az Azure Data Factory használatával című témakörben talál.

## <a name="decouple-storage-from-compute"></a>A tárolás leválasztása a számítástól

A helyszíni Hadoop-telepítések tipikusan ugyanazokat a gépeket használják az adattároláshoz és az adatfeldolgozáshoz. Mivel közös helyen helyezkednek el, a számítási és tárolási feladatokat együtt kell méretezni.

A HDInsight-fürtökön a tárolást nem kell a számítással együtt elhelyezni, és lehet az Azure Storage, az Azure Data Lake Storage vagy mindkettő. A tárolás és a számítás függetlenítése a következő előnyökkel jár:

- Adatmegosztás fürtök között.
- Átmeneti fürtök használata, mivel az adatok nem függ a fürttől.
- Csökkentett tárolási költség.
- A tárhely és a számítás külön-külön történő méretezése.
- Adatreplikáció régiók között.

A számítási fürtök az Azure-régióban a tárfiók-erőforrások közelében jönnek létre a számítási és tárolási elkülönítés teljesítményköltségeinek csökkentése érdekében. A nagy sebességű hálózatok hatékonysá teszik a számítási csomópontok számára az Azure storage-on belüli adatok elérését.

## <a name="use-external-metadata-stores"></a>Külső metaadat-tárolók használata

A HDInsight-fürtökkel két fő metatároló működik: [Az Apache Hive](https://hive.apache.org/) és az [Apache Oozie](https://oozie.apache.org/). A Hive metastore a központi sématár, amely et olyan adatfeldolgozó motorok használhatják, mint a Hadoop, a Spark, az LLAP, a Presto és az Apache Pig. Az Oozie metastore tárolja az ütemezés részleteit és a folyamatban lévő állapotot, és befejezte a Hadoop-feladatokat.

A HDInsight az Azure SQL Database-t használja a Hive és az Oozie metaáruházakhoz. A METAtárolók HDInsight-fürtökben kétféleképpen állíthatók be:

1. Alapértelmezett metabolt

    - Nincs további költség.
    - A metatároló a fürt törlésekor törlődik.
    - A metastore nem osztható meg a különböző fürtök között.
    - Egyszerű Azure SQL DB-t használ, amely öt DTU-korláttal rendelkezik.

1. Egyéni külső metatár

    - Adjon meg egy külső Azure SQL-adatbázist metastore-ként.
    - A fürtök a metaadatok elvesztése nélkül hozhatók létre és törölhetők, beleértve a Hive-séma Oozie feladat részleteit.
    - Az egymetastore db különböző típusú fürtökkel osztható meg.
    - A metastore szükség szerint felskálázható.
    - További információ: [Külső metaadat-tárolók használata az Azure HDInsightban című témakörben.](../hdinsight-use-external-metadata-stores.md)

## <a name="best-practices-for-hive-metastore"></a>A Hive Metastore bevált módszerei

Néhány HDInsight Hive metastore ajánlott eljárás a következő:

- Egyéni külső metatár használatával külön számítási erőforrások és metaadatok.
- Kezdje egy S2 rétegű Azure SQL-példány, amely 50 DTU és 250 GB tárhelyet biztosít. Ha szűk keresztmetszetet lát, felskálázhatja az adatbázist.
- Ne ossza meg az egy HDInsight-fürthöz létrehozott metatárolót egy másik verzió fürtjével. A különböző Hive-verziók különböző sémákat használnak. Például egy metaáruház nem osztható meg a Hive 1.2 és a Hive 2.1 fürtök.
- Rendszeresen biztonsági másolatot kell fésülni az egyéni metatárolóról.
- Tartsa a metastore és a HDInsight-fürt ugyanabban a régióban.
- Az Azure SQL Database Monitoring eszközeivel, például az Azure Portalon vagy az Azure Monitor-naplókban figyelheti a metatároló teljesítményét és rendelkezésre állását.
- A `ANALYZE TABLE` táblák és oszlopok statisztikáinak létrehozásához szükség szerint hajtsa végre a parancsot. Például: `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Gyakorlati tanácsok a különböző munkaterhelések

- Fontolja meg az LLAP-fürt használatát az [llap-válaszidővel](https://cwiki.apache.org/confluence/display/Hive/LLAP) rendelkező, továbbfejlesztett válaszidejű interaktív lekérdezésekhez, a Hive 2.0 új szolgáltatása, amely lehetővé teszi a lekérdezések memórián belüli gyorsítótárazását. Az LLAP bizonyos esetekben sokkal gyorsabbá teszi a Hive-lekérdezéseket, akár [26-szor gyorsabbá, mint a Hive 1.x.](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)
- Fontolja meg a Spark-feladatok használata a Hive-feladatok helyett.
- Fontolja meg az impala-alapú lekérdezések LLAP-lekérdezésekkel való lecserélését.
- Fontolja meg a MapReduce feladatok helyett a Spark-feladatok.
- Fontolja meg az alacsony késleltetésű Spark kötegelt feladatok helyett a Spark strukturált streamelési feladatok használatával.
- Fontolja meg az Azure Data Factory (ADF) 2.0-s adatvezénylési használatát.
- Fontolja meg az Ambari a fürtkezelése.
- Módosítsa az adattárolást a helyszíni HDFS-ről WASB-re, az ADLS-re vagy az ADFS-re parancsfájlok feldolgozásához.
- Fontolja meg a Ranger RBAC használatát a Hive-táblákon és a naplózást.
- Fontolja meg a CosmosDB használatát a MongoDB vagy a Cassandra helyett.

## <a name="next-steps"></a>További lépések

Olvassa el a sorozat következő cikkét:

- [Az Azure HDInsight Hadoop-áttelepítéshez szükséges helyszíni infrastruktúra-eljárások](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
