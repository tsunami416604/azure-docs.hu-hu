---
title: A OPENROWSET használata igény szerinti SQL-ben (előzetes verzió)
description: Ez a cikk az SQL on-demand (előzetes verzió) OPENROWSET szintaxisát ismerteti, és ismerteti az argumentumok használatát.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: e541a5620d4f263e5e1379b364d7c7dd9a97a331
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289021"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Igény szerinti SQL-OPENROWSET használata (előzetes verzió)

A `OPENROWSET(BULK...)` függvény lehetővé teszi a fájlok elérését az Azure Storage-ban. `OPENROWSET` a függvény egy távoli adatforrás tartalmát olvassa be (például fájl), és a tartalmat sorok halmaza adja vissza. Az SQL on-demand (előzetes verzió) erőforráson belül a OPENROWSET tömeges sorhalmaz szolgáltatója a OPENROWSET függvény meghívásával és a TÖMEGES beállítás megadásával érhető el.  

A `OPENROWSET` függvény hivatkozhat a `FROM` lekérdezés záradékára úgy, mintha a tábla neve lenne `OPENROWSET` . Olyan beépített TÖMEGES szolgáltatón keresztül támogatja a tömeges műveleteket, amely lehetővé teszi, hogy egy fájlból származó adatok beolvassák és visszaadjanak a sorhalmazban.

## <a name="data-source"></a>Adatforrás

A OPENROWSET függvény a szinapszis SQL-ben beolvassa a fájl (ok) tartalmát egy adatforrásból. Az adatforrás egy Azure Storage-fiók, amely explicit módon hivatkozható a `OPENROWSET` függvényben, vagy dinamikusan következtetni lehet az olvasni kívánt fájlok URL-címére.
A `OPENROWSET` függvény opcionálisan tartalmazhat egy `DATA_SOURCE` paramétert a fájlokat tartalmazó adatforrás megadásához.
- `OPENROWSET` a nem használható a `DATA_SOURCE` fájlok tartalmának közvetlen olvasására a megadott URL-címről a következő `BULK` beállítással:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Ezzel a módszerrel gyorsan és egyszerűen elolvashatja a fájlok tartalmát előzetes konfigurálás nélkül. Ez a beállítás lehetővé teszi, hogy az alapszintű hitelesítési lehetőség használatával hozzáférjen a tárolóhoz (Azure ad áteresztő Azure AD-bejelentkezésekhez és SAS-jogkivonat SQL-bejelentkezésekhez). 

- `OPENROWSET` a `DATA_SOURCE` használatával a megadott Storage-fiókban található fájlok érhetők el:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Ezzel a beállítással konfigurálhatja a Storage-fiók helyét az adatforrásban, és megadhatja a tároló eléréséhez használandó hitelesítési módszert. 
    
    > [!IMPORTANT]
    > `OPENROWSET``DATA_SOURCE`a nem biztosít gyors és egyszerű módszert a tárolási fájlok eléréséhez, de korlátozott hitelesítési lehetőségeket kínál. Az Azure AD-rendszerbiztonsági tag például csak az [Azure ad-identitás](develop-storage-files-storage-access-control.md?tabs=user-identity) vagy nyilvánosan elérhető fájlok használatával férhet hozzá a fájlokhoz. Ha nagyobb teljesítményű hitelesítési beállításokra van szüksége, használja `DATA_SOURCE` a kapcsolót, és adja meg a tárhely eléréséhez használni kívánt hitelesítő adatokat.


## <a name="security"></a>Biztonság

Egy adatbázis-felhasználónak engedéllyel kell rendelkeznie `ADMINISTER BULK OPERATIONS` a `OPENROWSET` függvény használatához.

A tároló rendszergazdájának engedélyeznie kell a felhasználók számára, hogy érvényes SAS-jogkivonatot biztosítanak, vagy engedélyezni kell az Azure AD-rendszerbiztonsági tag számára a tárolási fájlok elérését. További információ a tárterület-hozzáférés-vezérlésről [ebben a cikkben](develop-storage-files-storage-access-control.md).

