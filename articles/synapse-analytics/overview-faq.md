---
title: Gyakori kérdések - Azure Synapse Analytics
description: Gyakori kérdések az Azure Synapse Analytics szolgáltatásról
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 00a98fdeb4b8febbcb9d1183fabffc7298cc9e4c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770723"
---
# <a name="azure-synapse-analytics-workspace-preview-frequently-asked-questions"></a>Az Azure Synapse Analytics (munkaterület előzetes verziója) gyakori kérdések

Ebben az útmutatóban megtalálja a Synapse Analytics leggyakrabban feltett kérdéseit.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Általános kérdések

### <a name="q-what-is-azure-synapse-analytics"></a>K: Mi az Azure Synapse Analytics

A: Az Azure Synapse egy integrált adatplatform bi, ai és folyamatos intelligencia. Különböző elemzési futásidőket, például az SQL-t és a Sparkot egyetlen platformon keresztül köti össze, amely egységes módot biztosít a következőkre:

- Biztonságossá teszi az elemzési erőforrásokat, beleértve a hálózatot, a készlethez, az adatokhoz és a fejlesztési összetevőkhöz való egyszeri bejelentkezéskezelését.
- Egyszerűen figyelheti és gyorsan optimalizálhatja, reagálhatja és debugolhatja a munkaterületi tevékenységekben bármely rétegen befutó eseményeket.
- A metaadatok kezelése a motorok között. Hozzon létre egy Spark-táblát, és automatikusan elérhető lesz az Azure Synapse-adatbázisokban.
- Az adatok egységes felhasználói élményrévén kommunikálhat. A Synapse Studio ugyanarra a platformra hozza a Big Data Fejlesztőket, adatmérnököket, dba-kat, adatelemzőket és adatszakértőket.

### <a name="q-how-do-i-get-started-with-azure-synapse-analytics"></a>K: Hogyan kezdhetem el az Azure Synapse Analytics szolgáltatást?

