---
title: ETL helyett a design ELT
description: Rugalmas betöltési stratégiák implementálása az SQL analyticshez az Azure szinapszis Analyticsen belül
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bd356fba557d61f083e811c8763b4e7cf9805fbb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199937"
---
# <a name="data-loading-strategies-for-data-warehousing"></a>Adattárház-adatgyűjtési stratégiák

A hagyományos SMP-adattárházak kinyerési, átalakítási és betöltési (ETL) folyamatot használnak az adatok betöltéséhez. Az Azure szinapszis Analyticsben az SQL-készletek egy nagymértékben párhuzamos feldolgozási (MPP) architektúrával rendelkeznek, amely kihasználja a számítási és tárolási erőforrások méretezhetőségét és rugalmasságát. A kinyerési, betöltési és átalakítási (ELT) folyamat használatával kihasználhatja az MPP előnyeit, és kiszűrheti az adatoknak a betöltés előtt való átalakításához szükséges erőforrásokat. Míg az SQL-készletek számos betöltési módszert támogatnak, beleértve a népszerű SQL Server lehetőségeket, például a BCP-t és az SQL bulkcopy objektum céloszlopa API-t, az adatok betöltésének leggyorsabb és leginkább méretezhető módja a többes számú külső táblák és a [copy utasítás](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (előzetes verzió). A Base és a COPY utasítás használatával az Azure Blob Storage-ban vagy Azure Data Lake Storeban tárolt külső adatokhoz a T-SQL nyelv használatával férhet hozzá. A betöltés legrugalmasabban a MÁSOLÁSi utasítás használata javasolt.

> [!NOTE]  
> A COPY utasítás jelenleg nyilvános előzetes verzióban érhető el. Ha visszajelzést szeretne küldeni, küldjön e-mailt a következő terjesztési listára: sqldwcopypreview@service.microsoft.com.


> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Mi az a ELT?

A kinyerési, betöltési és átalakítási (ELT) folyamat során a rendszer kinyeri az adatforrásrendszer adatait, betölti azokat egy adattárházba, majd átalakítja azokat. 

A ELT megvalósításának alapvető lépései a következők:

1. A forrásadatok szövegfájlba való kinyerése.
2. Az Azure Blob Storage-ba vagy Azure Data Lake Storeba helyezheti az adatterületet.
3. Az adatgyűjtés előkészítése a betöltéshez.
4. Töltse be az adatokba az átmeneti táblákat a Base vagy a COPY paranccsal. 
5. Alakítsa át az adathalmazt.
6. Szúrja be az adatbevitelt az éles táblákba.


A Base betöltésével kapcsolatos oktatóanyagért lásd: az [adatok Azure Blob Storage-ból való betöltésének használata](load-data-from-azure-blob-storage-using-polybase.md).

További információ: [minták betöltése blog](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. a forrásadatok kibontása szövegfájlba

A forrásrendszer adatokból való beolvasása a tárterület helyétől függ.  A cél az adatok alapszintű és a másolás támogatott tagolt szöveg-vagy CSV-fájlokba való áthelyezése. 

### <a name="polybase-and-copy-external-file-formats"></a>Kiinduló és külső fájlformátumok másolása

A Base és a COPY utasítás használatával az adatok az UTF-8 és az UTF-16 kódolású tagolt szövegből vagy CSV-fájlokból tölthetők be. A tagolt szöveg-vagy CSV-fájlok mellett a Hadoop-fájlformátumokból, például az ORKből és a Parkettaből is betöltődik. A Base és a COPY utasítás a gzip és a Snappy tömörített fájlok adatait is képes betölteni. A bővített ASCII, a rögzített szélességű formátum és a beágyazott formátumok (például a WinZip vagy az XML) nem támogatottak. Ha SQL Serverból exportál, a [BCP parancssori eszköz](/sql/tools/bcp-utility?view=azure-sqldw-latest) használatával tagolt szövegfájlba exportálhatja az adatok mennyiségét. 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. az adatgyűjtés az Azure Blob Storage-ba vagy Azure Data Lake Store

Az Azure Storage-ban tárolt adatterületek áthelyezéséhez áthelyezheti azt az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) -ba vagy [Azure Data Lake Store Gen2](../data-lake-store/data-lake-store-overview.md). Mindkét helyen az adatfájlokat szövegfájlokban kell tárolni. A Base és a COPY utasítás bármelyik helyről tölthető be.

Az Azure Storage-ba történő adatáthelyezéshez használható eszközök és szolgáltatások:

- Az [Azure ExpressRoute](../expressroute/expressroute-introduction.md) szolgáltatás javítja a hálózat teljesítményét, a teljesítményt és a kiszámíthatóságot. Az ExpressRoute egy szolgáltatása, amely az adatok egy dedikált privát kapcsolaton keresztül irányítja az Azure-bA. A ExpressRoute-kapcsolatok nem a nyilvános interneten keresztül irányítják az adattovábbítást. A kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok a nyilvános interneten.
- A [AZCopy segédprogram](../storage/common/storage-moving-data.md) az Azure Storage-ba helyezi át az adatátvitelt a nyilvános interneten keresztül. Ez akkor működik, ha az adatméretek 10 TB-nál kisebbek. A AZCopy rendszeres betöltéséhez tesztelje a hálózati sebességet, és ellenőrizze, hogy elfogadható-e. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) olyan átjáróval rendelkezik, amelyet telepíthet a helyi kiszolgálóra. Ezután létrehozhat egy folyamatot, amely a helyi kiszolgálóról az Azure Storage-ba helyezi át az adatok áthelyezését. Ha az SQL Analytics szolgáltatással szeretne Data Factory használni, olvassa el az [SQL Analytics-beli adatbetöltése](/azure/data-factory/load-azure-sql-data-warehouse)című témakört.


