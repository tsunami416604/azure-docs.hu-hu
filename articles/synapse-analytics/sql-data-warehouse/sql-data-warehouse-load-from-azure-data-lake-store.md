---
title: Oktatóanyag betöltési adatai az Azure Data Lake Storage-ból
description: A PolyBase külső tábláival adatokat tölthet be az Azure Data Lake Storage for Synapse SQL-ből.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9713d73ee132f743ceea98cbaca6a83f36fd3a45
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416119"
---
# <a name="load-data-from-azure-data-lake-storage-for-sql-analytics"></a>Adatok betöltése az Azure Data Lake Storage-ból az SQL Analytics számára

Ez az útmutató ismerteti, hogyan használhatja a PolyBase külső táblák adatok betöltése az Azure Data Lake Storage-ból. Bár a Data Lake Storage-ban tárolt adatokon ad hoc lekérdezéseket futtathat, javasoljuk, hogy a legjobb teljesítmény érdekében importálja az adatokat.

> [!NOTE]  
> A betöltés alternatívája a [COPY utasítás](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) jelenleg nyilvános előzetes verzióban.  A COPY utasítás biztosítja a legnagyobb rugalmasságot. Ha visszajelzést szeretne küldeni a COPY utasításról, küldjön egy e-mailt a következő terjesztési listára: sqldwcopypreview@service.microsoft.com.
>
> [!div class="checklist"]
>
> * A Data Lake Storage-ból történő betöltéshez szükséges adatbázis-objektumok létrehozása.
> * Csatlakozás egy Data Lake Storage-könyvtárhoz.
> * Adatok betöltése az adattárházba.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag megkezdése előtt töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) legújabb verzióját.

Az oktatóanyag futtatásához a következőkre van szükség:

