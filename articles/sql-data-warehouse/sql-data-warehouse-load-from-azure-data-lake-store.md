---
title: Adatok betöltése a Azure Data Lake Storageból
description: Az adatok Azure Data Lake Storageból a Azure SQL Data Warehouseba való betöltéséhez használjon albase külső táblákat.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 12/06/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fdbf0eb849549071b4cbbb961c9e9f71fce1faf8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923628"
---
# <a name="load-data-from-azure-data-lake-storage-to-sql-data-warehouse"></a>Adatok betöltése a Azure Data Lake Storageból a SQL Data Warehouseba
Ez az útmutató azt ismerteti, hogyan használhatók a külső táblák az adatok Azure Data Lake Storageból Azure SQL Data Warehouseba való betöltéséhez. Bár a Data Lake Storage tárolt adatain is futtathatók az ad hoc lekérdezések, javasoljuk, hogy az adatSQL Data Warehouse a legjobb teljesítmény érdekében importálja az adatkészletbe. 

> [!NOTE]  
> A betöltés alternatívájaként a [másolási utasítás](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) jelenleg nyilvános előzetes verzióban érhető el. Ha visszajelzést szeretne küldeni a COPY utasításról, küldjön egy e-mailt a következő terjesztési listára: sqldwcopypreview@service.microsoft.com.
>
> [!div class="checklist"]

> * Hozza létre a Data Lake Storage betöltéséhez szükséges adatbázis-objektumokat.
> * Kapcsolódjon egy Data Lake Storage könyvtárhoz.
> * Betöltés az Azure SQL Data Warehouseba.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="before-you-begin"></a>Előzetes teendők
Az oktatóanyag megkezdése előtt töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) legújabb verzióját.

Az oktatóanyag futtatásához a következőkre lesz szüksége:

* Egy Azure SQL Data Warehouse. Lásd: [Létrehozás és lekérdezés és Azure SQL Data Warehouse](create-data-warehouse-portal.md).
* Egy Data Lake Storage-fiók. Lásd: [Azure Data Lake Storage első lépései](../data-lake-store/data-lake-store-get-started-portal.md). Ehhez a Storage-fiókhoz konfigurálnia kell vagy meg kell adnia a következő hitelesítő adatok egyikét a betöltéshez: A Storage-fiók kulcsa, az Azure Directory alkalmazás felhasználója vagy egy HRE-felhasználó, amely a megfelelő RBAC szerepkörrel rendelkezik a Storage-fiókhoz. 

##  <a name="create-a-credential"></a>Hitelesítő adat létrehozása
Kihagyhatja ezt a szakaszt, és folytathatja a "külső adatforrás létrehozása" lehetőséget, ha a hitelesítés során HRE továbbít. Az adatbázis-hatókörrel rendelkező hitelesítő adatok nem szükségesek ahhoz, hogy a rendszer létrehozza vagy megadja a HRE-továbbítást, de győződjön meg arról, hogy a HRE-felhasználó rendelkezik a megfelelő RBAC-szerepkörrel (Storage blob Adatolvasó, közreműködő vagy tulajdonos szerepkör) a Storage-fiókhoz. További részleteket [itt talál](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260). 

A Data Lake Storage-fiók eléréséhez létre kell hoznia egy adatbázis-főkulcsot a hitelesítő adatok titkos kódjának titkosításához. Ezután létrehoz egy adatbázis-hatókörű hitelesítő adatot a titkos kulcs tárolásához. Az egyszerű szolgáltatásnév (Azure Directory alkalmazás-felhasználó) használatával végzett hitelesítés során az adatbázis-hatókörrel rendelkező hitelesítő adatok a HRE-ben beállított egyszerű szolgáltatás hitelesítő adatait tárolják. Az adatbázis-hatókörön belüli hitelesítő adatokat is használhatja a Gen2 tartozó Storage-fiók kulcsának tárolására.

