---
title: Data Lake Storage Gen1 érintő forgatókönyvek |} A Microsoft Docs
description: Megismerheti a különböző forgatókönyveket és eszközök használatával, mely adatokat is betöltött, a feldolgozás, a letöltött és a webalkalmazásban jelennek meg a Data Lake Storage Gen1 (korábbi nevén Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0b16154edbda4bedfd4e9b680ba4311e7a235212
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878757"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Az Azure Data Lake Storage Gen1 használata big data-követelményekhez

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Big data-feldolgozáshoz négy fő szakaszból áll:

* Nagy mennyiségű adatot tölt be egy adattár, valós idejű, illetve kötegek formájában
* Az adatok feldolgozása
* Az adatok letöltése
* Az adatok megjelenítése

Ebben a cikkben áttekintjük az Azure Data Lake Storage Gen1 megtudhatja, hogy a beállítások és a big Data típusú adatok igény szerint elérhető eszközök megállapodást szakaszokat.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Betölteni az adatokat az Data Lake Storage Gen1
Ez a szakasz kiemeli a különböző adatforrás és a különböző módokon, amelyben az adatok olvasódnak be egy Data Lake Storage Gen1 fiókot.

![Betölteni az adatokat az Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "betölteni az adatokat az Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Ad hoc adatok
Ez jelöli, amelyek kisebb adatkészletek használt prototípus-készítés big data-alkalmazásokat. Különböző módon ad-hoc adatok feldolgozására függően az adatok forrását.

| Adatforrás | Kigyűjtés használatával |
| --- | --- |
| Helyi számítógép |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[A Data Lake Tools for Visual Studio használatával](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Az AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight-fürtön futó DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Streamelési adatok hangulatelemzéséhez
Vásárlói adatok hozhat létre különböző forrásokból, például az alkalmazások, eszközök, szenzorok, stb. Ezeket az adatokat az eszközök széles által olvasódnak be a Data Lake Storage Gen1. Ezek az eszközök általában fog rögzítése és feldolgozni az adatokat egy eseményt az esemény által történik, a valós idejű, majd írja be az események kötegekben Data Lake Storage Gen1 be, hogy ezek tovább dolgozhatók fel.

Eszközök, amelyekkel a következők:

* [Az Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) -események Event hubsba betöltött szolgáltatásba lehet írni az Azure Data Lake Storage Gen1 használatával egy Azure Data Lake Storage Gen1 kimenetet.
* [Az Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) -írhat adatokat közvetlenül a Data Lake Storage Gen1, a Storm-fürt.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – események fogadása az Event Hubs, és ezután a Data Lake Storage Gen1 használatával írja a [Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relációs adatok
A forrásadatok relációs adatbázisból. Egy időszakon belül a relációs adatbázisok gyűjtése hatalmas mennyiségű adatokat, ami a legfontosabb elemzéseket keresztül big Data jellegű adatok feldolgozása. A következő eszközök segítségével a Data Lake Storage Gen1 helyezhetik át az adatokat.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web server naplóadatok (feltöltés használó egyéni alkalmazások)
Ez a fajta adatkészlet azért hívjuk fel, mert a web server naplóadatok elemzése egy gyakori alkalmazási helyzet a big data-alkalmazások és a Data Lake Storage Gen1 fel kell tölteni a naplófájlok nagy mennyiségű kell. A következő eszközök bármelyikét használhatja saját parancsfájlok vagy alkalmazások töltse fel az adatokat írni.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

A web server naplózási adatok feltöltését, valamint is fel más típusú adatok (például közösségi hangulati adatok) egy jó módszer a saját egyéni parancsfájlok vagy alkalmazások írni, mert azt a rugalmasságot biztosít az adatok feltöltése összetevő részeként a nagyobb big data-alkalmazásokat. Bizonyos esetekben ez a kód is igénybe vehet egy parancsfájl vagy egyszerű parancssori segédprogrammal formájában. Más esetekben a kódot egy üzleti alkalmazás vagy megoldás big data-feldolgozáshoz integrálhatók használhatók.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtök kapcsolódó adatok
A legtöbb HDInsight-fürttípusok (Hadoop, HBase, Storm-) tároló adattár Data Lake Storage Gen1 támogatja. HDInsight-fürtök az Azure Storage Blobs (WASB) adatok eléréséhez. A jobb teljesítmény érdekében a egy Data Lake Storage Gen1 figyelembe, hogy a fürthöz társított másolhatja az adatokat a WASB. A következő eszközök használatával másolja az adatokat.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy Service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Adatok tárolása a helyszíni vagy IaaS Hadoop fürtök
Nagy mennyiségű adat tárolható a meglévő Hadoop-fürtök helyi HDFS használó gépeken. A Hadoop-fürtök lehet egy helyszíni környezetben vagy egy IaaS-fürtöt az Azure-on belül. Az ilyen adatok másolása az Azure Data Lake Storage Gen1 megközelítésre egyszeri vagy ismétlődő módon követelményeket is lehet. Különféle módon használhatja, ennek érdekében. Az alábbi, az alternatív megoldások és a kapcsolódó feláldozását listáját.

| A módszer | Részletek | Előnyök | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Adatok másolása az Azure Data Lake Storage Gen1 közvetlenül a Hadoop-fürtök az Azure Data Factory (ADF) használatával |[Az ADF támogatja a HDFS adatforrásként](../data-factory/connector-hdfs.md) |Az ADF HDFS és elsőrangú – teljes körű felügyeleti és figyelési-a-beépített támogatást nyújt a |Az adatkezelési átjáró üzembe helyezhető a helyszínen vagy az iaas-fürt szükséges |
| Adatok exportálása a Hadoop-fájlok formájában. Ezután másolja a fájlokat, és az Azure Data Lake Storage Gen1 megfelelő mechanizmussal. |Az Azure Data Lake Storage Gen1 használatával másolja a fájlokat: <ul><li>[Az Azure PowerShell, a Windows operációs rendszer](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Minden Data Lake Storage Gen1 SDK használatával egyéni alkalmazás</li></ul> |A gyors kezdéshez. Testre szabott feltöltések hajthatja végre. |Többlépéses folyamat, amely magában foglalja a több technológiákat. Felügyeleti és monitorozási növekszik, nagy kihívást jelent az eszközök testreszabott természetéből idővel |
| A Distcp használatával adatokat másol a Hadoop az Azure Storage. Ezután másolja az adatokat az Azure Storage a Data Lake Storage Gen1 megfelelő mechanizmussal. |A Data Lake Storage Gen1 használatával másolhat adatokat az Azure Storage-ból: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Az AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[A HDInsight-fürtökön futó Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Nyílt forráskódú eszközöket is használhat. |Többlépéses folyamat, amely magában foglalja a több technológiák |

### <a name="really-large-datasets"></a>Nagyon nagy méretű adatkészletek
Több terabájt tartománya az adatkészletek fel, a fenti módszerek használatával néha lassú és költséges lehet. Ezekben az esetekben használhatja az alábbi beállítások közül.

* **Az Azure ExpressRoute használatával**. Az Azure ExpressRoute használatával magánkapcsolatok hozhatók létre az Azure-adatközpontok és olyan infrastruktúrák között a helyszínen. Ez nagy mennyiségű adat átvitelére megbízható lehetőséget kínál. További információkért lásd: [Azure ExpressRoute dokumentációja](../expressroute/expressroute-introduction.md).
* **"Offline állapotba helyezi" az adatok feltöltése**. Ha az Azure ExpressRoute használata nem megvalósítható bármilyen okból, [Azure Import/Export szolgáltatás](../storage/common/storage-import-export-service.md) tehetnek a merevlemez-meghajtók egy Azure-adatközpontban található adatait. Az adatfeltöltés először az Azure Storage-blobokat. Ezután [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) vagy [AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md) az adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1.

  > [!NOTE]
  > Bár az Import/Export szolgáltatással, a méretűek, a lemezeken, amelyek az Azure-adatközpontban történő nem lehet nagyobb, mint 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>A Data Lake Storage Gen1 tárolt adatok feldolgozása
Amint az adatok Data Lake Storage Gen1 elérhető analysis futtathatja az adatok a támogatott big data-alkalmazások használatával. Jelenleg használhatja az Azure HDInsight és az Azure Data Lake Analytics a Data Lake Storage Gen1-ban tárolt adatokkal kapcsolatos adatok elemzési feladatok futtatásához.

![Adatok elemzése az Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "adatok elemzése az Data Lake Storage Gen1")

Tekintse meg az alábbi példák.

* [Hozzon létre egy HDInsight-fürt tárolójaként a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Töltse le az adatokat a Data Lake Storage Gen1
Érdemes azt is, töltse le vagy adatok áthelyezése az Azure Data Lake Storage Gen1 forgatókönyvek, például:

* Adatok áthelyezése más tárházakban való kapcsolat a meglévő adatfeldolgozási folyamatok. Például érdemes Data Lake Storage Gen1 adatok áthelyezése az Azure SQL Database vagy a helyszíni SQL Server.
* Adatok letöltése a helyi számítógép prototípusok application készítése során IDE-környezetek feldolgozásra.

![Kimenő forgalom a Data Lake Storage Gen1 adatait](./media/data-lake-store-data-scenarios/egress-data.png "kimenő forgalom a Data Lake Storage Gen1 adatait")

Ilyen esetben a következő lehetőségek bármelyikét használhatja:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Az alábbi módszerek használatával írja a saját adatokat letölteni a Data Lake Storage Gen1 parancsfájl/alkalmazást.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Adatok megjelenítése a Data Lake Storage Gen1
Szolgáltatások kombinációját használhatja létrehozása a Data Lake Storage Gen1 tárolt adatok vizuális ábrázolásai.

![Adatok megjelenítése a Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "adatok megjelenítése a Data Lake Storage Gen1")

* Első lépésként használatával [Azure Data Factoryben az adatok áthelyezése az Azure SQL Data Warehouse Data Lake Storage Gen1](../data-factory/copy-activity-overview.md)
* Ezt követően is [Power BI integrálható az Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) hozhat létre az adatok vizuális megjelenítése.