`OPENROWSET` a következő szabályok segítségével határozhatja meg, hogyan hitelesíthető a tárolóban:
- A `OPENROWSET` `DATA_SOURCE` hitelesítési mechanizmus nélkül a hívó típusától függ.
  - Bármely felhasználó használhatja `OPENROWSET` anélkül `DATA_SOURCE` , hogy nyilvánosan elérhető fájlokat kellene beolvasnia az Azure Storage-ban.
  - Az Azure AD-bejelentkezések saját [Azure ad-identitással](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) férhetnek hozzá a védett fájlokhoz, ha az Azure Storage lehetővé teszi, hogy az Azure ad-felhasználó hozzáférjen a mögöttes fájlokhoz (például ha a hívó rendelkezik `Storage Reader` engedéllyel az Azure Storage szolgáltatásban).
  - Az SQL-bejelentkezések `OPENROWSET` `DATA_SOURCE` a nyilvánosan elérhető fájlok, az SAS-token használatával védett fájlok, illetve a szinapszis munkaterület felügyelt identitása nélkül is használhatók. [Létre kell hoznia egy kiszolgáló-hatókörű hitelesítő adatot](develop-storage-files-storage-access-control.md#examples) a tárolási fájlok elérésének engedélyezéséhez. 
- A `OPENROWSET` és a `DATA_SOURCE` hitelesítési mechanizmus a hivatkozott adatforráshoz rendelt adatbázis-hatókörű hitelesítő adatokban van definiálva. Ez a beállítás lehetővé teszi a nyilvánosan elérhető tárolók elérését, vagy az SAS-token, a munkaterület felügyelt identitása vagy [a hívó Azure ad-identitása](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (ha a hívó az Azure ad rendszerbiztonsági tag) használatával fér hozzá a tárolóhoz. Ha `DATA_SOURCE` olyan Azure Storage-ra hivatkozik, amely nem nyilvános, [adatbázis-hatókörű hitelesítő adatokat kell létrehoznia](develop-storage-files-storage-access-control.md#examples) , és hivatkozni kell rá a `DATA SOURCE` tároló-fájlok elérésének engedélyezéséhez.

A hívónak engedéllyel kell rendelkeznie `REFERENCES` a hitelesítő adatok tárolásához való használatához.

## <a name="syntax"></a>Szintaxis

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
```

## <a name="arguments"></a>Argumentumok

A lekérdezéshez célként megadott adatokat tartalmazó bemeneti fájlok esetében két lehetőség közül választhat. Az érvényes értékek a következők:

- "CSV" – minden olyan tagolt szövegfájlt tartalmaz, amely sorok/oszlopok elválasztókkal rendelkezik. Bármely karakter használható mező elválasztóként, például TSV: FIELDTERMINATOR = Tab.

- "PARQUEt" – bináris fájl parketta formátumban 

**"unstructured_data_path"**

Az adatelérési utat kiépítő unstructured_data_path abszolút vagy relatív elérési út lehet:
- A "://" formátumú abszolút elérési út \<prefix> \<storage_account_path> / \<storage_path> lehetővé teszi a felhasználók számára, hogy közvetlenül beolvassák a fájlokat.
- Relatív elérési út a (z) "<storage_path>" formátumban, amelyet a paraméterrel kell használni, `DATA_SOURCE` és a <storage_account_path> helyen definiált fájl mintáját ismerteti `EXTERNAL DATA SOURCE` . 

 Az alábbiakban megtalálhatja a megfelelő <storage account path> értékeket, amelyek az adott külső adatforráshoz kapcsolódnak. 

| Külső adatforrás       | Előtag | Storage-fiók elérési útja                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http [s]  | \<storage_account>. blob.core.windows.net/path/file   |
| Azure Blob Storage         | wasb [s]  | \<container>@\<storage_account>. blob.core.windows.net/path/file |
| Azure Data Lake Storage Gen1 | http [s]  | \<storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | http [s]  | \<storage_account>. dfs.core.windows.net/Path/file   |
| Azure Data Lake Storage Gen2 | aufs [s]  | [\<file_system>@\<account_name>. dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

 Megadja a tárhelyen belüli útvonalat, amely az olvasni kívánt mappára vagy fájlra mutat. Ha az elérési út egy tárolóra vagy mappára mutat, a rendszer az adott tárolóból vagy mappából olvassa be az összes fájlt. Az almappákban található fájlok nem lesznek belefoglalva. 

 A helyettesítő karakterek használatával több fájlt vagy mappát is megcélozhat. Több nem egymást követő helyettesítő karakter használata engedélyezett.
Az alábbi példa az összes olyan *CSV* -fájlt beolvassa, amely a */CSV/Population*kezdődő összes mappából származó *populációval* kezdődik:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Ha a unstructured_data_path mappát adja meg, az SQL igény szerinti lekérdezése a mappában lévő fájlokat fogja lekérni. 

> [!NOTE]
> A Hadoop és a Base függvénytől eltérően az SQL on-demand nem ad vissza almappákat. Emellett a Hadoop és a Base függvénytől eltérően az SQL igény szerint visszaadja azokat a fájlokat, amelyekhez a fájlnév aláhúzással (_) vagy ponttal (.) kezdődik.

Ha az alábbi példában a unstructured_data_path =, az `https://mystorageaccount.dfs.core.windows.net/webdata/` SQL igény szerinti lekérdezése mydata.txt és _hidden.txt sorait fogja visszaadni. Nem ad vissza mydata2.txt és mydata3.txt, mert egy almappában találhatók.

![Rekurzív adatértékek külső táblákhoz](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

A WITH záradék segítségével megadhatja a fájlokból beolvasni kívánt oszlopokat.

- CSV-adatfájlok esetén az összes oszlop olvasásához adja meg az oszlopnevek és az adattípusok nevét. Ha az oszlopok egy részhalmazát szeretné használni, a sorszámok használatával válassza ki az oszlopokat a származó adatfájlokból a sorszám alapján. Az oszlopokat a sorszám megjelölése fogja kötni. 

    > [!IMPORTANT]
    > A WITH záradék kötelező a CSV-fájlokhoz.
    >
    
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

Meghatározza a használandó sort. Ha a lezáró sor nincs megadva, a rendszer az alapértelmezett megszakítások egyikét fogja használni. PARSER_VERSION = "1,0" alapértelmezett lezárói a következők: \r\n, \n és \r. A PARSER_VERSION = "2,0" alapértelmezett lezárói a következők: \r\n és \n.

ESCAPE_CHAR = "char"

Meghatározza a fájlban található karaktert, amely a fájl összes elválasztó értékének kiszökésére szolgál. Ha az Escape-karaktert a saját maga vagy az elválasztó értékek egyike követi, az escape-karakter eldobása az érték beolvasása közben történik. 

A ESCAPE_CHAR paraméter attól függetlenül lesz alkalmazva, hogy a FIELDQUOTE vagy nincs-e engedélyezve. A rendszer nem használja fel az idézett karakter megmenekülésére. Az idézőjel karakternek egy másik idézőjel karakterrel kell megszöknie. Az idézőjel karakter csak akkor szerepelhet az oszlop értékén belül, ha az érték idézőjelekkel van ellátva.

FIRSTROW = ' first_row ' 

Meghatározza a betöltendő első sor számát. Az alapértelmezett érték 1, és jelzi a megadott adatfájl első sorát. A sorok száma a sorok lezáróinak számlálásával határozható meg. A FIRSTROW 1-alapú.

FIELDQUOTE = ' field_quote ' 

Megadja azt a karaktert, amely a CSV-fájlban idézőjelként lesz használva. Ha nincs megadva, a rendszer az idézőjel karaktert (") fogja használni. 

DATA_COMPRESSION = "data_compression_method"

Meghatározza a tömörítési módszert. A következő tömörítési módszer támogatott:

- org. Apache. Hadoop. IO. compress. GzipCodec

PARSER_VERSION = "parser_version"

A fájlok olvasásakor használandó elemző verzió megadása. Jelenleg támogatott CSV-elemző verziója 1,0 és 2,0:

- PARSER_VERSION = "1,0"
- PARSER_VERSION = "2,0"

A CSV-elemző 1,0-es verziója alapértelmezett, és gazdag funkció. Az 2,0-es verzió a teljesítményre van felépítve, és nem támogatja az összes beállítást és kódolást. 

CSV-elemző 2,0-es verziójának sajátosságai:

- Az adattípusok nem támogatottak.
- A sorok maximális méretének korlátja 8 MB.
- A következő beállítások nem támogatottak: DATA_COMPRESSION.
- Az idézőjelek közé tartozó üres karakterlánc ("") üres sztringként van értelmezve.

## <a name="examples"></a>Példák

A következő példa csak két olyan oszlopot ad vissza, amelyekben az 1. és 4. sorszám szerepel a Population*. csv fájlokban. Mivel a fájlokban nem szerepel fejlécsor, a rendszer az első sor olvasását indítja el:

```sql
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

A következő példa visszaadja az első sor összes oszlopát a számbavételi adatkészletből, a parketta formátuma, valamint az oszlopnevek és az adattípusok megadása nélkül: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

## <a name="next-steps"></a>Következő lépések

További példákat a [lekérdezési adattárolási](query-data-storage.md) útmutatóban talál, amelyből megtudhatja, hogyan használható a `OPENROWSET` [CSV](query-single-csv-file.md)-, a [parketta](query-parquet-files.md)-és a [JSON](query-json-files.md) -fájlformátumok olvasásához. Azt is megtudhatja, hogyan mentheti a lekérdezés eredményeit az Azure Storage-ba a [CETAS](develop-tables-cetas.md)használatával.
