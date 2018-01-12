---
title: "Bontsa ki, átalakítási és betöltési (ETL) léptékű - Azure HDInsight |} Microsoft Docs"
description: "Ismerje meg, a hadooppal a Hdinsightban ETL használatáról."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 47c2d129cb296f6387142e03b14356bcd83ad698
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Kinyerési, átalakítási és betöltési (ETL) léptékű

Kinyerési, átalakítási és betöltési (ETL) az a folyamat, amellyel adatok különböző forrásokból szerzett, gyűjtése szabványos helyen, tisztítani és feldolgozni, és végső soron betölti, amelyből lekérdezhetők adattárolót. Adatok importálása, megtisztítsa tőle helyen és tárolása egy relációs adatok motor örökölt ETL-folyamatokkal. A hdinsight eszközzel a Hadoop ökoszisztémájának összetevők széles választékát támogatja, ETL elvégzését méretekben. 

Ez az adatcsatorna HDInsight használatának az az ETL-folyamat lépései röviden:

![HDInsight ETL áttekintése](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

Az alábbiakban megismerkedhet a ETL fázisok és a kapcsolódó összetevők.

## <a name="orchestration"></a>Adat-előkészítés

Vezénylési történjen az ETL-folyamat minden egyes szakaszába. Hdinsight ETL-feladatok gyakran tartalmaz, amely számos különböző termékek egymással párhuzamosan működik.  Hive segítségével törlése az adatok egy részét, amíg a Pig megtisztítja egy másik része lehet.  Hogy adatok betöltése az Azure SQL Database az Azure Data Lake Store az Azure Data Factory használatával.

Vezénylési megfelelő időben a megfelelő feladat futtatásához szükséges.

### <a name="oozie"></a>Oozie

Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Oozie belül HDInsight-fürtöt, és integrálva van a Hadoop-veremmel. Oozie Apache MapReduce, Apache Pig, Apache Hive és Apache Sqoop támogatja a Hadoop-feladatokat. Oozie feladatok, amelyek rendszerspecifikus, például Java programok vagy héjparancsfájlok ütemezésére ütemezését is használható.

További információkért lásd: [hadooppal határozza meg, és egy munkafolyamat futtatása a HDInsight használata Oozie](../hdinsight-use-oozie-linux-mac.md)

<!-- For a deep dive showing how to use Oozie to drive an end-to-end pipeline, see [Operationalize the Data Pipeline](hdinsight-operationalize-data-pipeline.md). -->

### <a name="azure-data-factory"></a>Azure Data Factory

Az Azure Data Factory a platform,--szolgáltatás formájában vezénylési képességeket biztosít. Egy felhőalapú integrációs szolgáltatás, amely lehetővé teszi az adatvezérelt munkafolyamatok létrehozni levezényli és automatizálja az adatmozgás és adatok átalakítása a felhőben is. 

Azure Data Factory használatával:

1. Hozzon létre, és ütemezés szerinti adatvezérelt munkafolyamatok (más néven folyamatok), amely különálló adattároló származó adatok.
2. Folyamat és a használatával végzett átalakítás számítási szolgáltatásokat, például az Azure HDInsight Hadoop, Spark, az Azure Data Lake Analytics, Azure Batch és Azure Machine Learning.
3. A kimeneti adatokat olyan adattárakban teheti közzé, mint az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket.

Az Azure Data Factory további információkért lásd: a [dokumentáció](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>A file storage és az eredmény tárolási betöltési

Forrásadatfájlok általában töltődnek be az Azure Storage vagy az Azure Data Lake Store egy helyre. Tetszőleges méretű fájlok lehetnek, ugyanakkor általában egybesimított fájlokba, például a CSV-k. 

### <a name="azure-storage"></a>Azure Storage 

[Az Azure Storage](https://azure.microsoft.com/services/storage/blobs/) rendelkezik [egyedi méretezhetőségi célok](../../storage/common/storage-scalability-targets.md).  A legtöbb elemzési csomópontok számára Azure Storage arányosan legjobb sok kisebb fájlt a meghatározásakor.  Az Azure Storage biztosítja, hogy ugyanaz a teljesítmény, függetlenül attól, fájlokat vagy méretét (amennyiben a határokon belül áll) fájlok.  Ez azt jelenti, hogy terabájtos adatkészleteket tárolja, és egységes teljesítmény továbbra is megjelenik, függetlenül attól, hogy az adatokat, vagy az adatok egy részét.

Az Azure Storage blobs számos különféle típusú rendelkezik.  Egy *hozzáfűző blob* naplókat web vagy érzékelői adatok tárolásához nagyszerű lehetőséget.  

Több blobok környezetekben, sok kiszolgálón való hozzáférés horizontális terjeszthető, de egyetlen blob csak egyetlen kiszolgáló szolgálhatók ki. Blobok logikailag csoportosíthatók blobtárolók, amíg nincsenek nem ez a csoportosítás a particionálási megvalósítását.

Az Azure Storage a blob Storage WebHDFS API réteggel is rendelkezik.  Összes szolgáltatásban a HDInsight férhetnek hozzá az Azure Blob Storage fájlok tisztítása adatokat és az adatok feldolgozása hasonló módon, hogy hogyan ezek a szolgáltatások akkor használják a Hadoop elosztott fájlok rendszer (HDFS).

Adatok általában okozhatnak az Azure Storage PowerShell, az Azure Storage szolgáltatás SDK vagy AZCopy használatával.

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

Azure Data Lake Store-(ADLS-) tárháza felügyelt, hyperscale, amely kompatibilis a HDFS analitikai adatok számára.  ADLS HDFS hasonló, és korlátlan méretezhetőség tekintetében teljes kapacitás és az egyes fájlok mérete kínál a Tervező paradigma használja. ADLS nem nagyon jó ha nagyméretű fájlokat, mivel a nagy fájlok tárolhatók több csomópont között.  Particionálás ADLS adatokat a háttérben történik.  Kiemelkedő átviteli sebességet biztosít a több ezer párhuzamos végrehajtóval rendelkező elemzési feladatok futtatásához, amelyekben a több száz terabájt adat olvasása és írása hatékonyan történik.

Adatok általában okozhatnak az Azure Data Factory, ADLS SDK-k, AdlCopy szolgáltatás, Apache ból a DistCp vagy Apache Sqoop használatával ADLS.  Ezen szolgáltatások használatára nagy mértékben függ az adatokat.  Ha az adatok jelenleg egy meglévő Hadoop-fürttel, használhatja a Apache ból a DistCp, a AdlCopy szolgáltatás vagy az Azure Data Factory.  Ha az Azure Blob Storage, Azure Data Lake Store .NET SDK, az Azure PowerShell vagy az Azure Data Factory használhatja.

ADLS is Azure Event Hubs vagy alatt futó Apache Storm esemény adatfeldolgozást van optimalizálva.

#### <a name="considerations-for-both-storage-options"></a>Mindkét tárolási lehetőségek szempontjai

Feltöltése adatkészletek terabájt tartományba, hálózati késés nagyobb problémát okozhat, különösen akkor, ha az adatok egy helyi helyre származik.  Ilyen esetben használhatja az alábbi beállítások közül:

* Azure ExpressRoute: Azure ExpressRoute segítségével Azure adatközpontjaiban és a helyszíni infrastruktúra közötti magánhálózati kapcsolatok létrehozása. Ezek a kapcsolatok nagy mennyiségű adat átvitele egy megbízható beállítást adja meg. További információkért lásd: [Azure ExpressRoute dokumentációja](../../expressroute/expressroute-introduction.md).

* "Offline" feltölteni az adatokat. Használhat [Azure Import/Export szolgáltatás](../../storage/common/storage-import-export-service.md) szállítási merevlemez-meghajtók az adatait az Azure adatközpontba. Az adatok először tölt fel az Azure Storage blobs szolgáltatásban. Ezután [Azure Data Factory](../../data-factory/v1/data-factory-azure-datalake-connector.md) vagy a [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) adatok másolása az Azure Storage blobs Data Lake Store az eszközt.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Az Azure SQL DW tárolásához kiváló választás, tisztítani és eredmények előkészítve a jövőbeli elemzés.  Az Azure HDInsight az Azure SQL DW szolgáltatások végrehajtásához használható.

Az SQL Data Warehouse (SQL DW) egy relációs adatbázis-tároló koncepción alapuló adatelemzési célokra optimalizálva.  Az Azure SQL DW alapján méretezi a particionált táblákat.  Táblák több csomópont között lehet particionálni.  Azure SQL DW-csomópontok ki van jelölve, a létrehozás időpontjában.  Lehessen őket méretezni bekövetkeztek, de ez egy aktív folyamat igénylő adatátvitelt jelölik. Lásd: [SQL Data Warehouse - kezelése számítási](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) további információt.

### <a name="hbase"></a>HBase

Apache HBase érhető el az Azure HDInsight egy kulcs-érték tárolóban.  Az Apache HBase egy nyílt forráskódú NoSQL-adatbázis, amely a Hadoopra épült, és a Google BigTable után van modellezve. A HBase biztosít performant véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok egy séma nélküli adatbázisban szerint rendezve.

Az adatok a táblasorokban vannak tárolva, és a sorokon belüli adatok oszlopcsalád szerint vannak csoportosítva. A HBase egy séma nélküli adatbázis abban az értelemben, hogy az oszlopokat és a bennük tárolt adattípusokat sem kell meghatározni a használatuk előtt. A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. A HBase számíthat adatredundanciát, kötegfeldolgozási és a Hadoop ökoszisztémájának elosztott alkalmazások által biztosított egyéb szolgáltatásokat.   

A HBase egy kiváló célja érzékelő és naplózási adatok további elemzés céljából.

A HBase a méretezhetőség azt a HDInsight-fürt a csomópontok száma függ.

### <a name="azure-sql-database-and-azure-database"></a>Az Azure SQL Database és az Azure-adatbázis

Az Azure platform,--szolgáltatás (PAAS), három különböző relációs adatbázisok kínál.

* [Az Azure SQL Database](../../sql-database/sql-database-technical-overview.md) Microsoft SQL Server megvalósítása. A teljesítményre további információkért lásd: [teljesítményének hangolása az Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure MySQL-adatbázis](../../mysql/overview.md) Oracle, MySQL megvalósítása.
* [Azure-adatbázis PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) PostgreSQL megvalósítása.

Ezeket a termékeket növelheti, ami azt jelenti, hogy azok több Processzor és memória hozzáadásával méretezhető.  Választhatja azt is, premium lemezek használata a termékek i/o-teljesítmény növelése érdekében.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Az Azure Analysis Services (AAS) döntési támogatási és üzleti analitikai lehetőségeket biztosítva az analitikus adatok üzleti jelentések és ügyfélalkalmazásokat, például a Power BI, Excel, Reporting Services-jelentések, és egyéb adatok használt analitikus adatok motor a képi megjelenítés eszközök.

Elemzési kockák minden egyes adatkocka rétegek módosításával is méretezhető.  További információkért lásd: [Azure Analysis Services szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Bontsa ki és betöltése

Miután az adatok létezik-e az Azure-ban, számos szolgáltatás segítségével kigyűjtésére, majd töltse be más termékek.  HDInsight a Sqoop és Flume támogatja. 

### <a name="sqoop"></a>Sqoop

Apache Sqoop egy olyan eszköz, hatékonyan közötti adatátvitel strukturált, félig strukturált és strukturálatlan adatforrások tervezték. 

Sqoop MapReduce használ az adatok párhuzamos művelet és a hibatűrés importálása és exportálása.

### <a name="flume"></a>Flume

Apache Flume olyan elosztott, megbízható és elérhető szolgáltatás hatékonyan gyűjtése, összesítése és nagy mennyiségű napló adatok áthelyezését. Flume rendelkezik egy egyszerű és rugalmas architektúra adatfolyam-adatfolyamok alapján. Flume hatékony és hibatűrő hangolható megbízhatóság mechanizmusok és sok feladatátvételi és helyreállítási mechanizmusokat. Flume egy egyszerű bővíthető adatmodell, amely lehetővé teszi, hogy az online analitikus alkalmazás használja.

Apache Flume Azure HDInsight nem használható.  Helyszíni Hadoop telepítés Flume segítségével adatokat küldeni a vagy az Azure Storage Blobs, vagy az Azure Data Lake Store.  További információkért lásd: [Apache Flume használata a hdinsightban](https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Átalakítás

Amennyiben az adatok a választott helyen szerepel, megtisztítsa tőle, kombinálhatja, vagy egy adott használati mód előkészíti szüksége.  Hive, Pig és Spark SQL az összes helyes döntések az adott munka.  Ezek használatát támogatja a hdinsight platformon. 

## <a name="next-steps"></a>További lépések

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
<!-- * [Using Apache Hive as an ETL Tool](hdinsight-using-apache-hive-as-an-etl-tool.md) -->
