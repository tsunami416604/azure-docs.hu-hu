---
title: Contoso kereskedelmi adatok betöltése az Azure SQL Data Warehouse |} A Microsoft Docs
description: Két tábla betöltése az a Contoso kereskedelmi adatok az Azure SQL Data Warehouse a PolyBase és T-SQL parancsok használatával.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: cf484d5d7a48117bac659dcfad356238f1e9f8a2
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307437"
---
# <a name="load-contoso-retail-data-to-azure-sql-data-warehouse"></a>Contoso kereskedelmi adatok betöltése az Azure SQL Data warehouse-bA

Két tábla betöltése az a Contoso kereskedelmi adatok az Azure SQL Data Warehouse a PolyBase és T-SQL parancsok használatával. A teljes adatkészlet betöltése, futtasson le a [betölteni a teljes Contoso kereskedelmi célú adattárház](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) a Microsoft SQL Server-minták tárházból.

Ez az oktatóanyag tartalma:

1. Az Azure blob storage-ból betöltése a PolyBase konfigurálása
2. Nyilvános adatok betöltése az adatbázisba
3. Hajtsa végre a optimalizálása, a betöltés befejezése után.

## <a name="before-you-begin"></a>Előkészületek
Ez az oktatóanyag futtatásához szüksége van egy Azure-fiókra, amely már rendelkezik az SQL Data Warehouse-adatbázis. Ha még nem rendelkezik ezzel, [hozzon létre egy SQL Data Warehouse][Create a SQL Data Warehouse].

## <a name="1-configure-the-data-source"></a>1. Az adatforrás konfigurálása
A polybase külső T-SQL-objektumok a hely és a külső adatok attribútumok meghatározásához. A külső objektum az SQL Data Warehouse tárolja. Maga az adatok külső erőforrásfájlban.

### <a name="11-create-a-credential"></a>1.1. Hitelesítő adatok létrehozása
**Kihagyhatja ezt a lépést** Ha tölt be a Contoso nyilvános adatai. A nyilvános adatokhoz való biztonságos hozzáférés nem van szüksége, mivel ez már mindenki számára elérhető.

**Ne hagyja ki ezt a lépést** használata ebben az oktatóanyagban egy sablont a saját adatok betöltésére. Adatok eléréséhez a hitelesítő adatok használatával, használja a következő szkriptet az adatbázishoz kötődő hitelesítő adatok létrehozása, és aztán a segítségükkel az adatforrás helyének meghatározásakor.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

Folytassa a 2.

### <a name="12-create-the-external-data-source"></a>1.2. A külső adatforrás létrehozása
Ezzel [CREATE EXTERNAL DATA SOURCE] [ CREATE EXTERNAL DATA SOURCE] parancs használatával tárolja az adatokat és az adatok típusát. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Ha úgy dönt, hogy az azure blob storage-tárolók nyilvános, ne feledje, hogy az adatok tulajdonosa, meg kell fizetni adatok kimenő forgalom költségeit adatok távozik az adatközpontban. 
> 
> 

## <a name="2-configure-data-format"></a>2. Adatformátum konfigurálása
Az adatok Azure blob storage-ban szöveges fájlok tárolják, és minden mező egyes egy elválasztó karaktert. Futtassa a következő parancsot [CREATE EXTERNAL FILE FORMAT] [ CREATE EXTERNAL FILE FORMAT] parancs használatával adhatja meg az adatok formátumát a szöveges fájlok. A Contoso adatok tömörítetlen és cső tagolt.

```sql
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

## <a name="3-create-the-external-tables"></a>3. A külső táblák létrehozása
Most, hogy megadta a forrás- és a fájl adatformátum, készen áll a külső táblák létrehozásához. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Hozzon létre egy sémát az adatokat.
Hely, a Contoso adatokat az adatbázisban tárolni, hozzon létre egy sémát.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Hozza létre a külső táblákat.
Futtassa ezt a szkriptet a DimProduct és FactOnlineSales külső táblák létrehozása. Az összes itt teljesítünk oszlopnevek és adattípusok definiálása, és kötése őket a hely és az Azure blob storage-fájlok formátuma. A definíció az SQL Data Warehouse tárolja, és az adatok továbbra is szerepel az Azure Storage-Blobba.

A **hely** paraméter értéke a mappát a gyökérmappában található az Azure Storage-blobba. Minden tábla van egy másik mappába.

```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. Az adatok betöltése
Külső adatok eléréséhez különböző módon van.  Adatok közvetlenül a külső tábla lekérdezése, az adatok betöltése az új adatbázis-táblákat, vagy külső adatok felvétele az meglévő adatbázistáblák.  

