---
title: A Data Lake Storage Gen1-et érintő adatforgatókönyvek | Microsoft dokumentumok
description: Ismerje meg a különböző forgatókönyveket és eszközöket, amelyek segítségével az adatok at lehet betölteni, feldolgozott, letöltött és láthatóvá a Data Lake Storage Gen1 (korábbi nevén Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: a8f9b66bf9a301888f2371fb1c58a4845c2232b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536156"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Az Azure Data Lake Storage Gen1 használata big data-követelményekhez

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

A big data feldolgozásának négy fő szakasza van:

* Nagy mennyiségű adat betöltése az adattárba valós időben vagy kötegekben
* Az adatok feldolgozása
* Az adatok letöltése
* Az adatok megjelenítése

Ebben a cikkben az Azure Data Lake Storage Gen1-hez kapcsolódó szakaszokat tekintjük, hogy megismerjük a big data-igények kielégítéséhez rendelkezésre álló lehetőségeket és eszközöket.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Adatok betöltése a Data Lake Storage Gen1 szolgáltatásba
Ez a szakasz kiemeli a különböző adatforrások és a különböző módon, amelyben az adatok at lehet betektengetni egy Data Lake Storage Gen1 fiókba.

![Adatok betöltése a Data Lake Storage Gen1 szolgáltatásba](./media/data-lake-store-data-scenarios/ingest-data.png "Adatok betöltése a Data Lake Storage Gen1 szolgáltatásba")

### <a name="ad-hoc-data"></a>Ad hoc adatok
Ez kisebb adatkészleteket jelent, amelyek big data-alkalmazások prototípus-beírásához használatosak. Az ad hoc adatok betöltésének különböző módjai vannak az adatok forrásától függően.

| Adatforrás | Betöltés a használatával |
| --- | --- |
| Helyi számítógép |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[A Data Lake eszközök használata a Visual Studio számára](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure-adatgyár](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight-fürtön futó DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Streamelt adatok
Ez olyan adatokat jelöl, amelyeket különböző forrásokból, például alkalmazásokból, eszközökből, érzékelőkből stb. Ezeket az adatokat számos eszköz használható a Data Lake Storage Gen1-be. Ezek az eszközök általában rögzítik és feldolgozzák az adatokat eseményenként valós időben, majd az eseményeket kötegekben a Data Lake Storage Gen1-be, hogy azok tovább dolgozhatók legyenek.

A következőkben használható eszközök:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) – Az Event Hubsba bevitt események az Azure Data Lake Storage Gen1 szolgáltatásba írhatók egy Azure Data Lake Storage Gen1 kimenet használatával.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) – Adatokat írhat közvetlenül a Data Lake Storage Gen1 a Storm-fürtből.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – Eseményeket fogadhat az Event Hubs-ból, majd írhat a Data Lake Storage Gen1 szolgáltatásba a [Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)használatával.

### <a name="relational-data"></a>Relációs adatok
Az adatokat relációs adatbázisokból is beszerezheti. Egy ideig a relációs adatbázisok hatalmas mennyiségű adatot gyűjtenek, amelyek kulcsfontosságú elemzéseket biztosíthatnak, ha big data-folyamaton keresztül dolgozzák fel. A következő eszközökkel helyezheti át ezeket az adatokat a Data Lake Storage Gen1-be.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure-adatgyár](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webkiszolgáló naplóadatai (feltöltés egyéni alkalmazásokkal)
Az ilyen típusú adatkészletkifejezetten azért van kihívva, mert a webkiszolgálónapló-adatok elemzése gyakori használati eset a big data-alkalmazások számára, és nagy mennyiségű naplófájlt kell feltölteni a Data Lake Storage Gen1-be. Az alábbi eszközök bármelyikével saját parancsfájlokat vagy alkalmazásokat írhat az ilyen adatok feltöltéséhez.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure-adatgyár](../data-factory/copy-activity-overview.md)