## <a name="3-prepare-the-data-for-loading"></a>3. az adatgyűjtés előkészítése a betöltéshez

Előfordulhat, hogy a betöltés előtt elő kell készítenie és meg kell tisztítania a Storage-fiókban tárolt adatait. Az adatelőkészítés elvégezhető, miközben az adatai a forrásban vannak, miközben az adatait szövegfájlba exportálja, vagy az Azure Storage-ban tárolt adatai.  A lehető leghamarabb dolgozhat az adatkezelési folyamatokkal.  

### <a name="define-external-tables"></a>Külső táblák definiálása

Ha a Base-t használja, a betöltés előtt meg kell határoznia a külső táblákat az adattárházban. A COPY utasítás nem igényli a külső táblákat. A Base külső táblákat használ az Azure Storage-ban tárolt adatok definiálásához és eléréséhez. Egy külső tábla hasonlít egy adatbázis-nézethez. A külső tábla tartalmazza a tábla sémáját, és az adatraktáron kívül tárolt adatértékekre mutat. 

A külső táblák meghatározása magában foglalja az adatforrás megadását, a szövegfájlok formátumát és a tábla definícióit. A T-SQL szintaxisához szükséges témakörök a következők:
- [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

A parketta betöltésekor az SQL adattípus-leképezés a következő:

| **Parketta adattípusa** | **SQL-adattípus** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          int          |        int        |
|        bigint         |      bigint       |
|        Logikai érték        |        bit        |
|        double         |       lebegőpontos       |
|         lebegőpontos         |       real        |
|        double         |       money       |
|        double         |    smallmoney     |
|        Karakterlánc         |       nchar       |
|        Karakterlánc         |     nvarchar      |
|        Karakterlánc         |       char        |
|        Karakterlánc         |      varchar      |
|        binary         |      binary       |
|        binary         |     varbinary     |
|       időbélyeg       |       dátum        |
|       időbélyeg       |   smalldatetime   |
|       időbélyeg       |     datetime2     |
|       időbélyeg       |     dátum/idő      |
|       időbélyeg       |       time        |
|         dátum          |       dátum        |
|        tizedes tört        |      tizedes tört      |

A külső objektumok létrehozásának példáját a betöltési útmutató [külső táblák létrehozása](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) lépésében tekintheti meg.

### <a name="format-text-files"></a>Szövegfájlok formázása

Ha a Base-t használja, a külső objektumoknak meg kell igazítaniuk a szövegfájl sorait a külső tábla és a fájlformátum definíciója szerint. A szövegfájl egyes soraiban lévő összes értéknek a tábla definícióját kell igazítania.
A szövegfájlok formázása:

- Ha az adatok nem összehasonlítható forrásból származnak, át kell alakítania azokat sorokra és oszlopokra. Függetlenül attól, hogy az adatok egy rokon vagy nem rokon forrásból származnak-e, az adatoknak át kell alakítani a táblázat azon oszlopainak definícióját, amelybe az adatok betöltését tervezi. 
- Formázza a szövegfájlban lévő adattípusokat, hogy a cél táblában lévő oszlopokkal és adattípusokkal igazodjanak. A külső szövegfájlokban lévő adattípusok és az adatraktár tábla közötti helytelen igazítás a sorok elutasítását okozza a terhelés során.
- Külön mezők a szövegfájlban egy lezáró fájllal.  Ügyeljen arra, hogy olyan karaktert vagy karaktert használjon, amely nem található a forrásadatok között. Használja a [külső fájlformátum létrehozásakor](/sql/t-sql/statements/create-external-file-format-transact-sql)megadott lezárót.


## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. az adatok betöltése a Base vagy a COPY utasítás használatával

Az ajánlott eljárás az, ha az adatgyűjtést egy előkészítési táblába tölti be. Az előkészítési táblázatok lehetővé teszik a hibák kezelését az éles táblákkal való beavatkozás nélkül. Az előkészítési táblázat lehetővé teszi, hogy az SQL Pool MPP-t használja az adatátalakításokhoz, mielőtt az adatfeldolgozási táblákba beszúrja azokat. A táblázatot előre létre kell hozni a MÁSOLÁSt végző előkészítési táblába való betöltéskor.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>A Base és a COPY utasítás betöltésének lehetőségei

Az adatok alapszintű betöltéséhez használhatja a következő betöltési lehetőségeket:

- A [T-SQL-](load-data-from-azure-blob-storage-using-polybase.md) sel jól használható, ha az adatok Azure Blob Storage-ban vagy Azure Data Lake Storeban vannak. A leghatékonyabban szabályozhatja a betöltési folyamatot, de külső adatobjektumokat is meg kell határoznia. A többi módszer ezeket az objektumokat a háttérben úgy definiálja, ahogy a forrástábla táblázatokra van leképezve.  A T-SQL-terhelések összehangolása érdekében Azure Data Factory, SSIS vagy Azure functions-T használhat. 
- A [SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) jól működik, ha a forrásadatok SQL Server, vagy SQL Server a helyszínen vagy a felhőben. A SSIS meghatározza a forrást a céltábla leképezéséhez, és összehangolja a terhelést is. Ha már rendelkezik SSIS-csomagokkal, a csomagokat módosíthatja úgy, hogy az új adattárház-célhelyen működjenek. 
- [A Base és a copy utasítás Azure Data Factory (ADF) egy másik összehangoló](sql-data-warehouse-load-with-data-factory.md) eszköz.  Meghatározza a folyamatokat és az ütemezett feladatokat. 
- A [Azure Databricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) a tábla adatait egy Databricks-dataframe továbbítja, és/vagy egy Databricks-dataframe származó adatot ír be egy tábla használatával.

### <a name="other-loading-options"></a>Egyéb betöltési beállítások

A Base és a COPY utasítás mellett a [BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest) vagy a [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)is használható. a BCP az Azure Blob Storage szolgáltatáson keresztül közvetlenül az adatbázisba töltődik be, és csak kis terhelések esetén használható. Vegye figyelembe, hogy ezeknek a beállításoknak a betöltési teljesítménye lassabb, mint a "Base" és a "COPY" utasítás. 


## <a name="5-transform-the-data"></a>5. az adatátalakítás

Amíg az adatok az előkészítési táblában vannak, hajtsa végre a számítási feladatok által igényelt átalakításokat. Ezután helyezze át az adatátvitelt egy éles táblába.


## <a name="6-insert-the-data-into-production-tables"></a>6. az adattábla beillesztése a termelési táblákba

A Beszúrás a (...) A SELECT utasítás az átmeneti tábla adatait az állandó táblába helyezi át. 

Az ETL-folyamat tervezésekor próbálja meg futtatni a folyamatot egy kis tesztelési mintán. Próbálja meg kibontani a 1000 sort a táblából egy fájlba, helyezze át az Azure-ba, majd próbálja meg betölteni egy előkészítési táblába. 


## <a name="partner-loading-solutions"></a>Partneri betöltési megoldások

Számos partnerünk betöltési megoldásokkal rendelkezik. További információért tekintse meg a [megoldás partnereink](sql-data-warehouse-partner-business-intelligence.md)listáját. 


## <a name="next-steps"></a>További lépések

Útmutatás a betöltéshez: [Útmutató az adatok betöltéséhez](guidance-for-loading-data.md).
