---
title: "Az Azure-ból betöltése az Azure data warehouse-blob |} Microsoft Docs"
description: "Útmutató az Azure blob storage adatok betöltése az SQL Data Warehouse PolyBase segítségével. A nyilvános adatok a Contoso kereskedelmi adatraktár sémába néhány táblák betöltése."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: faca0fe7-62e7-4e1f-a86f-032b4ffcb06e
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 2859c1144f72fd685af89f83024df1409902ab0c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="load-data-from-azure-blob-storage-into-sql-data-warehouse-polybase"></a>Adatok betöltése az Azure blob storage az SQL Data warehouse-ba (PolyBase)
> [!div class="op_single_selector"]
> * [Data Factory](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
> * [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
> 
> 

Adatok betöltése az Azure blob storage az Azure SQL Data Warehouse PolyBase és T-SQL-parancsok segítségével 

Legyen egyszerű, hogy ez az oktatóanyag tölti be a két tábla az egy nyilvános Azure Storage-Blobból a Contoso kereskedelmi adatraktár sémába. A teljes adatkészlet betöltéséhez futtassa a példa [betölteni a teljes Contoso kereskedelmi adatraktár] [ Load the full Contoso Retail Data Warehouse] a Microsoft SQL Server minták tárházból.

Az oktatóanyag tartalma:

1. Az Azure blob storage betöltése a PolyBase konfigurálása
2. Nyilvános adatok betöltése az adatbázisba
3. Hajtsa végre a optimalizálást, a betöltés befejezése után.

## <a name="before-you-begin"></a>Előkészületek
Ez az oktatóanyag elindításához Azure-fiókot, amely már rendelkezik egy SQL Data Warehouse-adatbázis kell. Ha még nem rendelkezik ezzel, lásd: [SQL Data Warehouse létrehozása][Create a SQL Data Warehouse].

## <a name="1-configure-the-data-source"></a>1. Konfigurálja az adatforrást
A polybase külső T-SQL-objektumok a hely és a külső adatokra vonatkozó attribútumok megadhatók. A külső objektum az SQL Data Warehouse tárolja. Maga az adat a külsőleg tárolódik.

### <a name="11-create-a-credential"></a>1.1. Hitelesítő adatok létrehozása
**Kihagyhatja ezt a lépést** Ha a Contoso nyilvános adatokat tölt be. A nyilvános adataihoz való biztonságos hozzáférés nem szükséges, mert már bárki hozzáférhet.

**Ne hagyja ki ezt a lépést** használata ebben az oktatóanyagban sablonként a saját adatok feltöltését. Adatok eléréséhez a hitelesítő adatokat, használja a következő parancsfájl egy adatbázishoz kötődő hitelesítő adatok létrehozása, és majd használatra, ha az adatforrás helyének meghatározása.

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

Ugorjon a 2.

### <a name="12-create-the-external-data-source"></a>1.2. A külső adatforrás létrehozása
Ezzel [külső ADATFORRÁS létrehozása] [ CREATE EXTERNAL DATA SOURCE] parancs a helyét, valamint az adatok, milyen típusú adatok tárolásához. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Ha arra, hogy az azure blob storage tárolók nyilvánosságra választja, ne feledje, hogy az adatok tulajdonosa fizetnie kell adatok kilépő díjak amikor adatok elhagyja az Adatközpont. 
> 
> 

## <a name="2-configure-data-format"></a>2. Az adatformátum konfigurálása
Az adatok szöveg fájlok az Azure blob Storage tárolóban tárolják, és minden mező elválasztóval választja el egymástól. Ez [külső FÁJLFORMÁTUM létrehozása] [ CREATE EXTERNAL FILE FORMAT] parancs használatával adja meg az adatok formátumának a szövegfájlok. A Contoso adatok tömörítetlen és az adatcsatorna tagolt.

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
Most, hogy a megadott forrás- és a fájl adatformátum, készen áll a külső táblák létrehozására. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Hozzon létre egy sémát.
A hely, a Contoso adatokat az adatbázisban tárolni, hozzon létre egy séma.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Hozza létre a külső táblákat.
Futtassa ezt a parancsfájlt a DimProduct és FactOnlineSales külső tábla létrehozásához. Csak azt is itt az oszlopneveket és adattípusokat meghatározása, és a kötése a hely és az Azure blob storage fájlok formátuma. A definíció SQL Data Warehouse tárolja, és az adatok továbbra is megtalálható az Azure Storage-Blobba.

A **hely** paraméter megadása a mappa alatt az Azure Storage-Blobba gyökérmappáját. Minden tábla másik mappában van.

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
Külső adatok eléréséhez különböző módon van.  Adatok közvetlenül a külső tábla lekérdezése, az adatok betöltése az új adatbázistáblák vagy külső adatok hozzáadása a meglévő táblák.  

### <a name="41-create-a-new-schema"></a>4.1. Hozzon létre egy új sémát
CTAS új táblát hoz létre, amely adatokat tartalmaz.  Először hozzon létre egy contoso sémát.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Az adatok betöltése az új táblák
Adatok betöltése az Azure blob storage és mentse a munkafüzetet egy táblát az adatbázis belül használja a [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] utasítást. A CTAS betöltése kihasználja az imént létrehozott szigorú típusmegadású külső táblákra. Adatok betöltése az új táblák, segítségével [CTAS] [ CTAS] táblánként utasítást. 
 
CTAS új táblát hoz létre, és feltölti a select utasítás a eredményekkel. CTAS határozza meg az új tábla ugyanazon oszlopok és adattípusok rendelkezik, mint a select utasítás eredményét. Ha minden oszlop egy külső tábla, az új táblázat lesz a külső tábla az adattípusokat és az oszlopok egy replikát.

Ebben a példában azt hozni a dimenzió, illetve a ténytábla, elosztott táblák kivonat. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 a folyamat előrehaladását betöltése
A dinamikus felügyeleti nézetekkel (dinamikus felügyeleti nézetek) betöltési állapotának nyomon követheti. 

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
Alapértelmezés szerint az SQL Data Warehouse tárolja a tábla fürtözött oszlopcentrikus index. A betöltés befejezése után az adatsorok némelyike esetleg nem tömörítse össze az oszlopcentrikus.  Miért Ez akkor fordulhat elő, ennek több van. További tudnivalókért lásd: [oszlopcentrikus Indexek kezelése][manage columnstore indexes].

Optimalizálható a teljesítmény-küszöbérték és a betöltés után oszlopcentrikus tömörítés, építse újra az oszloptárindexet az összes sor tömörítendő kényszerítése a tábla. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Az oszlopcentrikus indexek fenntartása További információkért lásd: a [oszlopcentrikus Indexek kezelése] [ manage columnstore indexes] cikk.

## <a name="6-optimize-statistics"></a>6. Statisztika optimalizálása
Érdemes a betöltés után azonnal létrehozza a egyoszlopos statisztikát. Nincsenek néhány statisztikai lehetőségeit. Például ha egyoszlopos statisztikát hoz létre minden egyes oszlophoz a statisztika, hogy hosszú ideig eltarthat. Ha ismeri az egyes oszlopok nem tervezi, hogy a lekérdezés predikátumok kell, kihagyhatja a statisztikák létrehozása az ilyen oszlopokat.

Ha egy oszlop statisztikákat létrehozni minden tábla minden egyes oszlophoz, használhatja a tárolt eljárás kódminta `prc_sqldw_create_stats` a a [statisztika] [ statistics] cikk.

A következő példa: statisztikák létrehozása az jó kiindulási pont. Minden egyes oszlopának a dimenziótáblában és a ténytáblák csatlakozó oszlopainak egyoszlopos statisztikát hoz. Mindig hozzáadhat egy vagy több oszlop statisztikai más tény táblaoszlopok később.

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

## <a name="achievement-unlocked"></a>Zárolása feloldva elérésének!
Az Azure SQL Data Warehouse nyilvános adatok sikeresen töltött. Remek munka!

Most elindíthatja a lekérdezésekkel például a következő táblázatok lekérdezése:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Következő lépések
A teljes Contoso kereskedelmi Data Warehouse-adatok betöltése, a parancsfájl használata további fejlesztési tippek. További információ: [SQL Data Warehouse fejlesztői áttekintés][SQL Data Warehouse development overview].

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
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