* Egy SQL készlet. Lásd: [SQL-készlet és lekérdezési adatok létrehozása](create-data-warehouse-portal.md).
* Egy Data Lake Storage-fiók. Lásd: [Az Azure Data Lake Storage első lépései.](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Ehhez a tárfiókhoz konfigurálnia vagy megadnia kell a következő hitelesítő adatok egyikét a betöltéshez: egy tárfiókkulcs, egy Azure Directory-alkalmazás felhasználó vagy egy AAD-felhasználó, amely rendelkezik a megfelelő RBAC szerepkörrel a tárfiókhoz.

## <a name="create-a-credential"></a>Hitelesítő adatok létrehozása

Ezt a szakaszt kihagyhatja, és az AAD-áthaladási parancatáláskor a "Külső adatforrás létrehozása" című részre léphet. Az AAD-áthaladás használatakor nem szükséges adatbázis-hatókörrel rendelkező hitelesítő adatokat létrehozni vagy megadni, de győződjön meg arról, hogy az AAD-felhasználó rendelkezik a megfelelő RBAC-szerepkörrel (Storage Blob Data Reader, Contributor vagy Owner Role) a tárfiókhoz. További részletek [itt](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260)vázolt .

A Data Lake Storage-fiók eléréséhez létre kell hoznia egy adatbázis-főkulcsot a hitelesítő adatok titkoskulcsának titkosításához. Ezután hozzon létre egy adatbázis hatókörrel hitelesítő adatok tárolására a titkos kulcsot. A szolgáltatásegyszerű szolgáltatás (Az Azure Directory-alkalmazás felhasználója) használatával történő hitelesítéskor az adatbázis-hatókörrel hitelesítő adatok az AAD-ban beállított egyszerű szolgáltatáshitelesítő adatokat tárolja. Az adatbázis hatóköre hitelesítő adatok segítségével is tárolhatja a Gen2 tárfiókkulcsát.

A Data Lake Storage szolgáltatásnévi szolgáltatások használatával való csatlakozáshoz **először** létre kell hoznia egy Azure Active Directory-alkalmazást, létre kell hoznia egy hozzáférési kulcsot, és hozzáférést kell biztosítania az alkalmazásnak a Data Lake Storage-fiókhoz. További információt a [Hitelesítés az Azure Data Lake Storage hitelesítése az Active Directory használatával (Hitelesítés az Azure Data Lake Storage szolgáltatásba) témakörben talál.](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Jelentkezzen be az SQL-készletbe egy CONTROL szintű engedélyekkel rendelkező felhasználóval, és hajtsa végre a következő SQL-utasításokat az adatbázison:

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

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

Ezzel [a KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) paranccsal tárolhatja az adatok helyét. Ha AAD-áthaladással hitelesít, a CREDENTIAL paraméter nem kötelező. Ha a szolgáltatásvégpontok felügyelt identitáshasználatával hitelesíti, kövesse ezt a [dokumentációt](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-sql-data-warehouse-polybase) a külső adatforrás beállításához.

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

## <a name="configure-data-format"></a>Adatformátum konfigurálása

Az adatok importálásához a Data Lake Storage,meg kell adnia a külső fájlformátum. Ez az objektum határozza meg, hogyan vannak írva a fájlok a Data Lake Storage.This object defines how the files are written in Data Lake Storage.
A teljes listát, nézd meg a T-SQL dokumentáció [létrehozása külső fájlformátum](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

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

## <a name="create-the-external-tables"></a>A külső táblák létrehozása

Most, hogy megadta az adatforrást és a fájlformátumot, készen áll a külső táblák létrehozására. A külső táblák a külső adatokkal való interakció t. A helyparaméter megadhat egy fájlt vagy egy könyvtárat. Ha könyvtárat ad meg, a könyvtárban lévő összes fájl betöltődik.

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

Létrehozása egy külső tábla egyszerű, de van néhány árnyalatok, amelyeket meg kell vitatni.

A külső táblák erősen bevannak gépelve. Ez azt jelenti, hogy a betöltés alatt álló adatok minden soránmeg kell felelnie a táblaséma definíciójának.
Ha egy sor nem felel meg a sémadefiníciónak, a rendszer elutasítja a sort a terhelésből.

A REJECT_TYPE és REJECT_VALUE beállítások lehetővé teszik annak meghatározását, hogy hány sornak vagy az adatok hány százalékának kell jelen lennie a döntő táblázatban. A betöltés során, ha eléri a selejtértékét, a terhelés sikertelen lesz. Az elutasított sorok leggyakoribb oka a sémadefiníció-eltérés. Ha például egy oszlop helytelenül kapja meg az int sémáját, amikor a fájlban lévő adatok karakterlánc, akkor minden sor nem töltődik be.

A Data Lake Storage Gen1 szerepköralapú hozzáférés-vezérlést (RBAC) használ az adatokhoz való hozzáférés szabályozásához. Ez azt jelenti, hogy a szolgáltatásnévnek olvasási engedéllyel kell rendelkeznie a helyparaméterben definiált könyvtárakhoz és a végső könyvtár és fájlok gyermekeihez. Ez lehetővé teszi a PolyBase számára az adatok hitelesítését és betöltését.

## <a name="load-the-data"></a>Az adatok betöltése

Adatok betöltéséhez a Data Lake Storage-ból használja a [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) utasítást.

A CTAS létrehoz egy új táblázatot, és feltölti azt egy select utasítás eredményeivel. A CTAS úgy határozza meg az új táblát, hogy ugyanazok az oszlopok és adattípusok legyenek, mint a select utasítás eredményei. Ha egy külső tábla összes oszlopát kijelöli, az új tábla a külső tábla oszlopainak és adattípusainak kópiája.

Ebben a példában egy DimProduct nevű kivonatoló elosztott táblát hozunk létre a külső DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```

## <a name="optimize-columnstore-compression"></a>Oszlopcentrikus tömörítés optimalizálása

Alapértelmezés szerint a táblák fürtözött oszlopcentrikus indexként vannak definiálva. A betöltés befejezése után előfordulhat, hogy az adatsorok egy része nem lesz tömörítve az oszloptárba.  Számos oka lehet annak, hogy ez miért történhet meg. További információ: [Oszlopcentrikus indexek kezelése.](sql-data-warehouse-tables-index.md)

A lekérdezési teljesítmény és az oszlopcentrikus tömörítés terhelés utáni optimalizálásához építse újra a táblát úgy, hogy az oszlopcentrikus index az összes sor tömörítése.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Statisztika optimalizálása

A legjobb, ha közvetlenül a terhelés után egyoszlopos statisztikákat hoz létre. Van néhány választási lehetőség a statisztikák. Ha például minden oszlopban egyoszlopos statisztikákat hoz létre, az összes statisztika újraépítése hosszú időt vehet igénybe. Ha tudja, hogy bizonyos oszlopok nem lesznek lekérdezési predikátumokban, kihagyhatja a statisztikák létrehozását ezeken az oszlopokon.

Ha úgy dönt, hogy minden tábla minden oszlopában egyoszlopos statisztikákat hoz `prc_sqldw_create_stats` létre, használhatja a [statisztikai](sql-data-warehouse-tables-statistics.md) cikkben tárolt eljáráskódmintát.

A következő példa jó kiindulási pont a statisztikák létrehozásához. Egyoszlopos statisztikákat hoz létre a dimenziótábla minden oszlopán és a ténytáblák minden egyes illesztési oszlopán. Később bármikor hozzáadhat egy- vagy többoszlopos statisztikákat más ténytábla-oszlopokhoz.

## <a name="achievement-unlocked"></a>Eredmény nyitva!

Sikeresen betöltött adatokat az adattárházba. Remek munka!

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban külső táblákat hozott létre a Data Lake Storage Gen1-ben tárolt adatok szerkezetének meghatározásához, majd a PolyBase CREATE TABLE AS SELECT utasítást használta az adatok betöltéséhez az adattárházba.

A következőket hajtotta végre:
> [!div class="checklist"]
>
> * A Data Lake Storage-ból való betöltéshez szükséges adatbázis-objektumok létrehozása.
> * Egy Data Lake Storage könyvtárhoz csatlakoztatva.
> * Adatok betöltése az adattárházba.
>

Az adatok betöltése az első lépés az Azure Synapse Analytics használatával egy adatraktár-megoldás fejlesztéséhez. Tekintse meg fejlesztési forrásainkat.

> [!div class="nextstepaction"]
> [További információ az adatraktározási táblák kidolgozásáról](sql-data-warehouse-tables-overview.md)
