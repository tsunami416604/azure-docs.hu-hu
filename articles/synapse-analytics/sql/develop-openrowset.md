---
title: A OPENROWSET használata igény szerinti SQL-ben (előzetes verzió)
description: Ez a cikk az SQL on-demand (előzetes verzió) OPENROWSET szintaxisát ismerteti, és ismerteti az argumentumok használatát.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81680489"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Igény szerinti SQL-OPENROWSET használata (előzetes verzió)

A OPENROWSET (BULK...) függvény lehetővé teszi a fájlok elérését az Azure Storage-ban. Az SQL on-demand (előzetes verzió) erőforráson belül a OPENROWSET tömeges sorhalmaz szolgáltatója a OPENROWSET függvény meghívásával és a TÖMEGES beállítás megadásával érhető el.  

A OPENROWSET függvényt a lekérdezés FROM záradékában lehet hivatkozni, mintha a tábla neve OPENROWSET. Olyan beépített TÖMEGES szolgáltatón keresztül támogatja a tömeges műveleteket, amely lehetővé teszi, hogy egy fájlból származó adatok beolvassák és visszaadjanak a sorhalmazban.

A OPENROWSET jelenleg nem támogatott az SQL-készletben.

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

A lekérdezéshez célként megadott adatokat tartalmazó bemeneti fájlok esetében két lehetőség közül választhat. Az érvényes értékek a következők:

- "CSV" – minden olyan tagolt szövegfájlt tartalmaz, amely sorok/oszlopok elválasztókkal rendelkezik. Bármely karakter használható mező elválasztóként, például TSV: FIELDTERMINATOR = Tab.

- "PARQUEt" – bináris fájl parketta formátumban 

**"unstructured_data_path"**

Az adatelérési utat kiépítő unstructured_data_path a következőképpen szerveződik:  
"\<előtag>://\<storage_account_path>/\<storage_path>"
 
 
 Az alábbiakban megtalálhatja a megfelelő tárolási fiók elérési útját, amely az adott külső adatforráshoz fog hivatkozni. 

| Külső adatforrás       | Előtag | Storage-fiók elérési útja                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | https  | \<storage_account>. blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | \<storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | \<storage_account>. dfs.core.windows.net              |
||||

"\<storage_path>"

 Megadja a tárhelyen belüli útvonalat, amely az olvasni kívánt mappára vagy fájlra mutat. Ha az elérési út egy tárolóra vagy mappára mutat, a rendszer az adott tárolóból vagy mappából olvassa be az összes fájlt. Az almappákban található fájlok nem lesznek belefoglalva. 
 
 A helyettesítő karakterek használatával több fájlt vagy mappát is megcélozhat. Több nem egymást követő helyettesítő karakter használata engedélyezett.
Az alábbi példa az összes olyan *CSV* -fájlt beolvassa, amely a */CSV/Population*kezdődő összes mappából származó *populációval* kezdődik:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Ha a unstructured_data_path mappát adja meg, az SQL igény szerinti lekérdezése a mappában lévő fájlokat fogja lekérni. 

> [!NOTE]
> A Hadoop és a Base függvénytől eltérően az SQL on-demand nem ad vissza almappákat. Emellett a Hadoop és a PloyBase függvénytől eltérően az SQL igény szerint olyan fájlokat ad vissza, amelyekhez a fájlnév aláhúzással (_) vagy ponttal (.) kezdődik.

Az alábbi példában, ha a unstructured_data_path =`https://mystorageaccount.dfs.core.windows.net/webdata/`, egy SQL igény szerinti lekérdezés a SajátAdatok. txt és a _Hidden. txt fájlból származó sorokat ad vissza. A mydata2. txt és a mydata3. txt fájlt nem fogja visszaadni, mert egy almappában találhatók.

![Rekurzív adatértékek külső táblákhoz](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

A WITH záradék segítségével megadhatja a fájlokból beolvasni kívánt oszlopokat.

- CSV-adatfájlok esetén az összes oszlop olvasásához adja meg az oszlopnevek és az adattípusok nevét. Ha az oszlopok egy részhalmazát szeretné használni, a sorszámok használatával válassza ki az oszlopokat a származó adatfájlokból a sorszám alapján. Az oszlopokat a sorszám megjelölése fogja kötni. 

> [!IMPORTANT]
> A WITH záradék kötelező a CSV-fájlokhoz.
- A parketta-adatfájlok esetében adja meg az oszlopok neveit, amelyek megfelelnek a kezdeményező adatfájlokban lévő oszlopnevek. Az oszlopok név szerint lesznek kötve. Ha a WITH záradék ki van hagyva, a rendszer a Parquet-fájlokból származó összes oszlopot visszaadja.

column_name = a kimeneti oszlop neve. Ha meg van adni, ez a név felülbírálja a forrásfájl oszlopának nevét.

column_type = a kimeneti oszlop adattípusa. Az implicit adattípus-konverziót itt fogja megtenni.

column_ordinal = a forrásfájl (ok) oszlopának sorszáma. Ezt az argumentumot a rendszer figyelmen kívül hagyja a Parquet-fájlokban, mivel a kötés név alapján történik. A következő példa csak egy CSV-fájlból származó második oszlopot ad vissza:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR = ' field_terminator '

Meghatározza a használni kívánt lezáró mezőt. Az alapértelmezett lezáró mező egy vessző ("**,**").

ROWTERMINATOR = ' row_terminator ' '

Meghatározza a használandó sort. Az alapértelmezett sor Terminator egy sortörési karakter, például \r\n.

ESCAPE_CHAR = "char"

Meghatározza a fájlban található karaktert, amely a fájl összes elválasztó értékének kiszökésére szolgál. Ha az Escape-karaktert a saját maga vagy az elválasztó értékek egyike követi, az escape-karakter eldobása az érték beolvasása közben történik. 

A ESCAPE_CHAR paraméter attól függetlenül lesz alkalmazva, hogy a FIELDQUOTE vagy nincs-e engedélyezve. A rendszer nem használja fel az idézett karakter megmenekülésére. Az idézőjeles karaktert dupla idézőjelek között kell megszökni az Excel CSV-viselkedéssel való igazításhoz.

FIRSTROW = ' first_row ' 

Meghatározza a betöltendő első sor számát. Az alapértelmezett érték 1. Ez jelzi a megadott adatfájl első sorát. A sorok száma a sorok lezáróinak számlálásával határozható meg. A FIRSTROW 1-alapú.

FIELDQUOTE = ' field_quote ' 

Megadja azt a karaktert, amely a CSV-fájlban idézőjelként lesz használva. Ha nincs megadva, a rendszer az idézőjel karaktert (") fogja használni. 

## <a name="examples"></a>Példák

A következő példa csak két olyan oszlopot ad vissza, amelyekben az 1. és 4. sorszám szerepel a Population*. csv fájlokban. Mivel a fájlokban nem szerepel fejlécsor, a rendszer az első sor olvasását indítja el:

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



A következő példa az első sor összes oszlopát adja vissza a (z) és a (z)-ben található népszámlálás-adatkészletből, az oszlopnevek és az adattípusok megadása nélkül: 

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

További példákért lépjen a gyors útmutatóhoz [, vagy mentse](query-data-storage.md) a lekérdezés eredményét az Azure Storage-ba a [CETAS](develop-tables-cetas.md)használatával.
