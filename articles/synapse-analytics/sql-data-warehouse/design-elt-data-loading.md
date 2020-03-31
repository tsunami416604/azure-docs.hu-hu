---
title: Etl helyett tervezze meg az ELT-t
description: Rugalmas adatbetöltési stratégiák megvalósítása az SQL Analytics számára az Azure Synapse Analytics szolgáltatásban
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 0111f3edc3ab7a681e22153828d1bb430a14e57b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348991"
---
# <a name="data-loading-strategies-for-data-warehousing"></a>Adatbetöltési stratégiák az adattárházhoz

A hagyományos SMP-adatraktárak kivonat, átalakítás és betöltés (ETL) folyamatot használnak az adatok betöltéséhez. Az Azure Synapse Analytics SQL-készletei masszívan párhuzamos feldolgozási (MPP) architektúrával rendelkeznek, amely kihasználja a számítási és tárolási erőforrások méretezhetőségét és rugalmasságát. A kinyerési, betöltési és átalakítási (ELT) folyamat kihasználásával kihasználhatja az MPP előnyeit, és kiküszöbölheti az adatok betöltés előtti átalakításához szükséges erőforrásokat. Míg az SQL-készletek számos betöltési módszert támogatnak, beleértve a népszerű SQL Server-beállításokat, például a BCP-t és az SQL BulkCopy API-t, az adatok betöltésének leggyorsabb és leginkább skálázható módja a PolyBase külső tábláin és a [COPY utasításon](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (előzetes verzió) keresztül történik. A PolyBase és a COPY utasítás segítségével a T-SQL-nyelven keresztül hozzáférhet az Azure Blob storage-ban vagy az Azure Data Lake Store-ban tárolt külső adatokhoz. A betöltéskor a legnagyobb rugalmasság érdekében javasoljuk a COPY utasítás használatát.

> [!NOTE]  
> A COPY utasítás jelenleg nyilvános előzetes verzióban érhető el. Ha visszajelzést szeretne küldeni, küldjön sqldwcopypreview@service.microsoft.come-mailt a következő terjesztési listára: .


> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Mi az ELT?

A kinyerés, a betöltés és az átalakítás (ELT) egy olyan folyamat, amelynek során az adatokat egy forrásrendszerből kinyerik, egy adatraktárba töltik be, majd alakítják át. 

Az ELT megvalósításának alapvető lépései a következők:

1. A forrásadatok kinyerése szövegfájlokba.
2. Az adatokat az Azure Blob storage vagy az Azure Data Lake Store áruházba tárolhatja.
3. Készítse elő az adatokat a betöltésre.
4. Töltse be az adatokat átmeneti táblákba a PolyBase vagy a COPY paranccsal. 
5. Az adatok átalakítása.
6. Adatok beszúrása az éles üzemi táblákba.


A PolyBase betöltési oktatóanyagáról a [PolyBase használata adatok azure blobstorage-ból történő betöltése.](load-data-from-azure-blob-storage-using-polybase.md)

További információ: [Minták betöltése blog](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. A forrásadatok kibontása szöveges fájlokba

Az adatok kitárolása a tárolási helytől függ.  A cél az, hogy az adatokat áthelyezze a PolyBase és a COPY támogatott tagolt szövegbe vagy CSV fájlokba. 

### <a name="polybase-and-copy-external-file-formats"></a>PolyBase és másolása külső fájlformátumok

A PolyBase és a COPY utasítás segítségével utf-8 és UTF-16 kódolású szöveg- vagy CSV-fájlokból tölthet be adatokat. A tagolt szöveg vagy CSV fájlok mellett a Hadoop fájlformátumokból, például az ORC-ből és a Parkettából is betöltődik. A PolyBase és a COPY utasítás a Gzip és a Snappy tömörített fájlokból is képes adatokat betölteni. A kiterjesztett ASCII, a rögzített szélességű formátum és a beágyazott formátumok, például a WinZip vagy az XML nem támogatottak. Ha az SQL Server kiszolgálóról exportál, az [bcp parancssori eszközzel](/sql/tools/bcp-utility?view=azure-sqldw-latest) exportálhatja az adatokat tagolt szövegfájlokba. 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Az adatok letárolása az Azure Blob storage-ba vagy az Azure Data Lake Store-ba

Az Azure Storage-ban való letéréshez áthelyezheti [azazure blobtárolóba](../../storage/blobs/storage-blobs-introduction.md) vagy az [Azure Data Lake Store Gen2 szolgáltatásba.](../../data-lake-store/data-lake-store-overview.md) Az adatokat mindkét helyen szöveges fájlokban kell tárolni. A PolyBase és a COPY utasítás mindkét helyről betölthető.

Az adatok Azure Storage-ba való áthelyezéséhez használható eszközök és szolgáltatások:

- [Az Azure ExpressRoute](../../expressroute/expressroute-introduction.md) szolgáltatás javítja a hálózati átviteli teljesítményt, a teljesítményt és a kiszámíthatóságot. Az ExpressRoute egy olyan szolgáltatás, amely dedikált privát kapcsolaton keresztül továbbítja az adatokat az Azure-hoz. Az ExpressRoute-kapcsolatok nem továbbítják az adatokat a nyilvános interneten keresztül. A kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késéseket és nagyobb biztonságot nyújtanak, mint a nyilvános interneten keresztüli tipikus kapcsolatok.
- [Az AZCopy segédprogram](../../storage/common/storage-choose-data-transfer-solution.md) az adatokat a nyilvános interneten keresztül helyezi át az Azure Storage-ba. Ez akkor működik, ha az adatméretek 10 TB-nál kisebbek. Ha az AZCopy segítségével rendszeresen szeretné végrehajtani a terheléseket, ellenőrizze a hálózati sebességet, hogy elfogadható-e. 
- [Az Azure Data Factory (ADF)](../../data-factory/introduction.md) rendelkezik egy átjáróval, amely a helyi kiszolgálón telepíthető. Ezután létrehozhat egy folyamatot az adatok áthelyezéséhez a helyi kiszolgálóról az Azure Storage-ba. A Data Factory és az SQL Analytics használatához olvassa el [az Adatok betöltése az SQL Analytics szolgáltatáshoz](../../data-factory/load-azure-sql-data-warehouse.md)című témakört.


## <a name="3-prepare-the-data-for-loading"></a>3. Készítse elő az adatokat a betöltésre

Előfordulhat, hogy a betöltés előtt elő kell készítenie és meg kell tisztítania a tárfiókban lévő adatokat. Az adatok előkészítése akkor is elvégezhető, ha az adatok a forrásban vannak, miközben exportálja az adatokat szöveges fájlokba, vagy miután az adatok az Azure Storage-ban vannak.  Az adatokkal a lehető leghamarabb a legegyszerűbb dolgozni.  

### <a name="define-external-tables"></a>Külső táblák definiálása

Ha PolyBase-t használ, a betöltés előtt meg kell határoznia a külső táblákat az adatraktárban. A COPY utasítás nem igényel külső táblákat. A PolyBase külső táblákat használ az Azure Storage-adatok definiálására és elérésére. Egy külső tábla hasonló az adatbázisnézethez. A külső tábla tartalmazza a táblasémát, és az adatraktáron kívül tárolt adatokra mutat. 

A külső táblák megadása magában foglalja az adatforrás, a szövegfájlok formátumának és a táblázatdefiníciók megadását. A Szükséges T-SQL szintaxis témakörök a következők:
- [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

A Parquet betöltésekor az SQL adattípus-leképezés a következő:

| **Parketta adattípusa** | **SQL adattípus** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          int          |        int        |
|        bigint         |      bigint       |
|        logikai        |        bit        |
|        double         |       lebegőpontos       |
|         lebegőpontos         |       real        |
|        double         |       Pénzt       |
|        double         |    kis pénz     |
|        sztring         |       nchar       |
|        sztring         |     nvarchar      |
|        sztring         |       Char        |
|        sztring         |      varchar      |
|        binary         |      binary       |
|        binary         |     varbinary között     |
|       időbélyeg       |       dátum        |
|       időbélyeg       |   smalldatetime   |
|       időbélyeg       |     datetime2     |
|       időbélyeg       |     dátum/idő      |
|       időbélyeg       |       time        |
|         dátum          |       dátum        |
|        tizedes tört        |      tizedes tört      |

A külső objektumok létrehozásának például a Betöltési oktatóanyag [Külső táblák létrehozása](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) című témakörben található.

### <a name="format-text-files"></a>Szövegfájlok formázása

PolyBase használata esetén a definiált külső objektumoknak a szövegfájlok sorait a külső táblázathoz és a fájlformátum-definícióhoz kell igazítaniuk. A szövegfájl egyes sorának adatainak igazodniuk kell a táblázat definíciójához.
A szövegfájlok formázása:

- Ha az adatok nem relációs forrásból származnak, sorokká és oszlopokká kell alakítania azadatokat. Függetlenül attól, hogy az adatok relációs vagy nem relációs forrásból származnak, az adatokat úgy kell átalakítani, hogy igazodjanak annak a táblának az oszlopdefinícióihoz, amelybe az adatokat be kívánja tölteni. 
- A szövegfájlban lévő adatok formázásával igazodjon a céltábla oszlopaihoz és adattípusaihoz. A külső szövegfájlokban lévő adattípusok és az adattárház-tábla közötti eltérés hatására a program elutasítja a sorokat a terhelés során.
- A szövegfájl mezőinek elkülönítése terminátorral.  Ügyeljen arra, hogy olyan karaktert vagy karaktersorozatot használjon, amely nem található meg a forrásadatokban. Használja a [külső fájlformátum létrehozása](/sql/t-sql/statements/create-external-file-format-transact-sql)mezőben megadott terminátort.


## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Töltse be az adatokat a PolyBase vagy a COPY utasítás használatával

Ajánlott adatokat betölteni egy átmeneti táblába. Az átmeneti táblák lehetővé teszik a hibák kezelését a termelési táblák beavatkozása nélkül. Az átmeneti tábla is lehetőséget ad arra, hogy az SQL-készlet MPP adatátalakítások, mielőtt az adatokat éles táblákba. A táblát előre létre kell hozni, amikor egy MÁSOLÁSsal rendelkező átmeneti táblába tölti be.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>A PolyBase és copy utasítással való betöltés beállításai

Az adatok PolyBase segítségével az alábbi betöltési lehetőségek bármelyikét használhatja:

- [A PolyBase t-SQL-t](load-data-from-azure-blob-storage-using-polybase.md) használ, ha az adatok az Azure Blob storage vagy az Azure Data Lake Store tárolóban vannak. Ez biztosítja a legtöbb ellenőrzést a betöltési folyamat, hanem megköveteli, hogy meghatározza a külső adatobjektumokat. A többi módszer határozza meg ezeket az objektumokat a színfalak mögött, amikor a forrástáblákat a céltáblákhoz rendeli.  A T-SQL-betöltések vezényléséhez használhatja az Azure Data Factory, az SSIS vagy az Azure-függvényeket. 
- [Az SSIS-sel rendelkező PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse) jól működik, ha a forrásadatok az SQL Server ben vannak, akár a helyszíni SQL Server ben, akár a felhőben. Az SSIS határozza meg a forrás és a céltábla-hozzárendeléseket, és vezényli a terhelést is. Ha már rendelkezik SSIS-csomagokkal, módosíthatja a csomagokat, hogy működjenek együtt az új adattárház célállomással. 
- [PolyBase és copy utasítás az Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md) egy másik vezénylési eszköz.  Meghatározza a folyamatot, és ütemezi a feladatokat. 
- [Az Azure Databricks-szel rendelkező PolyBase](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md) adatokat továbbít egy táblából egy Databricks-adatkeretbe, és/vagy adatokat ír egy Databricks-adatkeretből egy táblába a PolyBase használatával.

### <a name="other-loading-options"></a>Egyéb berakodási lehetőségek

A PolyBase és a COPY utasítás mellett használhatja a [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest) vagy az [SQLBulkCopy API-t](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)is. A bcp közvetlenül az adatbázisba töltődik be anélkül, hogy az Azure Blob-tárolón keresztül haladna, és csak kis terhelésekhez készült. Ne feledje, hogy ezeknek a beállításoknak a terhelési teljesítménye lassabb, mint a PolyBase és a COPY utasítás. 


## <a name="5-transform-the-data"></a>5. Az adatok átalakítása

Amíg az adatok az átmeneti táblában vannak, hajtsa végre a számítási feladatok által igényelt átalakításokat. Ezután helyezze át az adatokat egy termelési táblába.


## <a name="6-insert-the-data-into-production-tables"></a>6. Az adatok beszúrása a termelési táblázatokba

A betét ... SELECT utasítás az átmeneti táblából az állandó táblába helyezi át az adatokat. 

EtL-folyamat tervezésekor próbálja meg futtatni a folyamatot egy kis tesztmintán. Próbálja meg kibontani 1000 sort a táblából egy fájlba, helyezze át az Azure-ba, majd próbálja meg betölteni egy átmeneti táblába. 


## <a name="partner-loading-solutions"></a>Partnerbetöltési megoldások

Sok partnerünk rendelkezik rakodási megoldásokkal. További információkért tekintse meg [megoldáspartnereink](sql-data-warehouse-partner-business-intelligence.md)listáját. 


## <a name="next-steps"></a>További lépések

Útmutatás a betöltéshez: [Útmutató az adatok betöltéséhez](guidance-for-loading-data.md).