A webszerver naplóadatainak feltöltéséhez, valamint más típusú adatok (pl. közösségi érzelmek adatainak) feltöltéséhez jó megközelítés saját egyéni parancsfájlok/alkalmazások írása, mivel rugalmasságot biztosít az adatfeltöltési összetevőnek a nagyobb big data alkalmazás részeként történő felvételéhez. Bizonyos esetekben ez a kód parancsfájl vagy egyszerű parancssori segédprogram formáját öltheti. Más esetekben a kód használható a big data-feldolgozás üzleti alkalmazásba vagy megoldásba történő integrálására.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtökhöz társított adatok
A legtöbb HDInsight-fürttípus (Hadoop, HBase, Storm) támogatja a Data Lake Storage Gen1 adattárházat. A HDInsight-fürtök az Azure Storage Blobs (WASB) adataihoz férnek hozzá. A jobb teljesítmény érdekében a WASB-ból származó adatokat a fürthöz társított Data Lake Storage Gen1 fiókba másolhatja. Az adatok másolásához a következő eszközök kelthet.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy szolgáltatás](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure-adatgyár](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>A helyszíni vagy IaaS Hadoop-fürtökben tárolt adatok
Nagy mennyiségű adat tárolható a meglévő Hadoop-fürtökben, helyileg hdfs-t használó gépeken. A Hadoop-fürtök lehetnek helyszíni telepítésben, vagy egy IaaS-fürtön belül az Azure-ban. Előfordulhat, hogy az ilyen adatok másolása az Azure Data Lake Storage Gen1 egyszeri megközelítés, vagy ismétlődő módon. Vannak különböző lehetőségek, amelyek segítségével ennek elérése érdekében. Az alábbiakban felsoroljuk az alternatívákat és a kapcsolódó kompromisszumokat.

| Módszer | Részletek | Előnyök | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Az Azure Data Factory (ADF) használatával közvetlenül a Hadoop-fürtökből másolhat adatokat az Azure Data Lake Storage Gen1 szolgáltatásba |[Az ADF támogatja a HDFS adatforrásként való](../data-factory/connector-hdfs.md) |Az ADF beépített támogatást nyújt a HDFS és az első osztályú teljes körű felügyelet hez és felügyelethez |Adatkezelési átjáró helyszíni vagy Az IaaS-fürtben való üzembe helyezéséhez szükséges |
| Adatok exportálása a Hadoopból fájlként. Ezután másolja a fájlokat az Azure Data Lake Storage Gen1 megfelelő mechanizmus sal. |Fájlokat másolhat az Azure Data Lake Storage Gen1 szolgáltatásba a következő használatával: <ul><li>[Azure PowerShell Windows operációs rendszerhez](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Egyéni alkalmazás bármely Data Lake Storage Gen1 SDK használatával</li></ul> |Gyorsan elkezdeni. Meg tudja csinálni testreszabott feltöltések |Többlépéses folyamat, amely több technológiát foglal magában. A vezetés és a nyomon követés idővel kihívássá válik, tekintettel az eszközök |
| A Distcp használatával adatokat másolhat a Hadoopból az Azure Storage-ba. Ezután másolja az adatokat az Azure Storage-ból a Data Lake Storage Gen1-be a megfelelő mechanizmus használatával. |Az Azure Storage szolgáltatásból adatokat másolhat a Data Lake Storage Gen1 szolgáltatásba a következő használatával: <ul><li>[Azure-adatgyár](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[A HDInsight-fürtökön futó Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Használhat nyílt forráskódú eszközöket. |Többlépcsős folyamat, amely több technológiát foglal magában |

### <a name="really-large-datasets"></a>Igazán nagy adatkészletek
Több terabájtban terjedő adatkészletek feltöltéséhez a fent leírt módszerek használata néha lassú és költséges lehet. Ilyen esetekben használhatja az alábbi lehetőségeket.

* **Az Azure ExpressRoute használata.** Az Azure ExpressRoute lehetővé teszi, hogy privát kapcsolatokat hozzon létre az Azure-adatközpontok és az infrastruktúra között a telephelyén. Ez megbízható lehetőséget biztosít nagy mennyiségű adat átvitelére. További információt az [Azure ExpressRoute dokumentációjában](../expressroute/expressroute-introduction.md)talál.
* **"Offline" adatok feltöltése**. Ha az Azure ExpressRoute használata bármilyen okból nem valósítható meg, az [Azure importálási/exportálási szolgáltatásával](../storage/common/storage-import-export-service.md) merevlemez-meghajtókat szállíthat az adatokkal egy Azure-adatközpontba. Az adatok először az Azure Storage Blobs-ba töltődnek fel. Ezután az [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) vagy az [AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md) segítségével adatokat másolhat az Azure Storage Blobs ból a Data Lake Storage Gen1-be.

  > [!NOTE]
  > Az Importálás/exportálás szolgáltatás használata közben az Azure adatközpontba szállítandó lemezeken lévő fájlméretek nem lehetnek 195 GB-nál nagyobbak.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>A Data Lake Storage Gen1 szolgáltatásban tárolt adatok feldolgozása
Miután az adatok elérhetővé a Data Lake Storage Gen1 futtathatja az adatok elemzése a támogatott big data-alkalmazások használatával. Jelenleg az Azure HDInsight és az Azure Data Lake Analytics segítségével adatelemzési feladatokat futtathat a Data Lake Storage Gen1-ben tárolt adatokon.

![Adatok elemzése a Data Lake Storage Gen1 szolgáltatásban](./media/data-lake-store-data-scenarios/analyze-data.png "Adatok elemzése a Data Lake Storage Gen1 szolgáltatásban")

Tekintse meg az alábbi példákat.

* [HDInsight-fürt létrehozása a Data Lake Storage Gen1 tárolóként](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1 szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Adatok letöltése a Data Lake Storage Gen1 szolgáltatásból
Előfordulhat, hogy az Azure Data Lake Storage Gen1 szolgáltatásból is szeretne adatokat letölteni vagy áthelyezni olyan forgatókönyvek esetén, mint például:

* Helyezze át az adatokat más adattárakba, hogy kapcsolatba lépjen a meglévő adatfeldolgozási folyamatokkal. Előfordulhat például, hogy adatokat szeretne áthelyezni a Data Lake Storage Gen1-ből az Azure SQL Database vagy a helyszíni SQL Server szolgáltatásba.
* Töltse le az adatokat a helyi számítógépre az IDE-környezetekben történő feldolgozáshoz az alkalmazás prototípusainak készítése közben.

![A Data Lake Storage Gen1 kimenő adatai](./media/data-lake-store-data-scenarios/egress-data.png "A Data Lake Storage Gen1 kimenő adatai")

Ilyen esetekben az alábbi lehetőségek bármelyikét használhatja:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure-adatgyár](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

A következő módszerekkel is írhat saját parancsfájlt/alkalmazást a Data Lake Storage Gen1 adatainak letöltéséhez.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Adatok megjelenítése a Data Lake Storage Gen1 szolgáltatásban
A szolgáltatások vegyesen hozhat létre vizuális ábrázolásokat a Data Lake Storage Gen1-ben tárolt adatokról.

![Adatok megjelenítése a Data Lake Storage Gen1 szolgáltatásban](./media/data-lake-store-data-scenarios/visualize-data.png "Adatok megjelenítése a Data Lake Storage Gen1 szolgáltatásban")

* Az Azure Data Factory használatával [áthelyezheti az adatokat a Data Lake Storage Gen1-ről az Azure SQL Data Warehouse-ba](../data-factory/copy-activity-overview.md)
* Ezt követően [integrálhatja a Power BI-t az Azure SQL Data Warehouse-ral,](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) hogy vizuálisan ábrázolhassa az adatokat.
