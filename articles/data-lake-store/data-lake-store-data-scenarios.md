---
title: Data Lake Storage Gen1 érintő forgatókönyvek |} A Microsoft Docs
description: Megismerheti a különböző forgatókönyveket és eszközök használatával, mely adatokat is betöltött, a feldolgozás, a letöltött és a webalkalmazásban jelennek meg a Data Lake Storage Gen1 (korábbi nevén Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 19743dcd2866b8fc7b6ad1fdf387b134f7b3ca50
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783076"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Az Azure Data Lake Storage Gen1 használata big data-követelményekhez

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Big data-feldolgozáshoz négy fő szakaszból áll:

* Nagy mennyiségű adatot tölt be egy adattár, valós idejű, illetve kötegek formájában
* Az adatok feldolgozása
* Az adatok letöltése
* Az adatok megjelenítése

Ebben a cikkben áttekintjük garanciát az Azure Data Lake Store megtudhatja, hogy a beállítások és a big Data típusú adatok igény szerint elérhető eszközök szakaszokat.

## <a name="ingest-data-into-data-lake-store"></a>Adatok betöltése a Data Lake Store-bA
Ez a szakasz kiemeli a különböző adatforrás és a különböző módokon, amelyben az adatok olvasódnak be egy Data Lake Store-fiókot.

![Betölteni az adatokat az Data Lake Store](./media/data-lake-store-data-scenarios/ingest-data.png "gyűjthet adatokat a Data Lake Store-bA")

### <a name="ad-hoc-data"></a>Ad hoc adatok
Ez jelöli, amelyek kisebb adatkészletek használt prototípus-készítés big data-alkalmazásokat. Különböző módon ad-hoc adatok feldolgozására függően az adatok forrását.

| Adatforrás | Kigyűjtés használatával |
| --- | --- |
| Helyi számítógép |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Az Azure platformfüggetlen parancssori felület 2.0-s](data-lake-store-get-started-cli-2.0.md)</li> <li>[A Data Lake Tools for Visual Studio használatával](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Az AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight-fürtön futó DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Streamelési adatok hangulatelemzéséhez
Vásárlói adatok hozhat létre különböző forrásokból, például az alkalmazások, eszközök, szenzorok, stb. Ezeket az adatokat az eszközök széles által olvasódnak be egy Data Lake Store. Ezek az eszközök általában fog rögzítése és feldolgozni az adatokat egy eseményt az esemény által történik, a valós idejű, majd írja be az események kötegekben Data Lake Store-ba, hogy ezek tovább dolgozhatók fel.

Eszközök, amelyekkel a következők:

* [Az Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) -események Event hubsba betöltött szolgáltatásba lehet írni az Azure Data Lake használatával egy Azure Data Lake Store-kimenetet.
* [Az Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) -írhat adatokat közvetlenül a Data Lake Store, a Storm-fürt.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – események fogadása az Event Hubs, és ezután írás a Data Lake Store használatával az [Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relációs adatok
A forrásadatok relációs adatbázisból. Egy időszakon belül a relációs adatbázisok gyűjtése hatalmas mennyiségű adatokat, ami a legfontosabb elemzéseket keresztül big Data jellegű adatok feldolgozása. Az alábbi eszközöket használhatja az ilyen adatok áthelyezése a Data Lake Store-bA.

* [Az Apache sqoop használatával](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web server naplóadatok (feltöltés használó egyéni alkalmazások)
Ez a fajta adatkészlet azért hívjuk fel külön, mivel web server naplóadatok elemzése egy gyakori alkalmazási helyzet a big data-alkalmazások, a fájlokat lehet feltölteni a Data Lake Store nagy mennyiségű igényel. A következő eszközök bármelyikét használhatja saját parancsfájlok vagy alkalmazások töltse fel az adatokat írni.

* [Az Azure platformfüggetlen parancssori felület 2.0-s](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Az Azure Data Lake Store .NET SDK-val](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

A web server naplózási adatok feltöltését, valamint is fel más típusú adatok (például közösségi hangulati adatok) egy jó módszer a saját egyéni parancsfájlok vagy alkalmazások írni, mert azt a rugalmasságot biztosít az adatok feltöltése összetevő részeként a nagyobb big data-alkalmazásokat. Bizonyos esetekben ez a kód is igénybe vehet egy parancsfájl vagy egyszerű parancssori segédprogrammal formájában. Más esetekben a kódot egy üzleti alkalmazás vagy megoldás big data-feldolgozáshoz integrálhatók használhatók.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtök kapcsolódó adatok
A legtöbb HDInsight-fürttípusok (Hadoop, HBase, Storm-) tároló adattár Data Lake Store támogatja. HDInsight-fürtök az Azure Storage Blobs (WASB) adatok eléréséhez. A jobb teljesítmény érdekében a Data Lake Store-fiókra a fürthöz társított másolhatja az adatokat a WASB. A következő eszközök használatával másolja az adatokat.

* [Az Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy Service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Adatok tárolása a helyszíni vagy IaaS Hadoop fürtök
Nagy mennyiségű adat tárolható a meglévő Hadoop-fürtök helyi HDFS használó gépeken. A Hadoop-fürtök lehet egy helyszíni környezetben vagy egy IaaS-fürtöt az Azure-on belül. Az ilyen adatok másolása az Azure Data Lake Store megközelítésre egyszeri vagy ismétlődő módon követelményeket is lehet. Különféle módon használhatja, ennek érdekében. Az alábbi, az alternatív megoldások és a kapcsolódó feláldozását listáját.

| A módszer | Részletek | Előnyök | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Adatok másolása közvetlenül a Hadoop-fürtök az Azure Data Lake Store az Azure Data Factory (ADF) használatával |[Az ADF támogatja a HDFS adatforrásként](../data-factory/connector-hdfs.md) |Az ADF HDFS és elsőrangú – teljes körű felügyeleti és figyelési-a-beépített támogatást nyújt a |Az adatkezelési átjáró üzembe helyezhető a helyszínen vagy az iaas-fürt szükséges |
| Adatok exportálása a Hadoop-fájlok formájában. Ezután másolja a fájlokat az Azure Data Lake Store megfelelő mechanizmussal. |Az Azure Data Lake Store használatával másolja a fájlokat: <ul><li>[Az Azure PowerShell, a Windows operációs rendszer](data-lake-store-get-started-powershell.md)</li><li>[Az Azure platformfüggetlen parancssori felület 2.0 a nem Windows operációsrendszer-](data-lake-store-get-started-cli-2.0.md)</li><li>Minden Data Lake Store SDK használatával egyéni alkalmazás</li></ul> |A gyors kezdéshez. Testre szabott feltöltések hajthatja végre. |Többlépéses folyamat, amely magában foglalja a több technológiákat. Felügyeleti és monitorozási növekszik, nagy kihívást jelent az eszközök testreszabott természetéből idővel |
| A Distcp használatával adatokat másol a Hadoop az Azure Storage. Majd adatok másolása az Azure Storage-ból a Data Lake Store megfelelő mechanizmussal. |A Data Lake Store használatával másolhat adatokat az Azure Storage-ból: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Az AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[A HDInsight-fürtökön futó Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Nyílt forráskódú eszközöket is használhat. |Többlépéses folyamat, amely magában foglalja a több technológiák |

### <a name="really-large-datasets"></a>Nagyon nagy méretű adatkészletek
Több terabájt tartománya az adatkészletek fel, a fenti módszerek használatával néha lassú és költséges lehet. Ezekben az esetekben használhatja az alábbi beállítások közül.

* **Az Azure ExpressRoute használatával**. Az Azure ExpressRoute használatával magánkapcsolatok hozhatók létre az Azure-adatközpontok és olyan infrastruktúrák között a helyszínen. Ez nagy mennyiségű adat átvitelére megbízható lehetőséget kínál. További információkért lásd: [Azure ExpressRoute dokumentációja](../expressroute/expressroute-introduction.md).
* **"Offline állapotba helyezi" az adatok feltöltése**. Ha az Azure ExpressRoute használata nem megvalósítható bármilyen okból, [Azure Import/Export szolgáltatás](../storage/common/storage-import-export-service.md) tehetnek a merevlemez-meghajtók egy Azure-adatközpontban található adatait. Az adatfeltöltés először az Azure Storage-blobokat. Ezután [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) vagy [AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md) az adatok másolása az Azure Storage-Blobokból a Data Lake Store.

  > [!NOTE]
  > Bár az Import/Export szolgáltatással, a méretűek, a lemezeken, amelyek az Azure-adatközpontban történő nem lehet nagyobb, mint 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Data Lake Store-ban tárolt adatok feldolgozása
Az adatok elérhetővé válik a Data Lake Store elemzési futtathatja az adatok a támogatott big data-alkalmazások használatával. Az Azure HDInsight és az Azure Data Lake Analytics jelenleg, használhatja a Data Lake Store-ban tárolt adatokkal kapcsolatos adatok elemzési feladatok futtatásához.

![Adatok elemzése az Data Lake Store](./media/data-lake-store-data-scenarios/analyze-data.png "Data Lake Store az adatok elemzése")

Tekintse meg az alábbi példák.

* [Hozzon létre egy HDInsight-fürt tárolójaként a Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Töltse le az adatokat a Data Lake Store
Érdemes azt is, töltse le vagy adatok áthelyezése az Azure Data Lake Store forgatókönyvek például:

* Adatok áthelyezése más tárházakban való kapcsolat a meglévő adatfeldolgozási folyamatok. Például érdemes adatok áthelyezése az Azure SQL Database vagy a helyszíni SQL Server Data Lake Store.
* Adatok letöltése a helyi számítógép prototípusok application készítése során IDE-környezetek feldolgozásra.

![Kimenő forgalom a Data Lake Store származó adatok](./media/data-lake-store-data-scenarios/egress-data.png "kimenő forgalom a Data Lake Store adatait")

Ilyen esetben a következő lehetőségek bármelyikét használhatja:

* [Az Apache sqoop használatával](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Az Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Az alábbi módszerek használatával írhat adatokat letölteni a Data Lake Store a saját parancsfájl/alkalmazás.

* [Az Azure platformfüggetlen parancssori felület 2.0-s](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Az Azure Data Lake Store .NET SDK-val](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Adatok megjelenítése a Data Lake Store
Szolgáltatások kombinációját használhatja létrehozása a Data Lake Store-ban tárolt adatok vizuális ábrázolásai.

![Adatok megjelenítése a Data Lake Store](./media/data-lake-store-data-scenarios/visualize-data.png "adatok megjelenítése a Data Lake Store")

* Első lépésként használatával [Azure Data Factoryben az adatok áthelyezése az Azure SQL Data Warehouse Data Lake Store](../data-factory/copy-activity-overview.md)
* Ezt követően is [Power BI integrálható az Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) hozhat létre az adatok vizuális megjelenítése.
