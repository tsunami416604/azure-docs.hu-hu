---
title: Gyakori kérdések – Azure szinapszis Analytics (munkaterületek – előzetes verzió)
description: Gyakori kérdések az Azure szinapszis Analytics szolgáltatásról (munkaterületek előzetes verzió)
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: e0f1caf77f0e898b13045e2c840d93f42a5569fc
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85484043"
---
# <a name="azure-synapse-analytics-workspaces-preview-frequently-asked-questions"></a>Azure szinapszis Analytics (munkaterületek előzetes verzió) – gyakori kérdések

Ebben az útmutatóban megtalálja a szinapszis-elemzések leggyakrabban feltett kérdéseit.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Általános kérdések

### <a name="q-what-is-azure-synapse-analytics"></a>K: Mi az az Azure szinapszis Analytics?

A: az Azure szinapszis egy integrált adatplatform a BI, AI és folyamatos intelligencia számára. Különböző elemzési futtatókörnyezeteket (például SQL és Spark) kapcsol össze egyetlen platformon keresztül, amely egységes módszert biztosít a következőkhöz:

- Védje elemzési erőforrásait, beleértve a hálózatot, a készlethez, az adatforrásokhoz és a fejlesztési összetevőkhöz való egyszeri bejelentkezések kezelését.
- Egyszerűen figyelheti és gyorsan optimalizálhatja, reagálhatja és hibakeresési eseményeket végezhet a munkaterület tevékenységeiben bármely rétegben.
- A metaadatok kezelése a motorok között. Hozzon létre egy Spark-táblázatot, amely automatikusan elérhető lesz az Azure szinapszis-adatbázisaiban.
- Az adatkezelés egységes felhasználói élményt nyújt. A szinapszis Studio nagy adatfejlesztőket, adatmérnököket, Adattervezők, adatelemzőket és adatszakértőket kínál ugyanahhoz a platformhoz.

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>K: az Azure szinapszis Analytics fő összetevői

A: az Azure szinapszis a következő képességekkel rendelkezik:

- Az elemzési funkciók az SQL-készleten vagy az SQL on-demand (előzetes verzió) (kiszolgáló nélküli) elérhetők.
- Apache Spark készlet (előzetes verzió) teljes körű támogatással a Scala, a Python, a SparkSQL és a C szolgáltatáshoz #
- A Code-Free big data átalakítási felületét biztosító adatfolyam
- Adatintegrációs &-előkészítés az adatai integrálásához és az összes működővé tenni való hozzáféréshez
- A Studio ezen képességek egyetlen webes felhasználói felületen keresztüli eléréséhez

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>K: hogyan kapcsolódik az Azure szinapszis Analytics Azure SQL Data Warehouse

A: az Azure szinapszis Analytics a Azure SQL Data Warehouse fejlődése egy elemzési platformon, amely az SQL-készletet is tartalmazza adattárház-megoldásként. Ez a platform ötvözi az adatfeltárást, a betöltést, az átalakítást, az előkészítést és a kiszolgáló elemzési rétegét.

## <a name="use-cases"></a>Használati esetek

### <a name="q-how-do-i-rename-a-published-artifact-dataset-notebook-sql-script-and-so-on-in-azure-synapse"></a>K: Hogyan átnevezni egy közzétett összetevőt (adatkészlet, jegyzetfüzet, SQL-parancsfájl stb.) az Azure Szinapszisban?

A: egy közzétett összetevő-fájl átnevezéséhez először a fájlt kell megadnia, és átnevezni az új fájlt a kívánt névre. Manuálisan kell frissítenie az összetevő összes hivatkozását az új fájlnévre, és törölnie kell a régit.

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>K: mi a jó használati eset a szinapszis SQL-készlethez

