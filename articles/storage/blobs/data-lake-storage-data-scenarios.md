---
title: Azure Data Lake Storage Gen2 érintő forgatókönyvek |} A Microsoft Docs
description: Megismerheti a különböző forgatókönyveket és eszközök használatával, mely adatokat is betöltött, a feldolgozás, a letöltött és a webalkalmazásban jelennek meg a Data Lake Storage Gen2 (korábbi nevén Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.openlocfilehash: dfc47c40ce82eb8e9d414cb49c22bee033d000a6
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239028"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Az Azure Data Lake Storage Gen2 használata big data-követelményekhez

Big data-feldolgozáshoz négy fő szakaszból áll:

* Nagy mennyiségű adatot tölt be egy adattár, valós idejű, illetve kötegek formájában
* Az adatok feldolgozása
* Az adatok letöltése
* Az adatok megjelenítése

Ebben a cikkben áttekintjük garanciát az Azure Data Lake Storage Gen2 megtudhatja, hogy a beállítások és a big Data típusú adatok igény szerint elérhető eszközök szakaszokat.

## <a name="ingest-data-into-data-lake-storage-gen2"></a>Betölteni az adatokat az Data Lake Storage Gen2
Ez a szakasz kiemeli a különböző adatforrás és a különböző módokon, amelyben az adatok olvasódnak be egy Data Lake Storage Gen2-fiókot.

![Betölteni az adatokat az Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "betölteni az adatokat az Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad hoc adatok
Ez jelöli, amelyek kisebb adatkészletek használt prototípus-készítés big data-alkalmazásokat. Különböző módon ad-hoc adatok feldolgozására függően az adatok forrását.

| Adatforrás | Kigyűjtés használatával |
| --- | --- |
| Helyi számítógép |<ul> <li>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)</ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)</li> <li>[Az AzCopy-eszköz](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight-fürtön futó DistCp](data-lake-storage-use-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Streamelési adatok hangulatelemzéséhez
Vásárlói adatok hozhat létre különböző forrásokból, például az alkalmazások, eszközök, szenzorok, stb. Ezeket az adatokat az eszközök széles által olvasódnak be a Data Lake Storage Gen2. Ezek az eszközök általában fog rögzítése és feldolgozni az adatokat egy eseményt az esemény által történik, a valós idejű, majd írja be az események kötegekben Data Lake Storage Gen2 be, hogy ezek tovább dolgozhatók fel.

Eszközök, amelyekkel a következők:

* [Az Azure HDInsight Storm](../../hdinsight/storm/apache-storm-write-data-lake-store.md) -írhat adatokat közvetlenül a Data Lake Storage Gen2 a Storm-fürtből.

### <a name="relational-data"></a>Relációs adatok
A forrásadatok relációs adatbázisból. Egy időszakon belül a relációs adatbázisok gyűjtése hatalmas mennyiségű adatokat, ami a legfontosabb elemzéseket keresztül big Data jellegű adatok feldolgozása. A következő eszközök segítségével a Data Lake Storage Gen2 helyezhetik át az adatokat.

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web server naplóadatok (feltöltés használó egyéni alkalmazások)
Ez a fajta adatkészlet azért hívjuk fel, mert a web server naplóadatok elemzése egy gyakori alkalmazási helyzet a big data-alkalmazások és a Data Lake Storage Gen2-re kell feltölteni a naplófájlokat nagy mennyiségű kell. A következő eszközök bármelyikét használhatja saját parancsfájlok vagy alkalmazások töltse fel az adatokat írni.

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)

A web server naplózási adatok feltöltését, valamint is fel más típusú adatok (például közösségi hangulati adatok) egy jó módszer a saját egyéni parancsfájlok vagy alkalmazások írni, mert azt a rugalmasságot biztosít az adatok feltöltése összetevő részeként a nagyobb big data-alkalmazásokat. Bizonyos esetekben ez a kód is igénybe vehet egy parancsfájl vagy egyszerű parancssori segédprogrammal formájában. Más esetekben a kódot egy üzleti alkalmazás vagy megoldás big data-feldolgozáshoz integrálhatók használhatók.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtök kapcsolódó adatok
A legtöbb HDInsight-fürttípusok (Hadoop, HBase, Storm-) tároló adattár Data Lake Storage Gen2 támogatja. HDInsight-fürtök az Azure Storage Blobs (WASB) adatok eléréséhez. A jobb teljesítmény érdekében a Data Lake Storage Gen2-fiókra a fürthöz társított másolhatja az adatokat a WASB. A következő eszközök használatával másolja az adatokat.

