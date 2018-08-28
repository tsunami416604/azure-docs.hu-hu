---
title: Kinyerés, átalakítás és betöltés (ETL) nagy léptékben – Azure HDInsight
description: Ismerje meg, hogyan ETL és a Hadoop HDInsight használatban van.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 6af55bf8311efc14687ee16c3226988fd730081d
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049002"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Kinyerési, átalakítási és betöltési (ETL) ipari méretekben

Kinyerési, átalakítási és betöltési (ETL) az a folyamat, amellyel adatok különböző forrásokból származó megszerzett, egy standard szintű helyen, tisztítani és feldolgozását, és végső soron tölt be egy adattár, amelyről lekérdezhetők. Adatok importálása, megtisztítsa tőle a helyen és tárolása egy relációs adatok motor hagyományos ETL-folyamatok. A HDInsight Hadoop-ökoszisztéma összetevők számos támogatják a nagy mennyiségű ETL végrehajtása. 

Ez a folyamat HDInsight használatának az az ETL-folyamat lehet összegezni:

![HDInsight ETL áttekintése](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

A következő szakaszok Fedezze fel az egyes ETL fázisai és a kapcsolódó összetevőket.

## <a name="orchestration"></a>Adat-előkészítés

Vezénylési átfogóan az ETL-folyamat minden szakaszában. A HDInsight ETL-feladatok gyakran egymással együttműködve számos különböző termék magában foglalja.  Hive segítségével az adatok bizonyos része tiszta, amíg a Pig törli egy másik része lehet.  Adatok betöltése az Azure SQL Database az Azure Data Lake Store segítségével az Azure Data Factory lehet.

Vezénylési van szükség a megfelelő feladat futtatásához a megfelelő időben.

### <a name="oozie"></a>Oozie

Az Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Az Oozie egy HDInsight-fürtön belül fut, és a Hadoop-veremmel integrálva van. Az Oozie támogatja a Hadoop-feladatok Apache MapReduce, Apache Pig, Apache Hive- és Apache sqoop használatával. Az Oozie is használható a rendszer, például Java programok vagy héjparancsfájlok ütemezésére adott feladatok ütemezéséhez.

További információkért lásd: [használni az Oozie megadásához és a munkafolyamat futtatása a HDInsight Hadoop-keretrendszerrel](../hdinsight-use-oozie-linux-mac.md) az Oozie használata a meghajtó egy teljes körű folyamatot ismertető részletes bemutatása, lásd: [üzembe helyezése a adatfolyamat](../hdinsight-operationalize-data-pipeline.md). 

### <a name="azure-data-factory"></a>Azure Data Factory

Az Azure Data Factory a platform--szolgáltatásként formájában koordinálási lehetőségeket biztosít. Egy felhőalapú adatintegrációs szolgáltatás, amely lehetővé teszi, hogy adatvezérelt munkafolyamatok létrehozását irányítják és automatizálják az adatok áthelyezését és átalakítását a felhőben. 

Azure Data Factory használatával:

1. Hozzon létre, és az adatvezérelt munkafolyamatokat (folyamatokat is nevezik) különböző adattárolókból adatokat beolvasó ütemezése.
2. Folyamat és az adatokat az átalakítási számítási szolgáltatások, például az Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch- és Azure Machine Learning.
3. A kimeneti adatokat olyan adattárakban teheti közzé, mint az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket.

Az Azure Data Factory további információkért lásd: a [dokumentáció](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>A file storage és az eredmény tárolási betöltési

Forrásadatfájlok általában töltődnek be az Azure Storage vagy az Azure Data Lake Store egy helyre. Fájlok bármilyen formátumú is lehet, de általában egybesimított fájlok, például a CSV-k legyenek. 

### <a name="azure-storage"></a>Azure Storage 

[Az Azure Storage](https://azure.microsoft.com/services/storage/blobs/) rendelkezik [adott teljesítménycélokat](../../storage/common/storage-scalability-targets.md).  A legtöbb elemzési csomópontok esetében az Azure Storage méretezhető legjobb sok kisebb fájlok esetén.  Az Azure Storage garantálja, hogy ugyanazokat a méretezhetőségi, függetlenül attól, hogy hány fájlok, vagy mekkora a fájlokat (amennyiben Ön a határokon belül).  Ez azt jelenti, hogy terabájtnyi adat tárolásához, és továbbra is élvezhesse az állandó teljesítmény érdekében, akár az adatok vagy az adatok egy részét használja.

Az Azure Storage blobok számos különböző típusú rendelkezik.  Egy *hozzáfűző blob* webnapló- vagy érzékelőktől kapott adatok tárolására szolgáló nagyszerű lehetőséget.  

Több blobok számos kiszolgáló azokhoz való hozzáférés horizontális szét lehetnek osztva, de egy blob csak egyetlen kiszolgáló kiszolgálható. Blobok logikailag lehet csoportosítani a blob-tárolók, amíg nincsenek külön particionálási kihatása a csoportosítást.

Az Azure Storage a blob Storage a WebHDFS API-réteget is tartalmaz.  A HDInsight összes szolgáltatás el tudja érni adattisztító és adatfeldolgozási, hasonló módon, hogy hogyan ezeket a szolgáltatásokat a Hadoop elosztott fájlokat (HDFS) használja az Azure Blob Storage-fájlokat.

Adatok általában betöltött, PowerShell, az Azure Storage SDK-t vagy az AZCopy használatával Azure Storage-bA.

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

Az Azure Data Lake Store (ADLS) egy felügyelt, nagy kapacitású adattár a HDFS-kompatibilis elemzési adatok.  ADLS használ egy tervezési paradigm, amely a HDFS hasonló, és korlátlan skálázhatóság tekintetében teljes kapacitás és az egyes fájlok mérete. ADLS akkor nagyon jó, ha nagy méretű fájlok használata, mivel nagy fájlok tárolhatók több csomóponton.  Az ADLS az adatok particionálása a háttérben történik.  Kiemelkedő átviteli sebességet biztosít a több ezer párhuzamos végrehajtóval rendelkező elemzési feladatok futtatásához, amelyekben a több száz terabájt adat olvasása és írása hatékonyan történik.

Adatok általában az Azure Data Factory, ADLS SDK-k, AdlCopy szolgáltatás, Apache DistCp vagy az Apache Sqoop használatával ADLS-be lett töltve.  Ezek a szolgáltatások nagymértékben használandó közül melyik attól függ, hogy az adatok.  Ha az adatokat jelenleg egy meglévő Hadoop-fürtöt, az Apache DistCp, AdlCopy szolgáltatás vagy az Azure Data Factory használhat.  Ha az Azure Blob Storage, Azure Data Lake Store .NET SDK, az Azure PowerShell vagy az Azure Data Factory használhat.

ADLS is az Azure Event Hub vagy az Apache Storm használatával eseményfogadás van optimalizálva.

#### <a name="considerations-for-both-storage-options"></a>Mindkét tárolási lehetőség szempontjai

Fel a tartományba terabájtos adatkészleteket, hálózati késés nagyobb problémát jelenthetnek, különösen akkor, ha a helyszíni helyről az adatok várható.  Ezekben az esetekben használhatja az alábbi beállításokat:

* Az Azure ExpressRoute: Azure ExpressRoute használatával privát kapcsolatok létesíthetők az Azure-adatközpontok és a helyszíni infrastruktúra között. Ezek a kapcsolatok lehetőséget nyújtanak olyan megbízható nagy mennyiségű adat átvitele. További információkért lásd: [Azure ExpressRoute dokumentációja](../../expressroute/expressroute-introduction.md).

* "Offline állapotba helyezi" az adatok feltöltése. Használhat [Azure Import/Export szolgáltatás](../../storage/common/storage-import-export-service.md) tehetnek a merevlemez-meghajtók egy Azure-adatközpontban található adatait. Az adatfeltöltés először az Azure Storage-blobokat. Ezután [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) vagy a [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) adatok másolása az Azure Storage-blobokból a Data Lake Store az eszközt.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Az Azure SQL DW tárolásához kiváló választás az tisztítani és az eredmények előkészítve a jövőbeli elemzések.  Az Azure HDInsight segítségével hajtsa végre ezeket a szolgáltatásokat az Azure SQL dw-ben.

Az Azure SQL Data Warehouse (az SQL DW) egy relációs adatbázis-tároló elemzési feladatokra optimalizált.  Az Azure SQL DW alapján méretezi a particionált táblákat.  Táblák több csomóponton lehet particionálni.  Azure SQL DW-csomópontok ki van jelölve, a létrehozás időpontjában.  Az utólag lehessen méretezni őket, de ez egy aktív folyamat, amely lehet szükség az adatok áthelyezését. Lásd: [SQL Data Warehouse - kezelése számítási](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md) további információt.

### <a name="hbase"></a>HBase

Az Apache HBase egy Azure HDInsight elérhető kulcs-érték tárolóra.  Az Apache HBase egy nyílt forráskódú NoSQL-adatbázis, amely a Hadoopra épült, és a Google BigTable után van modellezve. A HBase biztosít a nagy teljesítményű véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok séma nélküli adatbázisban oszlopcsaládok szerint rendezve.

Az adatok a táblasorokban vannak tárolva, és a sorokon belüli adatok oszlopcsalád szerint vannak csoportosítva. A HBase egy séma nélküli adatbázis abban az értelemben, hogy az oszlopokat és a bennük tárolt adattípusokat sem kell meghatározni a használatuk előtt. A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. A HBase hagyatkozhat adatredundancia, kötegelt feldolgozás és a Hadoop-ökoszisztéma elosztott alkalmazások által biztosított funkciókat.   

A HBase az érzékelő és a naplózási adatok jövőbeli elemzések kiváló célhelyet.

A HBase méretezhetőség az adott nyelvtől függ a HDInsight-fürtben található csomópontok számát.

### <a name="azure-sql-database-and-azure-database"></a>Az Azure SQL Database és az Azure-adatbázis

Az Azure platform –-szolgáltatásként (PAAS) kínál három különböző relációs adatbázist.

* [Az Azure SQL Database](../../sql-database/sql-database-technical-overview.md) a Microsoft SQL Server implementációja. További információ a teljesítményre: [teljesítmény hangolása az Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md) az Oracle, MySQL implementációja.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) PostgreSQL implementációja.

Ezeket a termékeket, méretezhető, ami azt jelenti, hogy azok több Processzor és memória hozzáadásával horizontálisan.  Prémium szintű lemezek használata a termékeket a jobb i/o-teljesítmény azt is beállíthatja.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Az Azure Analysis Serviceshez (AAS) egy olyan analitikai adatokat motor, Döntéstámogatás és üzleti elemzés, a Reporting Services-jelentések, üzleti jelentések és az ügyfélalkalmazások, például a Power BI-, Excel, az analitikus adatok biztosítása és egyéb adatok vizualizációs eszközök.

Elemzési kockák szinten minden egyes adatkockához módosításával skálázhatja.  További információkért lásd: [Azure Analysis Services díjszabása](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Kinyerés és betöltés

Miután az adatok állnak rendelkezésre az Azure-ban, számos szolgáltatás segítségével csomagolja ki, és betöltheti, egyéb termékek.  HDInsight a Sqoop és Flume támogatja. 

### <a name="sqoop"></a>Sqoop

Az Apache sqoop használatával egy olyan eszköz, hatékonyan közötti adatátvitel strukturált, félig strukturált és strukturálatlan adatforrások tervezve. 

Sqoop MapReduce importálása és exportálása az adatok párhuzamos művelet és a hibatűrés biztosításához használja.

### <a name="flume"></a>Flume

Az Apache Flume egy elosztott, megbízható és elérhető szolgáltatás hatékonyan összegyűjtése, összevonása, és nagy mennyiségű Teljesítménynapló-adatok áthelyezését. Flume egy egyszerű és rugalmas architektúra alapján a streamelési adatok folyamatokat tartalmaz. Flume, hatékony és hibatűrő hangolható megbízhatóság mechanizmusok és számos feladatátvételi és helyreállítási mechanizmusokat. Flume, amely lehetővé teszi, hogy az online analitikus alkalmazás egyszerű bővíthető adatok modellt használ.

Az Azure HDInsight az Apache Flume nem használható.  Egy helyi Hadoop-telepítés Flume használatával vagy az Azure Storage-Blobokkal, vagy az Azure Data Lake Store adatküldéshez.  További információkért lásd: [Apache Flume használata a HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Átalakítás

Miután adatokat már létezik a megadott helyre, kell megtisztítsa tőle, összevonásához vagy felkészítse az egy adott használati módot.  Hive, Pig és a Spark SQL a munka jellege jó lehetőségeit.  Ezek mind támogatottak a HDInsight. 

## <a name="next-steps"></a>További lépések

* [A Pig használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-pig.md)
* [Az Apache Hive használatával egy ETL-eszközként](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
