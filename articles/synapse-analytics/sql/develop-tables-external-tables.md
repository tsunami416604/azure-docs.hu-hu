---
title: Külső táblák használata a Synapse SQL-rel
description: Adatfájlok olvasása vagy írása a Synapse SQL segítségével
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4d13d15fe950c89687acfca355d4ed183756536a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423977"
---
# <a name="use-external-tables-with-synapse-sql"></a>Külső táblák használata a Synapse SQL-rel

Egy külső tábla a Hadoopban, az Azure Storage blobban vagy az Azure Data Lake Storage-ban található adatokra mutat. A külső táblák adatok olvasására szolgálnak a fájlokból, vagy adatokat írnak az Azure Storage-fájlokba. A Synapse SQL használatával külső táblák kal olvashat adatokat SQL-készletbe vagy SQL-igény szerint (előzetes verzió) .

## <a name="external-tables-in-sql-pool"></a>Külső táblák az SQL-készletben

Az SQL-készletben külső tábla segítségével:

- Az Azure Blob Storage és az Azure Data Lake Gen2 lekérdezése Transact-SQL utasításokkal.
- Adatok importálása és tárolása az Azure Blob Storage-ból és az Azure Data Lake Storage-ból az SQL-készletbe.

Ha a CREATE [TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) utasítással együtt használja, a külső tábla kiválasztásával adatokat importál az SQL-készlet en belüli táblába. A COPY [utasításon](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)kívül a külső táblák hasznosak az adatok betöltéséhez. A betöltési oktatóanyagról a [PolyBase használata adatok betöltése az Azure Blob Storage szolgáltatásból.](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="external-tables-in-sql-on-demand-preview"></a>Külső táblák az SQL igény szerinti verziójában (előzetes verzió)

Az igény szerinti SQL esetén külső táblát kell használnia a következőkre:

- Adatok lekérdezése az Azure Blob Storage-ban vagy az Azure Data Lake Storage-ban Transact-SQL utasításokkal
- Az SQL igény szerinti lekérdezési eredményeit tárolja az Azure Blob Storage vagy az Azure Data Lake Storage fájljaiban a [CETAS](develop-tables-cetas.md)használatával.

Külső táblákat az alábbi lépésekkel hozhat létre az IGÉNY szerinti SQL használatával:

1. KÜLSŐ ADATFORRÁS LÉTREHOZÁSA
2. CREATE EXTERNAL FILE FORMAT
3. KÜLSŐ TÁBLA LÉTREHOZÁSA

## <a name="create-external-data-source"></a>KÜLSŐ ADATFORRÁS LÉTREHOZÁSA

A külső adatforrások a tárfiókokhoz való csatlakozáshoz használatosak. A teljes dokumentáció [itt](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)található .

## <a name="syntax-for-create-external-data-source"></a>Külső adatforrás létrehozásának szintaxisa

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>' )
[;]
```

## <a name="arguments-for-create-external-data-source"></a>Külső adatforrás létrehozása argumentumai

data_source_name -Az adatforrás felhasználó által definiált nevét adja meg. A névnek egyedinek kell lennie az adatbázisban.

LOCATION `'<prefix>://<path>'` = - Biztosítja a kapcsolódási protokollt és a külső adatforrás elérési útját. Az elérési út tartalmazhat egy `'<prefix>://<path>/container'`tárolót . és egy `'<prefix>://<path>/container/folder'`mappát .

| Külső adatforrás        | Helyelőtag | Hely elérési útja                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Data Lake Áruház Gen 1 | `adl`           | `<storage_account>.azuredatalake.net`                 |
| Azure Data Lake Áruház Gen 2 | `abfs[s]`       | `<container>@<storage_account>.dfs.core.windows.net`  |

## <a name="example-for-create-external-data-source"></a>Példa külső adatforrás létrehozása

A következő példa létrehoz egy külső adatforrást az Azure Data Lake Gen2 számára, amely a New York-i adatkészletre mutat:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://newyorktaxidataset.azuredatalakestore.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Létrehoz egy külső fájlformátum-objektumot, amely meghatározza az Azure Blob Storage vagy az Azure Data Lake Storage által tárolt külső adatokat. Külső fájlformátum létrehozása a külső tábla létrehozásának előfeltétele. A teljes dokumentáció [itt](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)van .

Külső fájlformátum létrehozásával megadhatja a külső táblázat által hivatkozott adatok tényleges elrendezését.

## <a name="syntax-for-create-external-file-format"></a>Külső fájlformátum létrehozásának szintaxisa

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
}
```

## <a name="arguments-for-create-external-file-format"></a>Külső fájlformátum létrehozása argumentumai

file_format_name – A külső fájlformátum nevét adja meg.

FORMAT_TYPE = [ PARQUET | DETAGZONOS SZÖVEG]- A külső adatok formátumát adja meg.

- PAROKT - Parketta formátumot ad meg.
- DELIMITEDTEXT - Oszlophatárolókkal ellátott szövegformátumot, más néven mezőterminátorokat ad meg.

FIELD_TERMINATOR = *field_terminator* - Csak tagolt szövegfájlokra vonatkozik. A mezőterminátor egy vagy több olyan karaktert határoz meg, amely a szöveggel tagolt fájlban minden mező (oszlop) végét jelöli. Az alapértelmezett érték a csőkarakter (:|).The default is the pipe character (i.)

Példák:

- FIELD_TERMINATOR = '|'
- FIELD_TERMINATOR = '
- FIELD_TERMINATOR =

STRING_DELIMITER = *string_delimiter* - A szövegtagolt fájlban karakterlánc típusú adatok mezőterminátorát adja meg. A karakterlánc-határoló jel egy vagy több karakter hosszú, és idézőjelek közé van zárva. Az alapértelmezett érték az üres karakterlánc ("").

Példák:

- STRING_DELIMITER = """
- STRING_DELIMITER = '*'
- STRING_DELIMITER =

FIRST_ROW = *First_row_int* - Megadja az elsőként beolvasott és az összes fájlra érvényes sorszámot. Ha az értéket kettőre állítja, az adatok betöltésekor minden fájl (fejlécsor) első sora kimarad. A sorok at a sorterminátorok (/r/n, /r, /n) megléte alapján hagyja ki a program.

USE_TYPE_DEFAULT = { IGAZ | **HAMIS** } - Megadja, hogyan kell kezelni a hiányzó értékeket a tagolt szövegfájlokban, amikor adatokat szeretne betartani a szövegfájlból.

IGAZ – Ha adatokat kér be a szövegfájlból, tárolja a hiányzó értékeket a külső tábladefiníció megfelelő oszlopához tartozó alapértelmezett érték adattípusával. Cserélje le például a hiányzó értéket a következőkre:

- 0, ha az oszlop numerikus oszlopként van definiálva. A decimális oszlopok nem támogatottak, és hibát okozhatnak.
- Üres karakterlánc (""), ha az oszlop karakterláncoszlop.
- 1900-01-01, ha az oszlop egy dátumoszlop.

HAMIS - Az összes hiányzó értéket NULL értékként tárolja. A tagolt szövegfájlban a NULL szó használatával tárolt NULL értékeket a program "NULL" karakterláncként importálja.

Kódolás = {'UTF8' | 'UTF16'} – Az SQL igény szerinti igény szerinti kódolású UTF8 és UTF16 kódolású tagozott szövegfájlokat tud olvasni.

DATA_COMPRESSION = *data_compression_method* - Ez az argumentum a külső adatok adattömörítési módját adja meg. Amikor külső táblákból olvas, a rendszer figyelmen kívül hagyja. Csak akkor használható, ha külső táblákra ír a [CETAS](develop-tables-cetas.md)használatával.

A PARQUET fájlformátum-típus a következő tömörítési módszereket támogatja:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

## <a name="example-for-create-external-file-format"></a>Példa külső fájlformátum létrehozására

A következő példa külső fájlformátumot hoz létre a népszámlálási fájlokhoz:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>KÜLSŐ TÁBLA LÉTREHOZÁSA

A CREATE EXTERNAL TABLE parancs létrehoz egy külső táblát a Synapse SQL számára az Azure Blob Storage vagy az Azure Data Lake Storage tárolja tárolt adatok eléréséhez. 

## <a name="syntax-for-create-external-table"></a>Külső tábla létrehozása szintaxisa

```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

## <a name="arguments-create-external-table"></a>Argumentumok LÉTREHOZÁSA KÜLSŐ TÁBLA

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

A létrehozandó tábla egy-három részből álló neve. Külső tábla esetén az SQL igény szerinti csak a tábla metaadatait tárolja. A tényleges adatok at sql on-demand nem kerülnek áthelyezésre vagy tárolásra.

<column_definition>, ... *n* ]

A CREATE EXTERNAL TABLE támogatja az oszlopnév, adattípus, nullability és rendezés konfigurálását. A DEFAULT CONSTRAINT nem használható külső táblákon.

>[!IMPORTANT]
>Az oszlopdefinícióknak – az adattípusokat és az oszlopok számát is beleértve – meg kell felelniük a külső fájlok adatainak. Ha ezek között eltérés található, a tényleges adatok lekérdezésekor a fájlsorok vissza lesznek utasítva.

A Parkettafájlokból történő olvasáskor csak az elolvasni kívánt oszlopokat adhatja meg, a többit pedig kihagyhatja.

HELY = '*folder_or_filepath*'

Megadja a mappát vagy a fájl elérési útját és a fájl nevét az Azure Blob Storage tényleges adataihoz. A hely a gyökérmappából indul. A gyökérmappa a külső adatforrásban megadott adathely.

Ha mappa LOCATION mappát ad meg, az SQL igény szerinti lekérdezése kiválasztja a külső táblából, és fájlokat olvas le a mappából.

> [!NOTE]
> A Hadooptól és a PolyBase-től eltérően az igény szerinti SQL nem ad vissza almappákat. Olyan fájlokat ad vissza, amelyek nél a fájlnév aláhúzással (_) vagy pontmal (.) kezdődik.

Ebben a példában, ha location='/webdata/', sql on-demand lekérdezés, visszaadja a sorokat a mydata.txt és _hidden.txt. Nem ad vissza mydata2.txt és mydata3.txt, mert egy almappában találhatók.

![Külső táblák rekurzív adatai](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* - Megadja a külső adatok helyét tartalmazó külső adatforrás nevét. Külső adatforrás létrehozásához használja [a KÜLSŐ ADATFORRÁS LÉTREHOZÁSA parancsot.](#create-external-data-source)

FILE_FORMAT = *external_file_format_name* - Megadja annak a külső fájlformátumú objektumnak a nevét, amely a külső adatok fájltípusát és tömörítési módját tárolja. Külső fájlformátum létrehozásához használja [a CREATE EXTERNAL FILE FORMAT parancsot.](#create-external-file-format)

## <a name="permissions-create-external-table"></a>Engedélyek létrehozása külső tábla létrehozása

A külső táblából való kiválasztáshoz megfelelő hitelesítő adatokra van szükség lista- és olvasási engedélyekkel.

## <a name="example-create-external-table"></a>Példa Külső tábla létrehozása

A következő példa létrehoz egy külső táblát. Az első sort adja vissza:

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Külső táblák létrehozása és lekérdezése egy fájlból az Azure Data Lake-ben

A Data Lake feltárási képességeinek használatával most már létrehozhat és lekérdezhet egy külső táblát az SQL-készlet vagy az SQL on-demand használatával, a fájlra való egyszerű jobb gombbal való kattintással.

## <a name="prerequisites"></a>Előfeltételek

- Legalább a Storage Blob Data Contributor ARM Access szerepkörrel rendelkező munkaterülethez kell hozzáférnie az ADLS Gen2-fiókhoz.

- Legalább engedéllyel kell rendelkeznie külső táblák [létrehozásához](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2) és lekérdezéséhez az SQL-készletben vagy az SQL OD-on

- Az ADLS Gen2-fiókhoz társított csatolt szolgáltatásnak **hozzáféréssel kell rendelkeznie a fájlhoz.** Ha például a csatolt szolgáltatáshitelesítési mechanizmus felügyelt identitás, a munkaterület felügyelt identitásának legalább storage blob-olvasó engedéllyel kell rendelkeznie a tárfiókhoz.

Az Adatok panelen jelölje ki azt a fájlt, amelyből létre szeretné hozni a külső táblát:
> [!div class="mx-imgBorder"]
>![külső1](./media/develop-tables-external-tables/external-table-1.png)

Megnyílik egy párbeszédablak. Válassza ki az SQL-készletet vagy az IGÉNY szerinti SQL-készletet, adjon nevet a táblának, és válassza a megnyitott parancsfájlt:

> [!div class="mx-imgBorder"]
>![külsőtáblázat2](./media/develop-tables-external-tables/external-table-2.png)

Az SQL Script automatikusan generálódik, és a sémát a fájlból hozza létre:
> [!div class="mx-imgBorder"]
>![külsőtáblázat3](./media/develop-tables-external-tables/external-table-3.png)

Futtassa a szkriptet. A parancsfájl automatikusan futtatja a Select Top 100 *.:
> [!div class="mx-imgBorder"]
>![külső4](./media/develop-tables-external-tables/external-table-4.png)

A külső tábla most jön létre, a külső tábla tartalmának jövőbeli feltárásához a felhasználó közvetlenül az Adatok ablaktábláról kérdezheti le:
> [!div class="mx-imgBorder"]
>![külső5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>További lépések

Tekintse meg a [CETAS](develop-tables-cetas.md) cikket, hogyan mentheti a lekérdezés eredményeit egy külső táblába az Azure Storage-ban. Vagy megkezdheti a [Spark-táblák](develop-storage-files-spark-tables.md)lekérdezését.
