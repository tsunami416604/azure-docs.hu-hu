---
title: Az Azure Data Lake Storage Gen2-t érintő adatforgatókönyvek | Microsoft dokumentumok
description: Ismerje meg a különböző forgatókönyveket és eszközöket, amelyek segítségével az adatok at lehet betölteni, feldolgozott, letöltött és láthatóvá a Data Lake Storage Gen2 (korábbi nevén Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b0ebe6cb505fa2a145dd3cbb94398912f2933a4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77369708"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Az Azure Data Lake Storage Gen2 használata big data-követelményekhez

A big data feldolgozásának négy fő szakasza van:

> [!div class="checklist"]
> * Nagy mennyiségű adat betöltése az adattárba valós időben vagy kötegekben
> * Az adatok feldolgozása
> * Az adatok letöltése
> * Az adatok megjelenítése

Ez a cikk kiemeli az egyes feldolgozási fázisok lehetőségeit és eszközeit.

Az Azure Data Lake Storage Gen2 szolgáltatással használható Azure-szolgáltatások teljes listáját az [Azure Data Lake Storage integrálása az Azure-szolgáltatásokkal](data-lake-storage-integrate-with-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Az adatok betöltése a Data Lake Storage Gen2-be

Ez a szakasz kiemeli a különböző adatforrások és a különböző módon, amelyben az adatok at lehet betektengetni egy Data Lake Storage Gen2 fiókba.

![Adatok betöltése a Data Lake Storage Gen2 szolgáltatásba](./media/data-lake-storage-data-scenarios/ingest-data.png "Adatok betöltése a Data Lake Storage Gen2 szolgáltatásba")

### <a name="ad-hoc-data"></a>Ad hoc adatok

Ez kisebb adatkészleteket jelent, amelyek big data-alkalmazások prototípus-beírásához használatosak. Az ad hoc adatok betöltésének különböző módjai vannak az adatok forrásától függően. 

Az alábbiakban az ad hoc adatok betöltéséhez használható eszközök listáját tartalmazza.

| Adatforrás | Betöltés a használatával |
| --- | --- |
| Helyi számítógép |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure CLI](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy eszköz](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure-adatgyár](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy eszköz](../common/storage-use-azcopy-v10.md)<br><br>[HDInsight-fürtön futó DistCp](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Streamelt adatok

Ez olyan adatokat jelöl, amelyeket különböző forrásokból, például alkalmazásokból, eszközökből, érzékelőkből stb. Ezeket az adatokat számos eszköz használható a Data Lake Storage Gen2-be. Ezek az eszközök általában rögzítik és feldolgozzák az adatokat eseményenként valós időben, majd az eseményeket kötegekben a Data Lake Storage Gen2-be, hogy azok tovább dolgozhatók legyenek.

Az alábbiakban az átszerkesztett adatok betöltéséhez használható eszközök listáját tartalmazza.

|Eszköz | Útmutatás |
|---|--|
|Azure Stream Analytics|[Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Kilépés az Azure Data Lake Gen2-hez](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Azure HDInsight Storm | [Írjon az Apache Hadoop HDFS-nek az Apache Storm ról a HDInsight-on](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relációs adatok

Az adatokat relációs adatbázisokból is beszerezheti. Egy ideig a relációs adatbázisok hatalmas mennyiségű adatot gyűjtenek, amelyek kulcsfontosságú elemzéseket biztosíthatnak, ha big data-folyamaton keresztül dolgozzák fel. A következő eszközökkel helyezheti át ezeket az adatokat a Data Lake Storage Gen2-be.

Az alábbiakban a relációs adatok betöltéséhez használható eszközök listáját tartalmazza.

|Eszköz | Útmutatás |
|---|--|
|Azure Data Factory | [Másolási tevékenység az Azure Data Factoryben](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webkiszolgáló naplóadatai (feltöltés egyéni alkalmazásokkal)

Az ilyen típusú adatkészletkifejezetten azért van kihívva, mert a webkiszolgálónapló-adatok elemzése gyakori használati eset a big data-alkalmazások számára, és nagy mennyiségű naplófájlt kell feltölteni a Data Lake Storage Gen2-be. Az alábbi eszközök bármelyikével saját parancsfájlokat vagy alkalmazásokat írhat az ilyen adatok feltöltéséhez.

Az alábbiakban a webkiszolgáló naplóadatainak betöltéséhez használható eszközök listáját tartalmazza.

|Eszköz | Útmutatás |
|---|--|
|Azure Data Factory | [Másolási tevékenység az Azure Data Factoryben](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |
|Azure CLI|[Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

A webszerver naplóadatainak feltöltéséhez, valamint más típusú adatok (pl. közösségi érzelmek adatainak) feltöltéséhez jó megközelítés saját egyéni parancsfájlok/alkalmazások írása, mivel rugalmasságot biztosít az adatfeltöltési összetevőnek a nagyobb big data alkalmazás részeként történő felvételéhez. Bizonyos esetekben ez a kód parancsfájl vagy egyszerű parancssori segédprogram formáját öltheti. Más esetekben a kód használható a big data-feldolgozás üzleti alkalmazásba vagy megoldásba történő integrálására.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtökhöz társított adatok

A legtöbb HDInsight-fürttípus (Hadoop, HBase, Storm) támogatja a Data Lake Storage Gen2 adattárként való tárolását. A HDInsight-fürtök az Azure Storage Blobs (WASB) adataihoz férnek hozzá. A jobb teljesítmény érdekében a WASB-ból származó adatokat a fürthöz társított Data Lake Storage Gen2 fiókba másolhatja. Az adatok másolásához a következő eszközök kelthet.

Az alábbiakban a HDInsight-fürtökhöz társított adatok betöltéséhez használható eszközök listáját tartalmazza.

|Eszköz | Útmutatás |
|---|--|
|Apache DistCp | [Adatok másolása az Azure Storage Blobs és az Azure Data Lake Storage Gen2 között a DistCp használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|AzCopy eszköz | [Adatok átvitele az AzCopy programmal](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Adatok másolása az Azure Data Lake Storage Gen2 szolgáltatásba vagy onnan az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>A helyszíni vagy IaaS Hadoop-fürtökben tárolt adatok

Nagy mennyiségű adat tárolható a meglévő Hadoop-fürtökben, helyileg hdfs-t használó gépeken. A Hadoop-fürtök lehetnek helyszíni telepítésben, vagy egy IaaS-fürtön belül az Azure-ban. Előfordulhat, hogy az ilyen adatok másolása az Azure Data Lake Storage Gen2 egy egyszeri megközelítés, vagy ismétlődő módon. Vannak különböző lehetőségek, amelyek segítségével ennek elérése érdekében. Az alábbiakban felsoroljuk az alternatívákat és a kapcsolódó kompromisszumokat.

| Módszer | Részletek | Előnyök | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Az Azure Data Factory (ADF) használatával közvetlenül a Hadoop-fürtökből másolhat adatokat az Azure Data Lake Storage Gen2 szolgáltatásba |[Az ADF támogatja a HDFS adatforrásként való](../../data-factory/connector-hdfs.md) |Az ADF beépített támogatást nyújt a HDFS és az első osztályú teljes körű felügyelet hez és felügyelethez |Adatkezelési átjáró helyszíni vagy Az IaaS-fürtben való üzembe helyezéséhez szükséges |
| A Distcp használatával adatokat másolhat a Hadoopból az Azure Storage-ba. Ezután másolja az adatokat az Azure Storage-ból a Data Lake Storage Gen2-be a megfelelő mechanizmus használatával. |Az Azure Storage-ból adatokat másolhat a Data Lake Storage Gen2 szolgáltatásba a következő használatával: <ul><li>[Azure-adatgyár](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy eszköz](../common/storage-use-azcopy-v10.md)</li><li>[A HDInsight-fürtökön futó Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |Használhat nyílt forráskódú eszközöket. |Többlépcsős folyamat, amely több technológiát foglal magában |

### <a name="really-large-datasets"></a>Igazán nagy adatkészletek

Több terabájtban terjedő adatkészletek feltöltéséhez a fent leírt módszerek használata néha lassú és költséges lehet. Ilyen esetekben használhatja az Azure ExpressRoute.In such cases, you can use Azure ExpressRoute.  

Az Azure ExpressRoute lehetővé teszi, hogy privát kapcsolatokat hozzon létre az Azure-adatközpontok és az infrastruktúra között a telephelyén. Ez megbízható lehetőséget biztosít nagy mennyiségű adat átvitelére. További információ: [Azure ExpressRoute dokumentáció.](../../expressroute/expressroute-introduction.md)

## <a name="process-the-data"></a>Az adatok feldolgozása

Miután az adatok elérhetővé a Data Lake Storage Gen2 futtathatja az adatok elemzése a támogatott big data-alkalmazások használatával. 

![Adatok elemzése a Data Lake Storage Gen2 szolgáltatásban](./media/data-lake-storage-data-scenarios/analyze-data.png "Adatok elemzése a Data Lake Storage Gen2 szolgáltatásban")

Az alábbiakban azokat az eszközöket sorolja fel, amelyekkel adatelemzési feladatokat futtathat a Data Lake Storage Gen2-ben tárolt adatokon.

|Eszköz | Útmutatás |
|---|--|
|Azure HDInsight | [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [2. generációs Azure Data Lake Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Rövid útmutató: Adatok elemzése az Azure Data Lake Storage Gen2 szolgáltatásban az Azure Databricks használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Az adatok vizualizációja

A Power BI-összekötő vel vizuális anammára jelenítheti meg a Data Lake Storage Gen2 szolgáltatásban tárolt adatokat. Lásd: [Adatok elemzése az Azure Data Lake Storage Gen2 szolgáltatásban a Power BI használatával.](https://docs.microsoft.com/power-query/connectors/datalakestorage)

## <a name="download-the-data"></a>Az adatok letöltése

Előfordulhat, hogy az Azure Data Lake Storage Gen2-ből is szeretne adatokat letölteni vagy áthelyezni olyan forgatókönyvek esetén, mint például:

* Helyezze át az adatokat más adattárakba, hogy kapcsolatba lépjen a meglévő adatfeldolgozási folyamatokkal. Előfordulhat például, hogy adatokat szeretne áthelyezni a Data Lake Storage Gen2-ből az Azure SQL Database vagy a helyszíni SQL Server szolgáltatásba.

* Töltse le az adatokat a helyi számítógépre az IDE-környezetekben történő feldolgozáshoz az alkalmazás prototípusainak készítése közben.

![A Data Lake Storage Gen2 kimenő adatai](./media/data-lake-storage-data-scenarios/egress-data.png "A Data Lake Storage Gen2 kimenő adatai")

Az alábbiakban azokat az eszközöket tartalmazza, amelyekkel adatokat tölthet le a Data Lake Storage Gen2 szolgáltatásból.

|Eszköz | Útmutatás |
|---|--|
|Azure Data Factory | [Másolási tevékenység az Azure Data Factoryben](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Adatok másolása az Azure Storage Blobs és az Azure Data Lake Storage Gen2 között a DistCp használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Azure Storage Explorer|[Az Azure Storage Explorer használatával kezelheti a könyvtárakat, fájlokat és ACL-eket az Azure Data Lake Storage Gen2 szolgáltatásban](data-lake-storage-explorer.md)|
|AzCopy eszköz|[Adatok átvitele az AzCopy és blob tárhellyel](../common/storage-use-azcopy-blobs.md)|
