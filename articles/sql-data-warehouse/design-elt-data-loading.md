---
title: ETL helyett a Azure SQL Data Warehouse tervezési ELT | Microsoft Docs
description: ETL helyett a kinyerési, betöltési és átalakítási (ELT) folyamatot kell megterveznie az adatok vagy Azure SQL Data Warehouse betöltéséhez.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 07/28/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: c248a2e3e6724388fa6402a70ac3bcb51f0f9ef3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492248"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-sql-data-warehouse"></a>Alapszintű betöltési stratégia tervezése Azure SQL Data Warehouse számára

A hagyományos SMP-adattárházak kinyerési, átalakítási és betöltési (ETL) folyamatot használnak az adatok betöltéséhez. Azure SQL Data Warehouse egy nagymértékben párhuzamos feldolgozási (MPP) architektúra, amely kihasználja a számítási és tárolási erőforrások méretezhetőségét és rugalmasságát. A kinyerési, betöltési és átalakítási (ELT) folyamat használatával kihasználhatja az MPP előnyeit, és kiszűrheti az adatoknak a betöltés előtt való átalakításához szükséges erőforrásokat. Míg a SQL Data Warehouse számos betöltési módszert támogat, többek között a nem alapszintű beállításokat, például a BCP-t és az SQL bulkcopy objektum céloszlopa API-t, a leggyorsabban és leginkább méretezhető módon a betöltési idő a következő  A Base egy olyan technológia, amely az Azure Blob Storage-ban vagy Azure Data Lake Storeban tárolt külső adatokhoz fér hozzá a T-SQL nyelv használatával.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Mi az a ELT?

A kinyerési, betöltési és átalakítási (ELT) folyamat során a rendszer egy adattárházba tölti ki az adatok kinyerését, majd átalakítja azokat. 

A SQL Data Warehouse-alapú ELT megvalósításának alapvető lépései a következők:

1. A forrásadatok szövegfájlba való kinyerése.
2. Az Azure Blob Storage-ba vagy Azure Data Lake Storeba helyezheti az adatterületet.
3. Az adatgyűjtés előkészítése a betöltéshez.
4. Az adatok betöltése a SQL Data Warehouse-előkészítési táblákba a Base használatával. 
5. Alakítsa át az adathalmazt.
6. Szúrja be az adatbevitelt az éles táblákba.


Betöltési oktatóanyagért lásd: az [Azure Blob Storage-ból való adatok betöltésének használata a Azure SQL Data Warehouseba](load-data-from-azure-blob-storage-using-polybase.md).

További információ: [minták betöltése blog](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. a forrásadatok kibontása szövegfájlba

A forrásrendszer adatokból való beolvasása a tárterület helyétől függ.  A cél az, hogy az adatok a következőre legyenek áthelyezve: Base támogatott tagolt szövegfájlok. 

### <a name="polybase-external-file-formats"></a>Alapszintű külső fájlformátumok

A kiinduló adatok az UTF-8 és az UTF-16 kódolású, tagolt szövegfájlokból is betöltődik. A tagolt szövegfájlok mellett az RC-fájlból, az ORKből és a Parkettaből is betöltődik a Hadoop fájlformátum. A rendszer a gzip és a Snappy tömörített fájlok adatait is képes betölteni. A Base jelenleg nem támogatja a bővített ASCII, a rögzített szélességű formátumot és a beágyazott formátumokat, például a WinZip, a JSON és az XML formátumot. Ha SQL Serverból exportál, a [BCP parancssori eszköz](/sql/tools/bcp-utility) használatával tagolt szövegfájlba exportálhatja az adatok mennyiségét. A Parquet és az SQL DW adattípusának megfeleltetése a következő:

| **Parketta adattípusa** |                      **SQL-adattípus**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        logikai        |                             bites                              |
|        double         |                            lebegőpontos                             |
|         lebegőpontos         |                             valós                             |
|        double         |                            pénzt                             |
|        double         |                          túlcsordulási                          |
|        sztring         |                            NCHAR                             |
|        sztring         |                           nvarchar                           |
|        sztring         |                             char                             |
|        sztring         |                           varchar                            |
|        Bináris         |                            Bináris                            |
|        Bináris         |                          varbinary                           |
|       időbélyeg       |                             dátum                             |
|       időbélyeg       |                        idő adattípusúra                         |
|       időbélyeg       |                          datetime2                           |
|       időbélyeg       |                           dátum/idő                           |
|       időbélyeg       |                             time                             |
|       dátum            |                             dátum                             |
|        tizedes tört        |                            tizedes tört                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. az adatgyűjtés az Azure Blob Storage-ba vagy Azure Data Lake Store

Az Azure Storage-ban tárolt adatterületek áthelyezéséhez áthelyezheti azt az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) -ba vagy [Azure Data Lake Storeba](../data-lake-store/data-lake-store-overview.md). Mindkét helyen az adatfájlokat szövegfájlokban kell tárolni. A Base bármelyik helyről betölthető.

