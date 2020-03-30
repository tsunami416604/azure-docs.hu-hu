---
title: Kibontás, átalakítás és betöltés (ETL) skálázáskor – Azure HDInsight
description: Ismerje meg, hogyan használja a kivonatot, az átalakítást és a betöltést a HDInsight az Apache Hadoop segítségével.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: f4be3343f090c4d31ccb85eba8e99f22a3b1fcae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529475"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Kivonás, átalakítás és betöltés (ETL) nagy méretekben

A kinyerés, az átalakítás és a betöltés (ETL) az a folyamat, amelynek során az adatokat különböző forrásokból szerzik be, szabványos helyen gyűjtik, tisztítják és feldolgozzák, és végül betöltik egy adattárba, amelyből lekérdezhetők. A régebbi ETL feldolgozza az adatok importálását, a helyükön tisztítja, majd relációs adatmotorban tárolja azokat. A HDInsight segítségével az Apache Hadoop ökoszisztéma-összetevők széles választéka támogatja az ETL nagy méretekben való elvégzését.

A HDInsight használata az ETL folyamatban ez a folyamat összegezhető:

![HDInsight ETL méretarányos áttekintés](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

A következő szakaszok az ETL-fázisokat és a hozzájuk kapcsolódó összetevőket vizsgálják.

## <a name="orchestration"></a>Hangszerelés

Vezénylési spans az ETL-folyamat összes fázisában. A HDInsight ETL-feladatai gyakran több különböző terméket foglalnak magukban, amelyek egymással együtt dolgoznak.  Előfordulhat, hogy a Hive segítségével tisztítsa meg az adatok egy részét, míg a Pig egy másik részt tisztít.  Előfordulhat, hogy az Azure Data Factory segítségével adatokat tölthet be az Azure Data Lake Store-ból az Azure Data Lake Store-ból.

Vezénylési szükséges a megfelelő feladat futtatásához a megfelelő időben.

### <a name="apache-oozie"></a>Apacs Oozie

Az Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Az Oozie egy HDInsight-fürtön belül fut, és integrálva van a Hadoop verembe. Az Oozie támogatja a Hadoop-feladatokat az Apache Hadoop MapReduce, az Apache Pig, az Apache Hive és az Apache Sqoop esetében. Az Oozie a rendszerre jellemző feladatok, például a Java-programok vagy a rendszerhéj-parancsfájlok ütemezésére is használható.

További információ: [Az Apache Oozie használata az Apache Hadoop segítségével definiálhat és futtathat egy munkafolyamatot a HDInsight-on:](../hdinsight-use-oozie-linux-mac.md) mély merülés, amely bemutatja, hogyan használhatja az Oozie-t egy végpontok között futó folyamat vezetésére, [lásd: Az adatfolyamat működőképessé tétele.](../hdinsight-operationalize-data-pipeline.md)

### <a name="azure-data-factory"></a>Azure Data Factory

Az Azure Data Factory vezénylési képességeket biztosít platformszolgáltatásként formájában. Ez egy felhőalapú adatintegrációs szolgáltatás, amely lehetővé teszi, hogy adatvezérelt munkafolyamatokat hozzon létre a felhőben az adatok mozgásának és az adatok átalakításának koordinálására és automatizálására.

Az Azure Data Factory használatával a következőket teheti:

1. Olyan adatközpontú munkafolyamatok (úgynevezett folyamatok) létrehozása és ütemezése, amelyek különböző adattárakból adatokat adnak be.
2. Dolgozza fel és alakítsa át az adatokat olyan számítási szolgáltatásokkal, mint az Azure HDInsight Hadoop, a Spark, az Azure Data Lake Analytics, az Azure Batch és az Azure Machine Learning.
3. A kimeneti adatokat olyan adattárakban teheti közzé, mint az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket.

Az Azure Data Factoryról további információt a [dokumentációban](../../data-factory/introduction.md)talál.

## <a name="ingest-file-storage-and-result-storage"></a>Fájltárolás és eredménytárolás betöltése

A forrásadatfájlok általában az Azure Storage vagy az Azure Data Lake Storage egy helyre töltődnek be. A fájlok bármilyen formátumban lehetnek, de általában lapos fájlok, például CSV-k.

### <a name="azure-storage"></a>Azure Storage

[Az Azure Storage](https://azure.microsoft.com/services/storage/blobs/) speciális méretezhetőségi célokkal rendelkezik. További információ: [Méretezhetőségi és teljesítménycélok a Blob storage.](../../storage/blobs/scalability-targets.md) A legtöbb analitikus csomópont esetében az Azure Storage méretarányosan méretezhető, ha sok kisebb fájllal van kezelve.  Az Azure Storage garantálja ugyanazt a teljesítményt, függetlenül attól, hogy hány fájlt, vagy milyen nagy a fájlok (mindaddig, amíg ön határain belül).  Ez azt jelenti, hogy több terabájtnyi adatot tárolhat, és továbbra is egységes teljesítményt érhet el, függetlenül attól, hogy az adatok egy részét vagy az összes adatot használja.

Az Azure Storage több különböző típusú blobok rendelkezik.  A *hozzáfűző blob* egy nagyszerű lehetőség a webes naplók vagy érzékelő adatok tárolására.  

Több blobok között elosztható számos kiszolgálón, hogy a hozzáférést horizontális felskálázásuk, de egyetlen blob csak egyetlen kiszolgáló által kiszolgálható. A blobok logikailag csoportosíthatók blobtárolókban, nincsenek particionálási következmények ebből a csoportosításból.

Az Azure Storage is rendelkezik egy WebHDFS API-réteg a blob storage.  A HDInsight összes szolgáltatása hozzáférhet az Azure Blob Storage-ban lévő fájlokhoz az adatok tisztításához és az adatfeldolgozáshoz, hasonlóan ahhoz, ahogyan ezek a szolgáltatások használnák a Hadoop Distributed Files System (HDFS) rendszert.

Az adatok at általában az Azure Storage-ba kell betöltésre használni a PowerShell, az Azure Storage SDK vagy az AZCopy használatával.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Az Azure Data Lake Storage (ADLS) egy felügyelt, nagy kapacitású tárház a HDFS-sel kompatibilis elemzési adatok hoz.  Az ADLS a HDFS-hez hasonló tervezési paradigmát használ, és korlátlan skálázhatóságot kínál a teljes kapacitás és az egyes fájlok mérete tekintetében. Az ADLS nagyon jó nagy fájlok esetén, mivel egy nagy fájl több csomóponton is tárolható.  Az ADLS-ben az adatok particionálása a színfalak mögött történik.  Hatalmas átviteli forgalmat kap az analitikus feladatok futtatásához több ezer egyidejű végrehajtóval, amelyek több száz terabájtnyi adatot olvasnak és írnak.

Az adatokat általában az Azure Data Factory, az ADLS SDK-k, az AdlCopy-szolgáltatás, az Apache DistCp vagy az Apache Sqoop használatával használják az ADLS-be.  Az adatok használatának helye attól függ, hogy e szolgáltatások közül melyiket használja.  Ha az adatok jelenleg egy meglévő Hadoop-fürtben vannak, használhatja az Apache DistCp, az AdlCopy Service vagy az Azure Data Factory szolgáltatást.  Ha az Azure Blob Storage, használhatja az Azure Data Lake Storage .NET SDK, Az Azure PowerShell vagy az Azure Data Factory.

Az ADLS az Azure Event Hub vagy az Apache Storm használatával is eseménybetöltésre van optimalizálva.

#### <a name="considerations-for-both-storage-options"></a>Mindkét tárolási lehetőség szempontjai

A terabájtos tartományban lévő adatkészletek feltöltése esetén a hálózati késés komoly problémát okozhat, különösen akkor, ha az adatok helyszíni helyről érkeznek.  Ilyen esetekben az alábbi lehetőségeket használhatja:

* Azure ExpressRoute: Az Azure ExpressRoute lehetővé teszi, hogy privát kapcsolatokat hozzon létre az Azure-adatközpontok és a helyszíni infrastruktúra között. Ezek a kapcsolatok megbízható lehetőséget biztosítanak nagy mennyiségű adat átvitelére. További információt az [Azure ExpressRoute dokumentációjában](../../expressroute/expressroute-introduction.md)talál.

* "Offline" adatok feltöltése. [Az Azure import/exportálási szolgáltatással](../../storage/common/storage-import-export-service.md) merevlemez-meghajtókat szállíthat az adatokkal egy Azure-adatközpontba. Az adatok először az Azure Storage Blobs-ba töltődnek fel. Ezután az [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) vagy az [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) eszköz segítségével adatokat másolhat az Azure Storage blobokból a Data Lake Storage-ba.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Az Azure SQL DW egy nagyszerű választás a megtisztított és előkészített eredmények tárolására a jövőbeli elemzésekhez.  Az Azure HDInsight segítségével elvégezheti ezeket a szolgáltatásokat az Azure SQL DW számára.

Az Azure SQL Data Warehouse (SQL DW) egy analitikus számítási feladatokra optimalizált relációs adatbázis-tároló.  Az Azure SQL DW particionált táblák alapján skálázódik.  A táblák több csomópont között is particionálhatók.  Az Azure SQL DW-csomópontok vannak kiválasztva a létrehozás időpontjában.  A tény után skálázhatók, de ez egy aktív folyamat, amely adatmozgást igényelhet. További információ: [SQL Data Warehouse - Manage Compute](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Az Apache HBase az Azure HDInsight egyik kulcsérték-tárolója.  Az Apache HBase egy nyílt forráskódú NoSQL-adatbázis, amely a Hadoopra épült, és a Google BigTable után van modellezve. A HBase teljesítményvéletlenszerű hozzáférést és erős konzisztenciát biztosít nagy mennyiségű strukturálatlan és félstrukturált adathoz egy séma nélküli adatbázisban oszlopcsaládok szerint rendezve.

Az adatok a táblasorokban vannak tárolva, és a sorokon belüli adatok oszlopcsalád szerint vannak csoportosítva. A HBase egy séma nélküli adatbázis abban az értelemben, hogy az oszlopokat és a bennük tárolt adattípusokat sem kell meghatározni a használatuk előtt. A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. A HBase támaszkodhat az adatredundanciára, a kötegelt feldolgozásra és a Hadoop-ökoszisztémában elosztott alkalmazások által biztosított egyéb funkciókra.

A HBase kiváló hely az érzékelő- és naplóadatokhoz a későbbi elemzéshez.

A HBase méretezhetősége a HDInsight-fürt csomópontjainak számától függ.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL-adatbázis és Azure-adatbázis

Az Azure három különböző relációs adatbázist kínál platformszolgáltatásként (PAAS).

* [Az Azure SQL Database](../../sql-database/sql-database-technical-overview.md) a Microsoft SQL Server implementációja. A teljesítményről további információt az Azure SQL Database teljesítményének növelése című [témakörben talál.](../../sql-database/sql-database-performance-guidance.md)
* [Az Azure Database for MySQL](../../mysql/overview.md) az Oracle MySQL implementációja.
* [A PostgreSQL Azure Database](../../postgresql/quickstart-create-server-database-portal.md) a PostgreSQL implementációja.

Ezek a termékek felskálázhatók, ami azt jelenti, hogy több PROCESSZOR és memória hozzáadásával méretezhetők.  A jobb I/O-teljesítmény érdekében prémium szintű lemezeket is használhat a termékekkel.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Az Azure Analysis Services (AAS) egy analitikus adatmotor, amelyet a döntéstámogatásban és az üzleti elemzésben használnak, és amely az üzleti jelentések és az ügyfélalkalmazások , például a Power BI, az Excel, a Reporting Services-jelentések és egyéb adatok elemzési adatait tartalmazza vizualizációs eszközök.

Az elemzési kockák skálázhatók az egyes kockák szintjeinek módosításával.  További információ: [Azure Analysis Services Pricing](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Kivonat és betöltés

Miután az adatok léteznek az Azure-ban, számos szolgáltatás segítségével kinyerheti és betöltheti azadatokat más termékekbe.  A HDInsight támogatja a Sqoop és a Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Az Apache Sqoop egy olyan eszköz, amelyet arra terveztek, hogy hatékonyan továbbítsa az adatokat a strukturált, félig strukturált és strukturálatlan adatforrások között.

A Sqoop a MapReduce segítségével importálja és exportálja az adatokat, hogy párhuzamos működést és hibatűrést biztosítson.

### <a name="apache-flume"></a>Apache Fuvola

Az Apache Flume egy elosztott, megbízható és elérhető szolgáltatás a nagy mennyiségű naplóadatok hatékony gyűjtéséhez, összesítéséhez és mozgatásához. A Flume egyszerű és rugalmas architektúrával rendelkezik, amely a streamelési adatfolyamokon alapul. A Flume robusztus és hibatűrő, hangolható megbízhatósági mechanizmusokkal, valamint számos feladatátvételi és helyreállítási mechanizmussal. A Flume egy egyszerű bővíthető adatmodellt használ, amely lehetővé teszi az online analitikus alkalmazást.

Az Apache Flume nem használható az Azure HDInsight-mal.  A helyszíni Hadoop-telepítés a Flume használatával adatokat küldhet az Azure Storage Blobs vagy az Azure Data Lake Storage számára.  További információ: [Az Apache Flume használata a HDInsight segítségével című témakörben talál.](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/)

## <a name="transform"></a>Átalakítás

Ha az adatok a kiválasztott helyen léteznek, meg kell tisztítania, kombinálnia kell őket, vagy elő kell készítenie egy adott használati mintára.  A Hive, a Pig és a Spark SQL mind jó választás az ilyen jellegű munkához.  Mindegyik támogatott a HDInsight-on.

## <a name="next-steps"></a>További lépések

* [Az Apache Hive használata ETL eszközként](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Adatok áthelyezése az Azure SQL Database-ből az Apache Hive táblába](./apache-hadoop-use-sqoop-mac-linux.md)