A: Az Azure Synapse Analytics használatának megkezdéséhez hozzon létre egy [Synapse munkaterületet](https://portal.azure.com) (ingyenes!), és hozza létre a kívánt erőforrásokat a munkaterületen. Követheti az egyik rövid útmutatónkat, például [a Szinapszis SQL-készlet létrehozása](quickstart-create-sql-pool.md) vagy [a Munkaterület létrehozása](quickstart-create-workspace.md), amely végigvezeti az egyszerű használati eseten. 

A mintajegyzetfüzeteket és az SQL-parancsfájlokat a tárházban is [megtalálhatja.](https://github.com/Azure/azure-synapse-analytics/tree/master/samples) Ha nyilvános adatkészlethez kell csatlakoznia, hozzon létre egy új csatolt szolgáltatást a következő attribútumokkal:

- azure_storage_account_name = "azureopendatastorage"
- azure_storage_sas_token = "" (írja **""**)

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>K: Melyek az Azure Synapse Analytics fő összetevői

A: Az Azure Synapse a következő képességekkel rendelkezik:

- Az Analytics-funkciók at sql készlet vagy az SQL on-demand (előzetes verzió) (Kiszolgáló nélküli) keresztül kínálják.
- Apache Spark-készlet (előzetes verzió) a Scala, a Python, a SparkSQL és a C teljes körű támogatásával #
- Kódmentes big data átalakítási élményt kínáló adatfolyam
- Adatintegráció& vezénylési az adatok integrálásához és az összes kódfejlesztés működőképessé tételéhez
- A Studio egyetlen webes felhasználói felületen keresztül érheti el ezeket a képességeket

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>K: Hogyan kapcsolódik az Azure Synapse Analytics az Azure SQL Data Warehouse-hoz?

A: Az Azure Synapse Analytics az Azure SQL Data Warehouse egy elemzési platformmá történő fejlődése, amely az SQL-készletet adattárház-megoldásként tartalmazza. Ez a platform egyesíti az adatok feltárását, betöltését, átalakítását, előkészítését és egy kiszolgáló elemzési réteget.

## <a name="use-cases"></a>Használati esetek

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>K: Mi a szinapszis SQL-készlet jó használati esete?

A: Az SQL-készlet az adattárház-igények szíve. Ez a vezető adattárház megoldás [az ár / teljesítmény.](https://azure.microsoft.com/services/sql-data-warehouse/compare/) Az SQL-készlet az iparágvezető felhőadat-raktármegoldás, mivel a következőket teheti:

- a nagy egyidejűségnek és a munkaterhelés-elkülönítésnek köszönhetően a munkaterhelések széles és vegyes választékát szolgálják ki anélkül, hogy ez hatással lenne a teljesítményre
- a hálózati biztonságtól a részletes hozzáférésig terjedő fejlett funkciókrévén könnyedén kezelheti adatait
- az ökorendszer széles körének előnyeit

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>K: Mi a jó használati eset a Spark a Synapse

V: Az első célunk az, hogy egy nagy adatmérnöki élményt az adatok átalakítása a tó felett kötegelt vagy stream. Az adatvezénk szoros és egyszerű integrálása egyszerűvé teszi a fejlesztési munka üzembe munkáját.

A Spark másik használati esete az adattudós:

- kibontása egy funkció
- adatok feltárása
- modell betanítása

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>K: Mi a jó használati eset az SQL on-demand a Szinapszis

V: SQL on-demand egy lekérdezési szolgáltatás az adatok a data lake. Lehetővé teszi az összes adathoz való hozzáférés demokratizálását azáltal, hogy egy ismerős T-SQL szintaxist biztosít az adatok lekérdezéséhez anélkül, hogy adatokat kellene másolnia vagy betöltenie egy speciális tárolóba.

A használati esetpéldák a következők:

- alapvető felfedezés és feltárás - az adatelemzők, a feltörekvő adatszakértők és adatmérnökök számára egyszerű utat biztosít az adattóban élő adatok első betekintéséhez az olvasott T-SQL lekérdezések sémájával
- logikai adattárház – az adatelemzők a T-SQL nyelv teljes kifejezőerejét futtathatják, hogy közvetlenül lekérdezzék és elemezhessék az Azure Storage-ban tárolt adatokat, és ismerős ÜZLETI-eszközökkel (pl. Azure Analysis Services, Power BI Premium stb.) frissíthessék az irányítópultokat a Starlight-lekérdezések újrafuttatásával
- "egy lekérdezés" ETL - lehetővé teszi az adatmérnökök számára, hogy az Azure Storage-alapú adatokat egyik formátumból a másikba alakítsák át, szűrje, összesítse stb.

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>K: Mi a jó használati eset az adatáramlás hoz a Szinapszisban

A: Az adatfolyam lehetővé teszi az adatmérnökök számára, hogy kód írása nélkül fejlesszenek ki grafikus adatátalakítási logikát. Az eredményül kapott adatfolyamatok az adatintegrációs & vezénylési tevékenységekként kerülnek végrehajtásra. Az adatfolyam-tevékenységek a meglévő ütemezési, vezérlési, folyamat- és figyelési képességek en keresztül működőképessé tehetők.

## <a name="security-and-access"></a>Biztonság és hozzáférés

A: A végpontok között egyszeri bejelentkezés fontos hitelesítési folyamat a Synapse Analytics-ben. A személyazonosság kezelése és átadása a teljes AAD-integráción keresztül elengedhetetlen.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adlsg2"></a>K: Hogyan férek hozzá az ADLSg2 fájljaihoz és mappáihoz?

A: A fájlokhoz és mappákhoz való hozzáférést jelenleg az ADLSg2 kezeli. További információ: [Data Lake storage access control](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>K: Használhatok külső üzletiintelligencia-eszközöket az Azure Synapse Analytics eléréséhez

A: Igen, használhatja a külső üzleti alkalmazások, például a Tableau és a Power BI, az SQL-készlet hez és az SQL igény szerinti csatlakozáshoz. A Spark támogatja az IntelliJ-t.

## <a name="next-steps"></a>További lépések

- [Munkaterület létrehozása](quickstart-create-workspace.md)
- [A Synapse Studio használata](quickstart-synapse-studio.md)
- [SQL-készlet létrehozása](quickstart-create-sql-pool.md)
- [Az igény szerinti SQL használata](quickstart-sql-on-demand.md)
- [Apache Spark-készlet létrehozása](quickstart-create-apache-spark-pool.md) 
