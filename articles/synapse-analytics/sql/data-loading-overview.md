---
title: Etl helyett tervezze meg az ELT-t a Synapse SQL pool számára | Microsoft dokumentumok
description: Etl helyett tervezzen egy kinyerési, betöltési és átalakítási (ELT) folyamatot az adatok vagy az SQL-készlet betöltéséhez.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 596f4bcf2e3f829430fdc90eb1806a44a84b2bc5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429589"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-synapse-sql-pool"></a>PolyBase-adatbetöltési stratégia tervezése az Azure Synapse SQL-készlethez

A hagyományos SMP adattárházak egy kinyerési, átalakítási és betöltési (ETL) folyamatot használnak az adatok betöltéséhez. Az Azure SQL-készlet egy masszívan párhuzamos feldolgozási (MPP) architektúra, amely kihasználja a számítási és tárolási erőforrások méretezhetőségét és rugalmasságát. A kinyerési, betöltési és átalakítási (ELT) folyamat kihasználásával kihasználhatja az MPP előnyeit, és kiküszöbölheti az adatok betöltés előtti átalakításához szükséges erőforrásokat.

Míg az SQL-készlet számos betöltési módszert támogat, beleértve a nem Polibázis-beállításokat, például a BCP és az SQL BulkCopy API-t, a leggyorsabb és leginkább skálázható módja a betöltési dátumnak a PolyBase-en keresztül történik.  A PolyBase egy olyan technológia, amely a T-SQL nyelven keresztül éri el az Azure Blob storage-ban vagy az Azure Data Lake Store-ban tárolt külső adatokat.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Mi az ELT?

A kinyerés, a betöltés és az átalakítás (ELT) egy olyan folyamat, amelynek során az adatokat egy forrásrendszerből nyeri ki, betölti egy adatraktárba, majd átalakítja.

A PolyBase ELT SQL-készlethez való megvalósításának alapvető lépései a következők:

1. A forrásadatok kinyerése szövegfájlokba.
2. Az adatokat az Azure Blob storage vagy az Azure Data Lake Store áruházba tárolhatja.
3. Készítse elő az adatokat a betöltésre.
4. Töltse be az adatokat az SQL-készlet átmeneti tábláiba a PolyBase használatával.
5. Az adatok átalakítása.
6. Adatok beszúrása az éles üzemi táblákba.

