---
title: Data Lake tárolási Gen1 érintő adatáttelepítések esetében |} Microsoft Docs
description: Különböző alkalmazási helyzetek és eszközök használatával, mely adatokra is okozhatnak, feldolgozása, letöltése és a Data Lake tárolási Gen1 (korábbi nevén az Azure Data Lake Store) ábrázolt megismeréséhez
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: e0c7ed22762ef19c6e68ad69d0cabcfeb8007251
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031039"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Azure Data Lake tárolási Gen1 használatával big Data típusú adatok követelmények

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Nagy adatfeldolgozási négy fő szakaszból áll:

* Választásával dolgozhat fel nagy mennyiségű adat adatok tárolóba, valós idejű vagy kötegek
* Az adatok feldolgozása
* Az adatok letöltése
* Az adatok megjelenítése

Ebben a cikkben úgy tekintünk a szakaszok tekintetében az Azure Data Lake Store megtudhatja, hogy a beállítások és a big Data típusú adatok igényeknek megfelelő eszközöket is.

## <a name="ingest-data-into-data-lake-store"></a>A Data Lake Store betöltik az adatokat
Ez a szakasz az adatok és a különböző módokon, amelyben el az adatok egy Data Lake Store figyelembe meghatározták a különböző forrásokból mutatja be.

![A Data Lake Store betöltik az adatokat](./media/data-lake-store-data-scenarios/ingest-data.png "betöltik az adatokat a Data Lake Store")

### <a name="ad-hoc-data"></a>Ad hoc adatok
Ez jelöli, amelyek kisebb adatkészletek prototípusának a big Data típusú adatok alkalmazás használja. Többféleképpen attól függően, hogy az adatforrás az alkalmi adatok választásával dolgozhat fel.