A: az SQL-készlet az adattárház-szükségletek szíve. Ez a vezető adattárház-megoldás az [ár/teljesítmény](https://azure.microsoft.com/services/sql-data-warehouse/compare/)szolgáltatásban. Az SQL Pool piacvezető felhőalapú adattárház-megoldás, mivel a következőket teheti:

- nagy és vegyes számítási feladatok ellátása anélkül, hogy a magas párhuzamosság és a számítási feladatok elkülönítése is negatív hatással lenne
- az adatok biztonságos védelme a hálózati biztonságtól egészen a részletesen elérhető speciális funkciókkal
- számos öko-rendszer előnye

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>K: mi a jó használati eset a Sparkhoz a Szinapszisban

A: az első célunk, hogy nagy adattervezési élményt nyújtson az adatkezeléshez a tavon a Batch vagy a stream használatával. Az adatelőkészítés szoros és egyszerű integrálása révén a fejlesztési munka operacionalizálási válik.

A Spark egy másik felhasználási esete, hogy az adattudós:

- szolgáltatás kibontása
- az adatfelfedezés
- modell betanítása

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>K: mi a jó használati eset az SQL igény szerinti használatához a Szinapszisban

A: az SQL on-demand egy lekérdezési szolgáltatás az adatközpontban található adaton keresztül. Lehetővé teszi, hogy demokratizálni az összes adataihoz való hozzáférést úgy, hogy ismerős T-SQL-szintaxist biztosít az adatlekérdezéshez, anélkül, hogy egy speciális tárolóba kellene másolnia vagy betöltenie az adatait.

Használati példák például a következők:

- alapszintű felderítés és feltárás – az adatelemzők, a kialakulóban lévő adatszakértők és az adatmérnökök könnyedén betekintést nyerhetnek az adatokba a saját adattóban az olvasási T-SQL-lekérdezésekkel
- logikai adattárház – az adatelemzők teljes kifejező-T futtathatnak a T-SQL nyelvről közvetlenül az Azure Storage-ban tárolt adat lekérdezésére és elemzésére, valamint ismerős BI-eszközök (például Azure Analysis Services, Power BI Premium stb.) használatára az irányítópultok frissítéséhez a Starlight lekérdezési lekérdezések újrafuttatásával
- "egyszeri lekérdezés" ETL – lehetővé teszi az adatmérnökök számára, hogy az Azure Storage-alapú adatokat az egyik formátumból a másikba, szűrésre, összesítésre és a nagymértékben párhuzamos feldolgozási divatba alakítsa át, a lekérdezési eredményeket az Azure Storage-ba, és azonnal elérhetővé tegye a további feldolgozáshoz a Starlight-lekérdezésben vagy más hasznos szolgáltatások

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>K: mi a jó használati eset a Szinapszisban zajló adatforgalomhoz

A: az adatforgalom lehetővé teszi, hogy az adatmérnökök programkód írása nélkül fejlesszenek grafikus Adatátalakítási logikát. Az eredményül kapott adatfolyamatok az adatintegrációs &-előkészítési tevékenységen belül lesznek végrehajtva. Az adatáramlási tevékenységek a meglévő ütemezési, vezérlési, folyamat-és figyelési képességek segítségével is működőképesek.

## <a name="security-and-access"></a>Biztonság és hozzáférés

A: a teljes körű egyszeri bejelentkezési élmény egy fontos hitelesítési folyamat a szinapszis Analyticsben. Az identitások teljes HRE-integráción keresztül történő kezelése és átadása kötelező.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>K: Hogyan hozzáférhet a fájlokhoz és mappákhoz a ADLS Gen2

A: a fájlokhoz és mappákhoz való hozzáférés jelenleg ADLS Gen2 felügyelhető. További információ: [Data Lake Storage hozzáférés-vezérlése](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>K: használhatok harmadik féltől származó üzleti intelligencia eszközöket az Azure szinapszis Analytics eléréséhez

Válasz: igen, a harmadik féltől származó üzleti alkalmazásokat, például a tablót és a Power BI is használhatja az SQL-készlethez és az igény szerinti SQL-hez való kapcsolódáshoz. A Spark támogatja a IntelliJ.

## <a name="next-steps"></a>Következő lépések

- [Munkaterület létrehozása](quickstart-create-workspace.md)
- [A szinapszis Studio használata](quickstart-synapse-studio.md)
- [SQL-készlet létrehozása](quickstart-create-sql-pool-portal.md)
- [Az igény szerinti SQL használata](quickstart-sql-on-demand.md)
- [Apache Spark-készlet létrehozása](quickstart-create-apache-spark-pool-portal.md) 
