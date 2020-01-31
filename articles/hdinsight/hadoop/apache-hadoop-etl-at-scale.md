---
title: Kinyerés, átalakítás és betöltés (ETL) skálán – Azure HDInsight
description: Megtudhatja, hogyan használják a kinyerési, átalakítási és betöltési HDInsight a Apache Hadoop.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/27/2020
ms.openlocfilehash: f2c18a1e858fcebf8d2c82210f2290cf4a14d061
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846016"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Kinyerés, átalakítás és betöltés (ETL) skálán

A kinyerési, átalakítási és betöltési (ETL) folyamat során a rendszer a különböző forrásokból származó adatokat gyűjti össze, amelyeket standard helyen, megtisztítva és feldolgozva végez, és végső soron egy olyan adattárba tölt be, amelyről lekérdezhető. Az örökölt ETL-folyamatok importálják az adatimportálást, megtisztítják a helyet, majd egy rokon adatkezelőben tárolják őket. A HDInsight számos Apache Hadoop ökoszisztéma-összetevő támogatja az ETL méretezését.

A HDInsight az ETL-folyamatban való használatát a következő folyamat összegzi:

![HDInsight ETL – áttekintés](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

A következő szakaszok az ETL fázisok és a hozzájuk társított összetevők megismerését ismertetik.

## <a name="orchestration"></a>Adat-előkészítés

Az összehangolás az ETL-folyamat minden fázisában megível. A HDInsight-ben található ETL-feladatok gyakran több különböző, egymással együtt működő terméket is tartalmaznak.  A kaptár használatával törölheti az egyes adatrészeket, míg a Pig egy másik részt is megtisztít.  Előfordulhat, hogy a Azure Data Factory használatával tölti be az adatait a Azure Data Lake Store Azure SQL Databaseba.

A megfelelő feladatok a megfelelő időben történő futtatásához szükséges.

### <a name="apache-oozie"></a>Apache Oozie

Az Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli. A Oozie egy HDInsight-fürtön belül fut, és integrálva van a Hadoop-verembe. A Oozie támogatja a Apache Hadoop MapReduce, az Apache Pig, az Apache Hive és az Apache Sqoop Hadoop-feladatait. A Oozie a rendszerre jellemző feladatok, például a Java-programok vagy a rendszerhéj-parancsfájlok számára is ütemezhetők.

További információkért lásd: az [Apache Oozie és a Apache Hadoop használata munkafolyamatok definiálásához és futtatásához a HDInsight](../hdinsight-use-oozie-linux-mac.md) -ben, amely bemutatja, hogyan használható a Oozie a végpontok közötti folyamat továbbítására, lásd: [az adatfolyamatok működővé tenni](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory a szolgáltatásként nyújtott platformon alapuló funkciókat biztosít. Ez egy felhőalapú adatintegrációs szolgáltatás, amely lehetővé teszi, hogy adatvezérelt munkafolyamatokat hozzon létre a felhőben az adatáthelyezés és az adatátalakítás előkészítéséhez és automatizálásához.

A Azure Data Factory használatával a következőket teheti:

1. Olyan adatvezérelt munkafolyamatokat (folyamatokat) hozhat létre és ütemezhet, amelyek különböző adattárakból származó adatokkal rendelkeznek.
2. Az adatok feldolgozása és átalakítása számítási szolgáltatásokkal, például a Azure HDInsight Hadoop, a Spark, a Azure Data Lake Analytics, a Azure Batch és a Azure Machine Learning használatával.
3. A kimeneti adatokat olyan adattárakban teheti közzé, mint az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket.

A Azure Data Factoryről a [dokumentációban](../../data-factory/introduction.md)talál további információt.

## <a name="ingest-file-storage-and-result-storage"></a>Fájlok tárolási és eredményes tárterületének beolvasása

A forrásadatok fájljai általában az Azure Storage-ban vagy Azure Data Lake Storage-ban vannak betöltve. A fájlok bármilyen formátumban megadhatók, de általában egyszerű fájlok, például CSV.

### <a name="azure-storage"></a>Azure Storage

Az [Azure Storage](https://azure.microsoft.com/services/storage/blobs/) -ban konkrét skálázhatósági célok vannak. További információkért lásd [a blob Storage skálázhatósági és teljesítménybeli céljait](../../storage/blobs/scalability-targets.md)ismertető témakört. A legtöbb analitikus csomópont esetében az Azure Storage a legjobbat méretezi a sok kisebb fájl kezelésekor.  Az Azure Storage ugyanazt a teljesítményt biztosítja, függetlenül attól, hogy hány fájl vagy mekkora a fájl mérete (ha az Ön korlátain belül van).  Ez azt jelenti, hogy a terabájtos adatmennyiséget tárolhatja, és továbbra is konzisztens teljesítményt érhet el, függetlenül attól, hogy az adat egy részhalmazát vagy az összes adatát használja-e.

Az Azure Storage számos különböző típusú blobot tartalmaz.  A *hozzáfűző blob* nagyszerű lehetőség webnaplók vagy érzékelők-adattárolók tárolására.  

Több blob is elosztható több kiszolgáló között a hozzáférés kiméretezéséhez, de egyetlen blobot csak egyetlen kiszolgáló tud kiszolgálni. Míg a Blobok logikai csoportokba helyezhetők a blob-tárolókban, a csoportosítás nem érinti a particionálást.

Az Azure Storage egy WebHDFS API-réteget is tartalmaz a blob Storage-hoz.  A HDInsight összes szolgáltatása hozzáférhet az Azure Blob Storageban található fájlokhoz az adattisztításhoz és az adatfeldolgozáshoz, hasonlóan ahhoz, hogy ezek a szolgáltatások a Hadoop Distributed Files System (HDFS) szolgáltatást használják.

Az Azure Storage-ban általában a PowerShell, az Azure Storage SDK vagy a AZCopy használatával történik az adatgyűjtés.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

A Azure Data Lake Storage (ADLS) egy felügyelt, nagy kapacitású adattár, amely kompatibilis a HDFS-mel.  A ADLS a HDFS hasonló tervezési paradigmát használja, és korlátlan skálázhatóságot biztosít a teljes kapacitás és az egyes fájlok mérete tekintetében. A ADLS nagyon jó, ha nagyméretű fájlokkal dolgozik, mivel a nagyméretű fájlok több csomóponton is tárolhatók.  Az ADLS-ben végzett particionálás a színfalak mögött történik.  Kiemelkedő átviteli sebességet biztosít a több ezer párhuzamos végrehajtóval rendelkező elemzési feladatok futtatásához, amelyekben a több száz terabájt adat olvasása és írása hatékonyan történik.

Az adatfeldolgozás általában a ADLS Azure Data Factory, ADLS SDK-k, AdlCopy Service, Apache DistCp vagy Apache Sqoop használatával történik.  Ezeknek a szolgáltatásoknak a használata nagy mértékben függ attól, hogy az adatok hol vannak.  Ha az adathalmaz jelenleg egy meglévő Hadoop-fürtben található, akkor az Apache DistCp, a AdlCopy Service vagy a Azure Data Factory is használható.  Ha az Azure Blob Storageban van, használhat Azure Data Lake Storage .NET SDK-t, Azure PowerShellt vagy Azure Data Factoryt.

A ADLS az Azure Event hub vagy a Apache Storm használatával is optimalizált az események betöltésére.

#### <a name="considerations-for-both-storage-options"></a>Mindkét tárolási lehetőség szempontjai

Az adatkészletek terabájt tartományban való feltöltéséhez a hálózati késés lehet jelentős probléma, különösen akkor, ha az adatok egy helyszíni helyről érkeznek.  Ilyen esetekben az alábbi lehetőségeket használhatja:

* Azure ExpressRoute: az Azure ExpressRoute lehetővé teszi privát kapcsolatok létrehozását az Azure-adatközpontok és a helyszíni infrastruktúra között. Ezek a kapcsolatok megbízható lehetőséget biztosítanak nagy mennyiségű adatátvitelre. További információ: az [Azure ExpressRoute dokumentációja](../../expressroute/expressroute-introduction.md).

* "Kapcsolat nélküli" adatok feltöltése. Az [Azure import/export szolgáltatást](../../storage/common/storage-import-export-service.md) használhatja az Azure-adatközpontba felhasználható merevlemez-meghajtók továbbítására. Az adatait először feltölti az Azure Storage-Blobokra. Ezután a [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) vagy a [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) eszközzel másolhat adatokat az Azure Storage-blobokból a Data Lake Storageba.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Az Azure SQL DW remek választás a megtisztított és előkészített eredmények tárolására a jövőbeli elemzésekhez.  Az Azure HDInsight ezen szolgáltatások Azure SQL DW-hez való végrehajtásához használható.

A Azure SQL Data Warehouse (SQL DW) az analitikus számítási feladatokhoz optimalizált, összehasonlítható adatbázis-tároló.  Az Azure SQL DW méretezése particionált táblák alapján.  A táblák több csomópont között is particionálva lehetnek.  Az Azure SQL DW-csomópontok a létrehozáskor vannak kiválasztva.  Az is előfordulhat, hogy a tény után méretezhetők, de ez egy aktív folyamat, amely adatáthelyezést igényelhet. További információ: [SQL Data Warehouse – számítások kezelése](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Az Apache HBase egy kulcs-érték tároló, amely az Azure HDInsight érhető el.  Az Apache HBase egy nyílt forráskódú NoSQL-adatbázis, amely a Hadoopra épült, és a Google BigTable után van modellezve. A HBase nagy mennyiségű strukturálatlan és félig strukturált adatokhoz nyújt véletlenszerű hozzáférést és erős konzisztenciát az oszlopos családok által rendezett, séma nélküli adatbázisokban.

Az adatok a táblasorokban vannak tárolva, és a sorokon belüli adatok oszlopcsalád szerint vannak csoportosítva. A HBase egy séma nélküli adatbázis abban az értelemben, hogy az oszlopokat és a bennük tárolt adattípusokat sem kell meghatározni a használatuk előtt. A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. A HBase támaszkodhat az adatredundanciára, a kötegelt feldolgozásra és a Hadoop-ökoszisztémában található elosztott alkalmazások által biztosított egyéb funkciókra is.

A HBase kiváló hely az érzékelők és a naplózási adatai számára a jövőbeli elemzésekhez.

A HBase méretezhetősége a HDInsight-fürt csomópontjainak számától függ.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database és az Azure Database

Az Azure három különböző, a szolgáltatásként kínált platformként szolgáló kapcsolatot biztosít.

* A [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) Microsoft SQL Server implementációja. A teljesítményre vonatkozó további információkért lásd: [a teljesítmény finomhangolása Azure SQL Databaseban](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md) az Oracle MySQL implementációja.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) a PostgreSQL implementációja.

Ezek a termékek vertikális felskálázást jelentenek, ami azt jelenti, hogy több processzor és memória hozzáadásával méretezhetők.  Azt is megteheti, hogy prémium szintű lemezeket használ a termékekkel a jobb I/O-teljesítmény érdekében.

## <a name="azure-analysis-services"></a>Azure Analysis Services

A Azure Analysis Services (AAS) a döntési támogatásban és az üzleti elemzésben használt analitikai adatmotor, amely az üzleti jelentések és ügyfélalkalmazások, például a Power BI, az Excel, a Reporting Services-jelentések és egyéb adatszolgáltatások analitikus adatkezelőjét biztosítja. vizualizációs eszközök.

Az elemzési kockák méretezése az egyes kockák szintjeinek módosításával végezhető el.  További információ: [Azure Analysis Services díjszabása](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Kinyerés és betöltés

Ha az adatok már léteznek az Azure-ban, számos szolgáltatás használatával kinyerheti és betöltheti azokat más termékekbe.  A HDInsight támogatja a Sqoop és a Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Az Apache Sqoop egy olyan eszköz, amely a strukturált, részben strukturált és strukturálatlan adatforrások közötti adatátvitelhez készült.

A Sqoop a MapReduce használatával importálja és exportálja az adatgyűjtést, így párhuzamos működést és hibatűrést biztosít.

### <a name="apache-flume"></a>Apache Flume

Az Apache Flume egy elosztott, megbízható és elérhető szolgáltatás a nagy mennyiségű naplózási adatok hatékony gyűjtéséhez, összesítéséhez és áthelyezéséhez. A Flume egy egyszerű és rugalmas architektúra, amely a folyamatos átviteli folyamatokon alapul. A Flume robusztus és hibatűrő megbízhatósági mechanizmusokkal és számos feladatátvételi és helyreállítási mechanizmussal rendelkezik. A Flume egy egyszerű, bővíthető adatmodellt használ, amely lehetővé teszi az online analitikai alkalmazások használatát.

Az Apache Flume nem használható az Azure HDInsight.  A helyszíni Hadoop-telepítés a Flume használatával küldheti el az Azure Storage-blobokat vagy a Azure Data Lake Storage.  További információ: az [Apache Flume és a HDInsight használata](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Átalakítás

Miután az adatok megtalálhatók a kiválasztott helyen, meg kell tisztítania, össze kell állítania, vagy elő kell készítenie egy adott használati mintához.  A kaptár, a Pig és a Spark SQL minden jó választás az adott típusú munkához.  Ezek mind támogatottak a HDInsight.

## <a name="next-steps"></a>Következő lépések

* [Apache Hive használata ETL-eszközként](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Adatok áthelyezése Azure SQL Databaseból Apache Hive táblázatba](./apache-hadoop-use-sqoop-mac-linux.md)