### <a name="41-create-a-new-schema"></a>4.1. Hozzon létre egy új sémát
A CTAS új táblát hoz létre, amely adatokat tartalmaz.  Először hozzon létre egy sémát a contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Az adatok betöltése az új táblák
Adatok betöltése az Azure blob storage-ból, és mentse azt egy tábla belül az adatbázis, használja a [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] utasítást. A CTAS betöltése használ az imént létrehozott külső táblák típusos. Adatok betöltése az új táblákat, használja az egyik [CTAS] [ CTAS] táblánként utasítást. 
 
A CTAS egy új táblát hoz létre, és feltölti azt egy kiválasztási utasítás eredményeivel. A CTAS határozza meg az új tábla, ugyanazokat az oszlopokat és adattípusokkal rendelkezik, mint a kiválasztási utasítás eredményei. Ha minden oszlop egy külső táblából választ, az új tábla lesz a külső tábla adattípusok és az oszlopok egy replikát.

Ebben a példában létrehozzuk a dimenzió és a ténytábla elosztott táblák kivonat szerint is. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 a betöltés előrehaladását nyomon követése
A dinamikus felügyeleti nézetekkel (DMV-kkel) használó betöltés előrehaladását nyomon követheti. 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. Oszlopcentrikus tömörítés optimalizálása
Alapértelmezés szerint az SQL Data Warehouse tárolja a tábla egy fürtözött oszlopcentrikus indexet. A betöltés befejezése után az adatsorokat némelyike előfordulhat, hogy nem tömöríti az oszloptárba.  Nincs több okból miért Ez akkor történhet. További tudnivalókért lásd: [oszlopcentrikus Indexek kezelése][manage columnstore indexes].

Lekérdezési teljesítmény és a egy betöltés után oszlopcentrikus tömörítés optimalizálása érdekében végezze el a tábla az oszlopcentrikus indexet az összes sor tömörítendő kényszerítése. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Az oszlopcentrikus indexek fenntartásával kapcsolatos további információkért lásd: a [oszlopcentrikus Indexek kezelése] [ manage columnstore indexes] cikk.

## <a name="6-optimize-statistics"></a>6. Statisztika optimalizálása
Célszerű a betöltés után azonnal létrehozza a egyoszlopos statisztikát. Van néhány statisztikai lehetőségeit. Például ha egyoszlopos statisztikát hoz létre minden oszlopról építse újra az összes statisztika hosszú ideig eltarthat. Ha ismeri az egyes oszlopok nem gazdakiszolgálói lesznek az olyan predikátumokban lekérdezés, kihagyhatja a statisztikák létrehozása az oszlopokat.

Ha úgy dönt, hogy egy oszlop statisztikákat létrehozni minden tábla minden oszlopához, használhatja a tárolt eljárás kódminta `prc_sqldw_create_stats` a a [statisztika] [ statistics] cikk.

Az alábbi példa a statisztikák létrehozása az jó kiindulási pont. A dimenzió tábla minden oszlopához, és az egyes csatlakozó oszlopa a ténytáblák hozza egyoszlopos statisztikát. Mindig hozzáadhat egy vagy több oszlop statisztikai más (tény) tábla oszlopait később.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Feloldotta eléréséhez!
Nyilvános adatok Azure SQL Data Warehouse-bA sikeresen töltött. Remek munka!

Most már megkezdheti a lekérdezésekkel például a következő táblák lekérdezése:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>További lépések
A teljes Contoso Retail Data Warehouse-adatok betöltése, a parancsprogramot használja, további fejlesztési tippek, lásd: [SQL Data Warehouse fejlesztői áttekintés][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