Az Azure Storage-ba történő adatáthelyezéshez használható eszközök és szolgáltatások:

- Az [Azure ExpressRoute](../expressroute/expressroute-introduction.md) szolgáltatás javítja a hálózat teljesítményét, a teljesítményt és a kiszámíthatóságot. A ExpressRoute egy olyan szolgáltatás, amely az Azure-hoz való dedikált privát kapcsolatban továbbítja az adatait. A ExpressRoute-kapcsolatok nem a nyilvános interneten keresztül irányítják az adattovábbítást. A kapcsolatok nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot biztosítanak, mint a szokásos kapcsolatok a nyilvános interneten.
- A [AZCopy segédprogram](../storage/common/storage-moving-data.md) az Azure Storage-ba helyezi át az adatátvitelt a nyilvános interneten keresztül. Ez akkor működik, ha az adatméretek 10 TB-nál kisebbek. A AZCopy rendszeres betöltéséhez tesztelje a hálózati sebességet, és ellenőrizze, hogy elfogadható-e. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) olyan átjáróval rendelkezik, amelyet telepíthet a helyi kiszolgálóra. Ezután létrehozhat egy folyamatot, amely a helyi kiszolgálóról az Azure Storage-ba helyezi át az adatok áthelyezését. Ha a Data Factoryt SQL Data Warehouse használatával szeretné használni, olvassa el a következő témakört: [az Adatterhelés a SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. az adatgyűjtés előkészítése a betöltéshez

Előfordulhat, hogy az SQL Data Warehouseba való betöltés előtt elő kell készítenie és meg kell tisztítania a Storage-fiókban tárolt adatait. Az adatelőkészítés elvégezhető, miközben az adatai a forrásban vannak, miközben az adatait szövegfájlba exportálja, vagy az Azure Storage-ban tárolt adatai.  A lehető leghamarabb dolgozhat az adatkezelési folyamatokkal.  

### <a name="define-external-tables"></a>Külső táblák definiálása

Az adatbetöltése előtt meg kell határoznia a külső táblákat az adattárházban. A Base külső táblákat használ az Azure Storage-ban tárolt adatok definiálásához és eléréséhez. Egy külső tábla hasonlít egy adatbázis-nézethez. A külső tábla tartalmazza a tábla sémáját, és az adatraktáron kívül tárolt adatértékekre mutat. 

A külső táblák meghatározása magában foglalja az adatforrás megadását, a szövegfájlok formátumát és a tábla definícióit. Ezek a T-SQL szintaxissal kapcsolatos témakörök, amelyekre szüksége lesz:
- [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql)

A külső objektumok létrehozásának példáját a betöltési útmutató [külső táblák létrehozása](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) lépésében tekintheti meg.

### <a name="format-text-files"></a>Szövegfájlok formázása

A külső objektumok meghatározása után a szövegfájl sorait a külső tábla és a fájlformátum definíciója szerint kell összehangolni. A szövegfájl egyes soraiban lévő összes értéknek a tábla definícióját kell igazítania.
A szövegfájlok formázása:

- Ha az adatok nem összehasonlítható forrásból származnak, át kell alakítania azokat sorokra és oszlopokra. Függetlenül attól, hogy az adatok egy rokon vagy nem rokon forrásból származnak-e, az adatoknak át kell alakítani a táblázat azon oszlopainak definícióját, amelybe az adatok betöltését tervezi. 
- Formázza a szövegfájlban található adattípusokat úgy, hogy az a SQL Data Warehousei céltábla oszlopaihoz és adattípusaihoz legyen igazítva. A külső szövegfájlokban lévő adattípusok és az adatraktár tábla közötti helytelen igazítás a sorok elutasítását okozza a terhelés során.
- Külön mezők a szövegfájlban egy lezáró fájllal.  Ügyeljen arra, hogy olyan karaktert vagy karaktert használjon, amely nem található a forrásadatok között. Használja a [külső fájlformátum létrehozásakor](/sql/t-sql/statements/create-external-file-format-transact-sql)megadott lezárót.


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase"></a>4. az adatok betöltése a SQL Data Warehouse-előkészítési táblákba a Base használatával

Az ajánlott eljárás az, ha az adatgyűjtést egy előkészítési táblába tölti be. Az előkészítési táblázatok lehetővé teszik a hibák kezelését az éles táblákkal való beavatkozás nélkül. Az előkészítési táblázat lehetővé teszi, hogy az adatátalakításhoz SQL Data Warehouse MPP-t használjon, mielőtt beilleszti az adatfeldolgozási táblákba.

### <a name="options-for-loading-with-polybase"></a>A-alapú betöltés lehetőségei

Az adatok alapszintű betöltéséhez használhatja a következő betöltési lehetőségeket:

- A [T-SQL-](load-data-from-azure-blob-storage-using-polybase.md) sel jól használható, ha az adatok Azure Blob Storage-ban vagy Azure Data Lake Storeban vannak. A leghatékonyabban szabályozhatja a betöltési folyamatot, de külső adatobjektumokat is meg kell határoznia. A többi módszer ezeket az objektumokat a háttérben úgy definiálja, ahogy a forrástábla táblázatokra van leképezve.  A T-SQL-terhelések összehangolása érdekében Azure Data Factory, SSIS vagy Azure functions-T használhat. 
- A [SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) jól működik, ha a forrásadatok SQL Server, vagy SQL Server a helyszínen vagy a felhőben. A SSIS meghatározza a forrást a céltábla leképezéséhez, és összehangolja a terhelést is. Ha már rendelkezik SSIS-csomagokkal, a csomagokat módosíthatja úgy, hogy az új adattárház-célhelyen működjenek. 
- [A Azure Data Factory (ADF) használatával egy másik összehangoló](sql-data-warehouse-load-with-data-factory.md) eszköz van.  Meghatározza a folyamatokat és az ütemezett feladatokat. 
- A [Azure Databricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) a SQL Data Warehouse táblák adatait egy Databricks dataframe továbbítja, és/vagy egy Databricks-dataframe származó adatot ír egy SQL Data Warehouse táblába, a Base használatával.

### <a name="non-polybase-loading-options"></a>Nem albase betöltési beállítások

Ha az adatok nem kompatibilisek a SQLBulkCopy, használhatja a [BCP](/sql/tools/bcp-utility) -t vagy a [API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx)-t. a BCP közvetlenül az Azure Blob Storage-ba való SQL Data Warehouse nélkül töltődik be, és kizárólag kis terhelésekhez készült. Vegye figyelembe, hogy ezeknek a beállításoknak a betöltési teljesítménye jóval lassabb, mint a kiinduló. 


## <a name="5-transform-the-data"></a>5. az adatátalakítás

Amíg az adatok az előkészítési táblában vannak, hajtsa végre a számítási feladatok által igényelt átalakításokat. Ezután helyezze át az adatátvitelt egy éles táblába.


## <a name="6-insert-the-data-into-production-tables"></a>6. az adattábla beillesztése a termelési táblákba

A Beszúrás a (...) A SELECT utasítás az átmeneti tábla adatait az állandó táblába helyezi át. 

Az ETL-folyamat tervezésekor próbálja meg futtatni a folyamatot egy kis tesztelési mintán. Próbálja meg kibontani a 1000 sort a táblából egy fájlba, helyezze át az Azure-ba, majd próbálja meg betölteni egy előkészítési táblába. 


## <a name="partner-loading-solutions"></a>Partneri betöltési megoldások

Számos partnerünk betöltési megoldásokkal rendelkezik. További információért tekintse meg a [megoldás partnereink](sql-data-warehouse-partner-business-intelligence.md)listáját. 


## <a name="next-steps"></a>További lépések

Útmutatást a [betöltési útmutatóban](guidance-for-loading-data.md)talál.


