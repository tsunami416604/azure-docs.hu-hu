---
title: Az OPENROWSET használata az SQL on-demand-ben (előzetes verzió)
description: Ez a cikk az OPENROWSET szintaxisát ismerteti az SQL on-demand (előzetes verzió) rendszerben, és ismerteti az argumentumok használatát.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680489"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Az OPENROWSET használata az SQL on-demand használatával (előzetes verzió)

Az OPENROWSET(BULK...) funkció lehetővé teszi a fájlok elérését az Azure Storage-ban. Az SQL on-demand (előzetes verzió) erőforráson belül az OPENROWSET tömeges sorhalmaz-szolgáltató az OPENROWSET függvény hívásával és a TÖMEGES beállítás megadásával érhető el.  

Az OPENROWSET függvényre úgy lehet hivatkozni a lekérdezés FROM záradékában, mintha egy OPENROWSET táblanév lenne. Támogatja a tömeges műveletek et egy beépített BULK szolgáltatón keresztül, amely lehetővé teszi a fájlból származó adatok olvasását és sorokhalmazként való visszaadását.

Az OPENROWSET jelenleg nem támogatott az SQL készletben.

## <a name="syntax"></a>Szintaxis

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>Argumentumok

A lekérdezéshez a céladatokat tartalmazó bemeneti fájlok esetében két lehetőség közül választhat. Az érvényes értékek a következők:

- "CSV" - Tartalmazza a sor-/oszlopelválasztóval tagolt szövegfájlt. Bármilyen karakter használható mezőelválasztóként, például TSV: FIELDTERMINATOR = tabulátor.

- "PARQUET" - Bináris fájl parketta formátumban 

**"unstructured_data_path"**

Az adatok elérési útját létrehozó unstructured_data_path a következőképpen épül fel:  
'\<előtag\<>://\<storage_account_path>/ storage_path>"
 
 
 Az alábbiakban megtalálja a megfelelő tárfiók elérési útjait, amelyek az adott külső adatforráshoz kapcsolódnak. 

| Külső adatforrás       | Előtag | Tárfiók elérési útja                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | https  | \<storage_account>.blob.core.windows.net             |
| Azure Data Lake Áruház Gen1 | https  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Áruház Gen2 | https  | \<storage_account>.dfs.core.windows.net              |
||||

"\<storage_path>"

 Megadja a tárolón belüli elérési utat, amely az olvasni kívánt mappára vagy fájlra mutat. Ha a görbe egy tárolóra vagy mappára mutat, a program az összes fájlt az adott tárolóból vagy mappából olvassa be. Az almappákban lévő fájlok nem lesznek benne. 
 
 Helyettesítő karakterek használatával több fájlt vagy mappát is megcélozhat. Több nem egymást követő helyettesítő karakter használata engedélyezett.
Az alábbi példa az összes *csv-fájlt* beolvassa, kezdve a */csv/population*kapcsolóval kezdődő mappák *populációjával:*  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Ha a unstructured_data_path mappának adja meg, az SQL igény szerinti lekérdezése fájlokat fog beolvasni a mappából. 

> [!NOTE]
> A Hadooptól és a PolyBase-től eltérően az igény szerinti SQL nem ad vissza almappákat. A Hadooptól és a PloyBase-től eltérően az SQL igény szerinti fájlja idoben olyan fájlokat ad vissza, amelyek fájlneve aláhúzással (_) vagy pont (.) karakterrel kezdődik.

Az alábbi példában, ha`https://mystorageaccount.dfs.core.windows.net/webdata/`a unstructured_data_path= , az SQL on-demand lekérdezés sorokat ad vissza a mydata.txt és a _hidden.txt fájlból. Nem ad vissza a mydata2.txt fájlt és a mydata3.txt fájlt, mert almappában találhatók.

![Külső táblák rekurzív adatai](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

A WITH záradék lehetővé teszi a fájlokból olvasni kívánt oszlopok megadását.

- A CSV-adatfájlok esetében az összes oszlop olvasásához adja meg az oszlopneveket és azok adattípusait. Ha az oszlopok egy részét szeretné használni, sorszámokkal válassza ki az oszlopokat az eredeti adatfájlokból sorszám szerint. Az oszlopokat a sormegjelölés köti. 

> [!IMPORTANT]
> A WITH záradék kötelező a CSV-fájlokesetében.
- A Parketta-adatfájlok esetében adja meg az okat, amelyek megfelelnek az eredeti adatfájlok oszlopnevének. Az oszlopokat név köti. Ha a WITH záradék nincs megadva, a program a Programozó fájlok összes oszlopát visszaadja.

column_name = A kimeneti oszlop neve. Ha meg van adva, ez a név felülbírálja a forrásfájloszlop nevét.

column_type = A kimeneti oszlop adattípusa. Az implicit adattípus-átalakítás itt történik.

column_ordinal = a forrásfájl(ok) oszlopának sorszáma. Ez az argumentum figyelmen kívül hagyja a Parketta fájlokat, mivel a kötés név szerint történik. A következő példa csak egy CSV-fájlból ad vissza egy második oszlopot:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Megadja a használandó mezőterminátort. Az alapértelmezett mezőterminátor vessző ("**, ").**

ROWTERMINATOR ='row_terminator''

Megadja a használandó sorterminátort. Az alapértelmezett sorterminátor egy új sorkarakter, például \r\n.

ESCAPE_CHAR = "char"

Megadja a fájlban azt a karaktert, amely a fájl kikerülésére szolgál, és a fájl összes határolóértéke. Ha az escape karaktert nem önmaga, vagy a határolóértékek bármelyike követi, az érték olvasásakor az escape karakter ellesz dobva. 

A ESCAPE_CHAR paraméter attól függetlenül alkalmazásra kerül, hogy a FIELDQUOTE engedélyezve van-e vagy sem. Nem fogják arra használni, hogy elmeneküljön az idéző karakter elől. Az idéző karakter az Excel CSV viselkedésével összhangban dupla idézőjelekkel kerül ki.

FIRSTROW = 'first_row' 

Megadja az első betöltandó sor számát. Az alapértelmezett érték 1. Ez a megadott adatfájl első sorát jelzi. A sorszámokat a sorterminátorok számlálásával határozzák meg. A FIRSTROW 1-alapú.

FIELDQUOTE = 'field_quote' 

Megadja azt a karaktert, amelyet a rendszer a CSV-fájlban idézőjelként fog használni. Ha nincs megadva, a (") ajánlati karakter lesz használva. 

## <a name="examples"></a>Példák

A következő példa csak két oszlopot ad vissza, amelyek sorszáma 1 és 4 a population*.csv fájlokból. Mivel a fájlokban nincs fejlécsor, az első sorból indul el:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```



A következő példa a Parquet formátumban megadott népszámlálási adatkészlet első sorának összes oszlopát adja vissza oszlopnevek és adattípusok megadása nélkül: 

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>További lépések

További mintákért lépjen [rövid útmutatókra,](query-data-storage.md) vagy mentse a lekérdezés eredményeit az Azure Storage-ba a [CETAS](develop-tables-cetas.md)használatával.