* [Apache DistCp](data-lake-storage-use-distcp.md)
* [Az AzCopy-eszköz](../common/storage-use-azcopy-v10.md)
* [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Adatok tárolása a helyszíni vagy IaaS Hadoop fürtök
Nagy mennyiségű adat tárolható a meglévő Hadoop-fürtök helyi HDFS használó gépeken. A Hadoop-fürtök lehet egy helyszíni környezetben vagy egy IaaS-fürtöt az Azure-on belül. Az ilyen adatok másolása az Azure Data Lake Storage Gen2-re megközelítésre egyszeri vagy ismétlődő módon követelményeket is lehet. Különféle módon használhatja, ennek érdekében. Az alábbi, az alternatív megoldások és a kapcsolódó feláldozását listáját.

| A módszer | Részletek | Előnyök | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Az Azure Data Factory (ADF) használatával közvetlenül a Hadoop-fürtök adatok másolása az Azure Data Lake Storage Gen2-re |[Az ADF támogatja a HDFS adatforrásként](../../data-factory/connector-hdfs.md) |Az ADF HDFS és elsőrangú – teljes körű felügyeleti és figyelési-a-beépített támogatást nyújt a |Az adatkezelési átjáró üzembe helyezhető a helyszínen vagy az iaas-fürt szükséges |
| A Distcp használatával adatokat másol a Hadoop az Azure Storage. Ezután másolja az adatokat az Azure Storage Data Lake Storage Gen2-re megfelelő mechanizmussal. |A Data Lake Storage Gen2 használatával másolhat adatokat az Azure Storage-ból: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Az AzCopy-eszköz](../common/storage-use-azcopy-v10.md)</li><li>[A HDInsight-fürtökön futó Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |Nyílt forráskódú eszközöket is használhat. |Többlépéses folyamat, amely magában foglalja a több technológiák |

### <a name="really-large-datasets"></a>Nagyon nagy méretű adatkészletek
Több terabájt tartománya az adatkészletek fel, a fenti módszerek használatával néha lassú és költséges lehet. Ezekben az esetekben használhatja az alábbi beállítások közül.

* **Az Azure ExpressRoute használatával**. Az Azure ExpressRoute használatával magánkapcsolatok hozhatók létre az Azure-adatközpontok és olyan infrastruktúrák között a helyszínen. Ez nagy mennyiségű adat átvitelére megbízható lehetőséget kínál. További információkért lásd: [Azure ExpressRoute dokumentációja](../../expressroute/expressroute-introduction.md).

## <a name="process-data-stored-in-data-lake-storage-gen2"></a>A Data Lake Storage Gen2 tárolt adatok feldolgozása
Amint az adatok Data Lake Storage Gen2 elérhető analysis futtathatja az adatok a támogatott big data-alkalmazások használatával. Jelenleg használhatja az Azure HDInsight és az Azure Databricks a Data Lake Storage Gen2-ban tárolt adatokkal kapcsolatos adatok elemzési feladatok futtatásához.

![Adatok elemzése az Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "a Data Lake Storage Gen2 adatok elemzése")


## <a name="download-data-from-data-lake-storage-gen2"></a>Töltse le az adatokat a Data Lake Storage Gen2
Érdemes azt is, töltse le vagy adatok áthelyezése az Azure Data Lake Storage Gen2 forgatókönyvek, például:

* Adatok áthelyezése más tárházakban való kapcsolat a meglévő adatfeldolgozási folyamatok. Például érdemes Data Lake Storage Gen2 adatok áthelyezése az Azure SQL Database vagy a helyszíni SQL Server.
* Adatok letöltése a helyi számítógép prototípusok application készítése során IDE-környezetek feldolgozásra.

![Kimenő forgalom a Data Lake Storage Gen2 adatait](./media/data-lake-storage-data-scenarios/egress-data.png "kimenő forgalom a Data Lake Storage Gen2 adatait")

Ilyen esetben a következő lehetőségek bármelyikét használhatja:

* [Azure Data Factory](../../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-storage-use-distcp.md)

## <a name="visualize-data-in-data-lake-storage-gen2"></a>Adatok megjelenítése a Data Lake Storage Gen2
Szolgáltatások kombinációját használhatja létrehozása a Data Lake Storage Gen2-ban tárolt adatok vizuális ábrázolásai.

![Adatok megjelenítése a Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "adatok megjelenítése a Data Lake Storage Gen2")

* Első lépésként használatával [Azure Data Factoryben az adatok áthelyezése az Azure SQL Data Warehouse Data Lake Storage Gen2](../../data-factory/copy-activity-overview.md)
* Ezt követően is [Power BI integrálható az Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) hozhat létre az adatok vizuális megjelenítése.