| Adatforrás | Betöltési használatával |
| --- | --- |
| Helyi számítógép |<ul> <li>[Azure Portal](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Az Azure platformfüggetlen parancssori felület 2.0](data-lake-store-get-started-cli-2.0.md)</li> <li>[A Data Lake Tools for Visual Studio használatával](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight-fürtön futó ból a DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Adatfolyamként továbbított adatok
Adatok, például alkalmazások, eszközök, érzékelőket és stb különböző forrásokból létrehozható jelképez. Ezek az adatok által számos különféle eszközre történő egy Data Lake Store meghatározták. Ezek az eszközök általában fog rögzítése és feldolgozni az adatokat az esemény által alapon valós idejű, majd írja be az események kötegekben a Data Lake Store, hogy azok további dolgozhatók fel.

Az alábbiakban eszközök közül választhat:

* [Az Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) -események az Event Hubsban okozhatnak csak írható Azure Data Lake az Azure Data Lake Store kimeneti használatával.
* [Az Azure HDInsight alatt futó Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) -írhat adatok közvetlenül a Data Lake Store a Storm-fürtök.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) – események fogadásához az Event Hubs, és jegyezze Data Lake Store használatára a [Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Relációs adatok
A forrásadatok relációs adatbázisból. Egy meghatározott időtartam során rendelkezésre a relációs adatbázisok gyűjtése hatalmas mennyiségű adat, amely kulcs áttekintést adnak is, ha a big data-feldolgozási folyamaton keresztül. A következő eszközök segítségével ilyen adatok áthelyezése a Data Lake Store rendszerbe.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web server naplóadatokat (a feltöltési egyéni alkalmazások használata)
Ez a fajta adatkészlet kifejezetten feltüntettük mert web server napló adatok elemzése a big data-alkalmazások gyakori használati esetek és a naplófájlokat, és fel kell tölteni a Data Lake Store nagy méretű köteten kell. A következő eszközök bármelyikével használhatja a saját parancsfájlok vagy alkalmazások feltölteni az ilyen adatokat írni.

* [Az Azure platformfüggetlen parancssori felület 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Az Azure Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Web server napló adatfeltöltési, valamint a más típusú adatok (például közösségi hangulati elemek adatok) feltöltése-e a saját egyéni parancsfájlok alkalmazások írására, mert azt a rugalmasságot biztosít az adatok feltöltése összetevő részeként jó megközelítése a nagyobb big Data típusú adatok alkalmazás. Egyes esetekben ez a kód is igénybe vehet az űrlap egy parancsfájl vagy egy egyszerű parancssori segédprogrammal. Más esetekben a kód egy üzleti alkalmazás vagy megoldás nagy adatfeldolgozási integrálja használható.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Társított alkalmazások Azure HDInsight-fürtök
A legtöbb HDInsight-fürttípusok (Hadoop, HBase, Storm) Data Lake Store támogatják az adatok tárolási tára. A HDInsight-fürtök elérni az adatokat az Azure Storage Blobs (WASB). A jobb teljesítmény érdekében átmásolhatja az adatokat WASB be a fürthöz tartozó Data Lake Store-fiókba. A következő eszközök segítségével másolja az adatokat.

* [Apache ból a DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy Service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Tárolt adatokat a helyszíni vagy infrastruktúra-szolgáltatási Hadoop fürtök
Nagy mennyiségű adat tárolható meglévő Hadoop-fürtök, helyi HDFS használatával gépeken. A Hadoop-fürtök egy helyi központi esetleg lehet, hogy az infrastruktúra-szolgáltatási fürtöt az Azure-on belül. Előfordulhat, hogy az ilyen adatok másolása az Azure Data Lake Store megközelítésre egyszeri vagy ismétlődő módon követelményeinek. Ennek eléréséhez használható különböző lehetőség áll rendelkezésre. Az alábbiakban olvashat egy listát alternatívák és a kapcsolódó kompromisszumot.

| Módszer | Részletek | Előnyei | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Adatok másolása közvetlenül a Hadoop-fürtök az Azure Data Lake Store az Azure Data Factory (ADF) használatával |[ADF HDFS adatforrásként támogatja.](../data-factory/connector-hdfs.md) |ADF out-of-az-box támogatást biztosít az HDFS és első-végpontok kezelése és figyelése |Az adatkezelési átjáró telepített helyszíni vagy a az IaaS a fürt szükséges |
| Adatok exportálása a Hadoop-fájlok formájában. Ezután másolja a fájlokat az Azure Data Lake Store megfelelő mechanizmussal. |Azure Data Lake Store használatával másolhat fájlokat: <ul><li>[Az Azure PowerShell, a Windows operációs rendszer](data-lake-store-get-started-powershell.md)</li><li>[Az Azure platformfüggetlen parancssori felület 2.0 a nem - Windows operációs rendszer](data-lake-store-get-started-cli-2.0.md)</li><li>Minden Data Lake Store SDK használatával egyéni alkalmazás</li></ul> |Gyors megkezdéséhez. Testre szabott feltöltések teheti |Folyamat, amely magában foglalja a több technológiák. Kezelési és figyelési eszközök testreszabott jellegéből időbeli kihívást kell nőhet |
| Adatok másolása a Hadoop Azure Storage ból a Distcp segítségével. Majd adatok másolása az Azure Storage az Data Lake Store megfelelő mechanizmussal. |Az Azure Storage adatok másolása Data Lake Store használata: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[A HDInsight-fürtökön futó Apache ból a DistCp](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Nyílt forráskódú eszközök is használhatók. |Folyamat, amely magában foglalja a több technológiák |

### <a name="really-large-datasets"></a>Valóban nagy adatkészletek
Az adatkészleteket, amelyek között a több terabájt feltöltése, a fenti módszerek használatával néha lassú és költséges lehet. Ilyen esetben az alábbi beállítások közül is használhatja.

* **Az Azure ExpressRoute segítségével**. Az Azure ExpressRoute lehetővé teszi az Azure adatközpontjaiban és az infrastruktúra közötti magánhálózati kapcsolatok létrehozása a helyszínen. Ez lehetővé teszi nagy mennyiségű adat átvitele egy megbízható beállítása. További információkért lásd: [Azure ExpressRoute dokumentációja](../expressroute/expressroute-introduction.md).
* **Az adatok "Offline" feltöltése a**. Ha Azure ExpressRoute használata nem megvalósítható a bármilyen okból, [Azure Import/Export szolgáltatás](../storage/common/storage-import-export-service.md) szállítási merevlemez-meghajtók az adatait az Azure adatközpontba. Az adatok először tölt fel az Azure Storage blobs szolgáltatásban. Ezután [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) vagy [AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md) adatok másolása az Azure Storage Blobs Data Lake Store-bA.

  > [!NOTE]
  > Amíg az Import/Export szolgáltatás, a fájlméret, a lemezeken, amelyek az Azure-adatközpontba történő használatával nem lehet nagyobb, mint 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Data Lake Store-ban tárolt adatok feldolgozása
Az adatok elérhetővé válik a Data Lake Store is futtatása az adatok a támogatott big data-alkalmazások használatával. Jelenleg Azure HDInsight és az Azure Data Lake Analytics is használja a Data Lake Store-ban tárolt adatok adatok feladatok futtatásához.

![Adatok elemzése az Data Lake Store](./media/data-lake-store-data-scenarios/analyze-data.png "elemezhetik a Data Lake Store-ban")

A következő példákban is megtekinthetik.

* [HDInsight-fürtök létrehozása a Data Lake Store tárolóként](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Töltse le az adatok Data Lake Store-ból
Érdemes azt is, töltse le és tárolt adatok mozgatása az Azure Data Lake Store forgatókönyvek például:

* Adatok áthelyezése az egyéb tárhelyek felületet a meglévő adatok feldolgozása kimenetátirányítási mechanizmusát használó műveletekről. Például előfordulhat, hogy áthelyezendő adatok Data Lake Store-ból az Azure SQL Database vagy a helyszíni SQL Server.
* Adatok letöltése a helyi számítógép alkalmazás prototípusok felépítésekor IDE környezetben feldolgozásra.

![Data Lake Store-ból adatokat kilépési](./media/data-lake-store-data-scenarios/egress-data.png "kilépési adatok Data Lake Store-ból")

Ilyen esetben a következő beállítások bármelyikét használhatja:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache ból a DistCp](data-lake-store-copy-data-wasb-distcp.md)

Az alábbi eljárások segítségével a saját parancsfájl/alkalmazás adatok letöltése a Data Lake Store-ból.

* [Az Azure platformfüggetlen parancssori felület 2.0](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Az Azure Data Lake Store .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>A Data Lake Store-adatok ábrázolása
A szolgáltatások kombinációját hozhat létre a Data Lake Store-ban tárolt adatok vizuális ábrázolásai.

![A Data Lake Store-adatok ábrázolása](./media/data-lake-store-data-scenarios/visualize-data.png "a Data Lake Store-adatok ábrázolása")

* Megkezdheti a [Azure Data Factory Data Lake Store az Azure SQL Data Warehouse tárolt adatok mozgatása](../data-factory/copy-activity-overview.md)
* Ezt követően is [Power BI integrálása az Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) létrehozása az adatok vizuális ábrázolását.