A betöltési oktatóanyagról a [PolyBase használata adatok betöltése az Azure blob storage-ból az Azure SQL Data Warehouse-ba című témakörben található.](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

További információ: [Minták betöltése blog](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. A forrásadatok kibontása szöveges fájlokba

Az adatok kitárolása a tárolási helytől függ.  A cél az, hogy az adatokat áthelyezik a PolyBase által támogatott tagolt szövegfájlokba.

### <a name="polybase-external-file-formats"></a>PolyBase külső fájlformátumok

A PolyBase utf-8 és UTF-16 kódolású, tagolt szövegfájlokból tölti be az adatokat. A tagolt szövegfájlok mellett a Hadoop fájlformátumokból is betöltődik RC fájl, ORC és Parketta. A PolyBase a Gzip és a Snappy tömörített fájlokból is képes adatokat betölteni. A PolyBase jelenleg nem támogatja a kiterjesztett ASCII, rögzített szélességű és beágyazott formátumokat, például a WinZip, a JSON és az XML formátumot.

Ha az SQL Server kiszolgálóról exportál, az [bcp parancssori eszközzel](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) exportálhatja az adatokat tagolt szövegfájlokba. A Parketta-SQL DW adattípus-leképezés a következő:

| **Parketta adattípusa** |                      **SQL adattípus**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        logikai        |                             bit                              |
|        double         |                            lebegőpontos                             |
|         lebegőpontos         |                             real                             |
|        double         |                            Pénzt                             |
|        double         |                          kis pénz                          |
|        sztring         |                            nchar                             |
|        sztring         |                           nvarchar                           |
|        sztring         |                             Char                             |
|        sztring         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary között                           |
|       időbélyeg       |                             dátum                             |
|       időbélyeg       |                        smalldatetime                         |
|       időbélyeg       |                          datetime2                           |
|       időbélyeg       |                           dátum/idő                           |
|       időbélyeg       |                             time                             |
|       dátum            |                             dátum                             |
|        tizedes tört        |                            tizedes tört                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Az adatok letárolása az Azure Blob storage-ba vagy az Azure Data Lake Store-ba

Az azure storage-ban való letéréshez áthelyezheti őket az [Azure Blob storage-ba](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) vagy az [Azure Data Lake Store-ba.](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Az adatokat mindkét helyen szöveges fájlokban kell tárolni. A PolyBase mindkét helyről betölthető.

Az adatok Azure Storage-ba való áthelyezéséhez használható eszközök és szolgáltatások:

- [Az Azure ExpressRoute](../../expressroute/expressroute-introduction.md) szolgáltatás javítja a hálózati átviteli teljesítményt, a teljesítményt és a kiszámíthatóságot. Az ExpressRoute egy olyan szolgáltatás, amely dedikált privát kapcsolaton keresztül továbbítja az adatokat az Azure-hoz. Az ExpressRoute-kapcsolatok nem továbbítják az adatokat a nyilvános interneten keresztül. A kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késéseket és nagyobb biztonságot nyújtanak, mint a nyilvános interneten keresztüli tipikus kapcsolatok.
- [Az AZCopy segédprogram](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) az adatokat a nyilvános interneten keresztül helyezi át az Azure Storage-ba. Ez akkor működik, ha az adatméretek 10 TB-nál kisebbek. Ha az AZCopy segítségével rendszeresen szeretné végrehajtani a terheléseket, ellenőrizze a hálózati sebességet, hogy elfogadható-e.
- [Az Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) rendelkezik egy átjáróval, amely a helyi kiszolgálón telepíthető. Ezután létrehozhat egy folyamatot az adatok áthelyezéséhez a helyi kiszolgálóról az Azure Storage-ba. A Data Factory SQL-készlettel való használatához olvassa el [az Adatok betöltése az SQL-készletbe című témakört.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="3-prepare-the-data-for-loading"></a>3. Készítse elő az adatokat a betöltésre

Előfordulhat, hogy elő kell készítenie és meg kell tisztítania az adatokat a tárfiókban, mielőtt betöltené az SQL-készletbe. Az adatok előkészítése akkor is elvégezhető, ha az adatok a forrásban vannak, miközben exportálja az adatokat szöveges fájlokba, vagy miután az adatok az Azure Storage-ban vannak.  Az adatokkal a lehető leghamarabb a legegyszerűbb dolgozni.  

### <a name="define-external-tables"></a>Külső táblák definiálása

Az adatok betöltése előtt meg kell határoznia a külső táblákat az adatraktárban. A PolyBase külső táblákat használ az Azure Storage-adatok definiálására és elérésére. Egy külső tábla hasonló az adatbázisnézethez. A külső tábla tartalmazza a táblasémát, és az adatraktáron kívül tárolt adatokra mutat.

A külső táblák megadása magában foglalja az adatforrás, a szövegfájlok formátumának és a táblázatdefiníciók megadását. Ezek a T-SQL szintaktikai témakörök, amelyekre szüksége lesz:

- [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

A külső objektumok létrehozásának például a Betöltési oktatóanyag [Külső táblák létrehozása](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-external-tables-for-the-sample-data) című témakörben található.

### <a name="format-text-files"></a>Szövegfájlok formázása

A külső objektumok definiálása után a szövegfájlok sorait a külső táblázathoz és a fájlformátum definíciójához kell igazítania. A szövegfájl egyes sorának adatainak igazodniuk kell a táblázat definíciójához.
A szövegfájlok formázása:

- Ha az adatok nem relációs forrásból származnak, sorokká és oszlopokká kell alakítania azadatokat. Függetlenül attól, hogy az adatok relációs vagy nem relációs forrásból származnak, az adatokat úgy kell átalakítani, hogy igazodjanak annak a táblának az oszlopdefinícióihoz, amelybe az adatokat be kívánja tölteni.
- Formázza a szövegfájlban lévő adatokat úgy, hogy igazodjanak az SQL-készlet céltáblájának oszlopaihoz és adattípusaihoz. A külső szövegfájlokban lévő adattípusok és az adattárház-tábla közötti eltérés hatására a program elutasítja a sorokat a terhelés során.
- A szövegfájl mezőinek elkülönítése terminátorral.  Ügyeljen arra, hogy olyan karaktert vagy karaktersorozatot használjon, amely nem található meg a forrásadatokban. Használja a [külső fájlformátum létrehozása](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)mezőben megadott terminátort.

## <a name="4-load-the-data-into-sql-pool-staging-tables-using-polybase"></a>4. Töltse be az adatokat az SQL készlet átmeneti tábláiba a PolyBase használatával

Ajánlott adatokat betölteni egy átmeneti táblába. Az átmeneti táblák lehetővé teszik a hibák kezelését a termelési táblák beavatkozása nélkül. Az átmeneti tábla is lehetőséget ad arra, hogy sql készlet MPP adatátalakítások, mielőtt az adatok at éles táblák.

### <a name="options-for-loading-with-polybase"></a>A PolyBase segítségével való betöltés lehetőségei

Az adatok PolyBase segítségével az alábbi betöltési lehetőségek bármelyikét használhatja:

- [A PolyBase t-SQL-t](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) használ, ha az adatok az Azure Blob storage vagy az Azure Data Lake Store tárolóban vannak. Ez biztosítja a legtöbb ellenőrzést a betöltési folyamat, hanem megköveteli, hogy meghatározza a külső adatobjektumokat. A többi módszer határozza meg ezeket az objektumokat a színfalak mögött, amikor a forrástáblákat a céltáblákhoz rendeli.  A T-SQL-betöltések vezényléséhez használhatja az Azure Data Factory, az SSIS vagy az Azure-függvényeket.
- [Az SSIS-sel rendelkező PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) jól működik, ha a forrásadatok az SQL Server ben vannak, akár a helyszíni SQL Server ben, akár a felhőben. Az SSIS határozza meg a forrás és a céltábla-hozzárendeléseket, és vezényli a terhelést is. Ha már rendelkezik SSIS-csomagokkal, módosíthatja a csomagokat, hogy működjenek együtt az új adattárház célállomással.
- [PolyBase az Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) egy másik vezénylési eszköz.  Meghatározza a folyamatot, és ütemezi a feladatokat.
- [Az Azure Databricks-szel rendelkező PolyBase](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) adatokat továbbít egy SQL Data Warehouse táblából egy Databricks dataframe-be, és/vagy adatokat ír egy Databricks-adatkeretből egy SQL Data Warehouse-táblába a PolyBase használatával.

### <a name="non-polybase-loading-options"></a>Nem Polibázisos betöltési beállítások

Ha az adatok nem kompatibilisek a PolyBase-lel, használhatja az [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) vagy az [SQLBulkCopy API -t.](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) A bcp közvetlenül az SQL-készletbe töltődik be anélkül, hogy az Azure Blob-tárolón keresztül haladna, és csak kis rakományok számára készült. Ne feledje, hogy ezeknek a beállításoknak a terhelési teljesítménye jelentősen lassabb, mint a PolyBase.

## <a name="5-transform-the-data"></a>5. Az adatok átalakítása

Amíg az adatok az átmeneti táblában vannak, hajtsa végre a számítási feladatok által igényelt átalakításokat. Ezután helyezze át az adatokat egy termelési táblába.

## <a name="6-insert-the-data-into-production-tables"></a>6. Az adatok beszúrása a termelési táblázatokba

A betét ... SELECT utasítás az átmeneti táblából az állandó táblába helyezi át az adatokat.

EtL-folyamat tervezésekor próbálja meg futtatni a folyamatot egy kis tesztmintán. Próbálja meg kibontani 1000 sort a táblából egy fájlba, helyezze át az Azure-ba, majd próbálja meg betölteni egy átmeneti táblába.

## <a name="partner-loading-solutions"></a>Partnerbetöltési megoldások

Sok partnerünk rendelkezik rakodási megoldásokkal. További információkért tekintse meg [megoldáspartnereink](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)listáját.

## <a name="next-steps"></a>További lépések

A terhelési útmutatót lásd: [Útmutató a terhelési adatokhoz](data-loading-best-practices.md).