Ha az egyszerű szolgáltatásokkal szeretne csatlakozni Data Lake Storagehoz, **először** létre kell hoznia egy Azure Active Directory alkalmazást, létre kell hoznia egy hozzáférési kulcsot, és biztosítania kell az alkalmazás számára a Data Lake Storage-fiók elérését. Útmutatásért lásd: [hitelesítés a Azure Data Lake Storage Active Directory használatával](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principal:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Külső adatforrás létrehozása
Ezzel a [create External adatforrás](/sql/t-sql/statements/create-external-data-source-transact-sql) -paranccsal tárolhatók az adattárolók helye. Ha a HRE átmenő hitelesítéssel végzi a hitelesítést, a HITELESÍTő adatok paraméterének megadása nem kötelező. 

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Az adatformátum konfigurálása
Az adatok Data Lake Storageból történő importálásához meg kell adnia a külső fájlformátumot. Ez az objektum határozza meg, hogy a fájlok hogyan íródnak Data Lake Storageba.
A teljes lista esetében tekintse meg a T-SQL-dokumentáció [külső fájlformátum létrehozása](/sql/t-sql/statements/create-external-file-format-transact-sql) című dokumentumot.

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>Külső táblák létrehozása
Most, hogy megadta az adatforrás és a fájl formátumát, készen áll a külső táblák létrehozására. A külső táblák a külső adatforrásokkal való interakciók. A Location paraméter egy fájlt vagy egy könyvtárat is megadhat. Ha megadja a könyvtárat, a rendszer a könyvtárban lévő összes fájlt betölti.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Külső tábla szempontjai
Egy külső tábla létrehozása egyszerű, de van néhány olyan árnyalat, amelyet meg kell vitatni.

A külső táblák erősen begépeltek. Ez azt jelenti, hogy a betöltött adatsorok mindegyikének meg kell felelnie a tábla sémájának definíciójának.
Ha egy sor nem felel meg a séma definíciójának, a rendszer elutasítja a sort a betöltésből.

A REJECT_TYPE és REJECT_VALUE lehetőséggel megadhatja, hogy a végső táblában hány sor vagy milyen százalékban kell szerepelnie az adatmennyiségnek. Ha a betöltés során az elutasítás értéke elérte a értéket, a betöltés sikertelen lesz. Az elutasított sorok leggyakoribb oka a séma definíciójának eltérése. Ha például egy oszlop helytelenül van megadva az int sémája, ha a fájlban lévő adat karakterlánc, akkor minden sor betöltése sikertelen lesz.

Data Lake Storage Gen1 a szerepköralapú Access Control (RBAC) használatával vezérli az adathozzáférést. Ez azt jelenti, hogy az egyszerű szolgáltatásnak olvasási engedéllyel kell rendelkeznie a Location paraméterben definiált címtárakhoz és a végső könyvtár és a fájlok gyermekeihez. Ez lehetővé teszi, hogy a Base hitelesítse és betöltse az adatok betöltését. 

## <a name="load-the-data"></a>Az adatok betöltése
Az adatok Data Lake Storage való betöltéséhez használja a [CREATE TABLE as Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) utasítást. 

A CTAS létrehoz egy új táblát, és feltölti azt egy SELECT utasítás eredményeivel. A CTAS határozza meg, hogy az új tábla ugyanazokat az oszlopokat és adattípusokat tartalmazza, mint a SELECT utasítás eredményei. Ha az összes oszlopot kiválasztja egy külső táblából, az új tábla a külső tábla oszlopainak és adattípusának replikája.

Ebben a példában egy DimProduct nevű kivonatoló elosztott táblát hozunk létre a külső táblából DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Oszlopcentrikus tömörítés optimalizálása
Alapértelmezés szerint a SQL Data Warehouse fürtözött oszlopcentrikus indexként tárolja a táblát. A betöltés befejeződése után előfordulhat, hogy egyes adatsorok nem lesznek tömörítve a oszlopcentrikus.  Ennek számos oka lehet. További információ: [oszlopcentrikus indexek kezelése](sql-data-warehouse-tables-index.md).

A lekérdezés teljesítményének és a oszlopcentrikus a terhelés utáni tömörítésének optimalizálásához építse újra a táblázatot a oszlopcentrikus index kikényszerítéséhez az összes sor tömörítéséhez.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Statisztikák optimalizálása
A legjobb, ha egy terhelés után azonnal létrehoz egy egyoszlopos statisztikát. Néhány statisztikai lehetőség is rendelkezésre áll. Ha például egyoszlopos statisztikát hoz létre minden egyes oszlophoz, akkor hosszú időt vehet igénybe az összes statisztika újraépítéséhez. Ha tudja, hogy bizonyos oszlopok nem szerepelnek a lekérdezési predikátumokban, kihagyhatja ezeket az oszlopokat a statisztikák létrehozásával.

Ha úgy dönt, hogy egyoszlopos statisztikát hoz létre minden táblázat minden oszlopához, használhatja a [statisztikai](sql-data-warehouse-tables-statistics.md) cikkben a tárolt eljárás kódja mintát `prc_sqldw_create_stats`.

Az alábbi példa jó kiindulási pont a statisztikák létrehozásához. Egyoszlopos statisztikát hoz létre a dimenzió tábla minden egyes oszlopán, valamint a táblák egyes összekapcsolási oszlopaiban. Később is hozzáadhat egy vagy több oszlopos statisztikát más táblák oszlopaihoz.

## <a name="achievement-unlocked"></a>A megvalósítás feloldva!
Sikeresen betöltötte az adatAzure SQL Data Warehouseba. Remek munka!

## <a name="next-steps"></a>Következő lépések 
Ebben az oktatóanyagban külső táblákat hozott létre a Data Lake Storage Gen1ban tárolt adatok struktúrájának definiálásához, majd a Base CREATE TABLE AS SELECT utasítás használatával tölti be az adatok adattárházba való betöltését. 

A következőket hajtotta végre:
> [!div class="checklist"]
> * A Data Lake Storageból való betöltéshez szükséges adatbázis-objektumok létrehozása.
> * Egy Data Lake Storage könyvtárhoz csatlakozik.
> * A rendszer betöltötte az adatAzure SQL Data Warehouse.
>

Az adatraktár-megoldások betöltésének első lépése a SQL Data Warehouse használatával történő adattárház-megoldás fejlesztése. Tekintse meg fejlesztési erőforrásait.

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan fejleszthet táblázatok SQL Data Warehouse](sql-data-warehouse-tables-overview.md)
