---
title: Contoso-beli kiskereskedelmi adatforgalom betöltése a Azure SQL Data Warehouseba | Microsoft Docs
description: 'A contoso kiskereskedelmi adatokból a Azure SQL Data Warehouseba betölthet két táblázatot a következőben: Base és T-SQL-parancsok.'
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b96b65b7dd38900fccb8d5d3a9133f37ee93949f
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "67595518"
---
# <a name="load-contoso-retail-data-to-azure-sql-data-warehouse"></a>Contoso-beli kiskereskedelmi adatforgalom betöltése a Azure SQL Data Warehouseba

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a "Base" és a "T-SQL" parancsokat két tábla betöltésére a contoso kiskereskedelmi adatokból Azure SQL Data Warehouseba. 

Ebben az oktatóanyagban a következőket fogja elsajátítani:

1. A tároló konfigurálása az Azure Blob Storage-ból való betöltéshez
2. Nyilvános adattárolás betöltése az adatbázisba
3. Optimalizálások végrehajtása a betöltés befejeződése után.

## <a name="before-you-begin"></a>Előkészületek
Az oktatóanyag futtatásához olyan Azure-fiókra van szükség, amely már rendelkezik SQL Data Warehouse. Ha nincs kiépített adattárház, tekintse [meg a SQL Data Warehouse létrehozása és a kiszolgálói szintű tűzfalszabály beállítása][Create a SQL Data Warehouse]című témakört.

## <a name="1-configure-the-data-source"></a>1. Az adatforrás konfigurálása
A Base T-SQL külső objektumokat használ a külső adatok helyének és attribútumainak meghatározásához. A külső objektum-definíciókat a SQL Data Warehouse tárolja. Az adattárolást külsőleg tárolja a rendszer.

### <a name="11-create-a-credential"></a>1.1. Hitelesítő adat létrehozása
**Hagyja ki ezt a lépést** , ha betölti a contoso nyilvános információit. Nem szükséges biztonságos hozzáférést biztosítani a nyilvános adatbázishoz, mert már mindenki számára elérhető.

**Ne ugorja át ezt a lépést** , ha az oktatóanyagot sablonként használja a saját adatai betöltéséhez. Az adatok hitelesítésen keresztüli eléréséhez használja az alábbi parancsfájlt egy adatbázis-hatókörű hitelesítő adat létrehozásához, majd használja az adatforrás helyének meghatározásakor.

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

### <a name="12-create-the-external-data-source"></a>1.2. Külső adatforrás létrehozása
Ezzel a [create External adatforrás][CREATE EXTERNAL DATA SOURCE] -paranccsal tárolhatók az adathalmazok és az adattípusok. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Ha úgy dönt, hogy az Azure Blob Storage-tárolókat nyilvánosként adja meg, ne feledje, hogy az adatok tulajdonosaként a kimenő adatforgalomért fizetendő díjat kell fizetnie, amikor az adatok elhagyják az adatközpontot. 
> 
> 

## <a name="2-configure-data-format"></a>2. Az adatformátum konfigurálása
Az adatok tárolása szövegfájlokban történik az Azure Blob Storage-ban, és minden mező elválasztó karakterrel elválasztva. A SSMS-ben futtassa a következő [create External File Format (külső fájlformátum létrehozása][CREATE EXTERNAL FILE FORMAT] ) parancsot a szövegfájlokban lévő adatformátum megadásához. A contoso-adatmennyiség tömörítve van, és a cső tagolva van.

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

## <a name="3-create-the-external-tables"></a>3. Külső táblák létrehozása
Most, hogy megadta az adatforrás és a fájl formátumát, készen áll a külső táblák létrehozására. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Hozzon létre egy sémát az adatkezeléshez.
Ha létre szeretne hozni egy helyet a contoso-adatbázis tárolására, hozzon létre egy sémát.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Hozza létre a külső táblákat.
Futtassa a következő szkriptet a DimProduct és a FactOnlineSales külső táblák létrehozásához. Itt csak az oszlopnevek és az adattípusok vannak meghatározva, és az Azure Blob Storage-fájlok helyére és formátumára kell kötni őket. A definíció tárolása SQL Data Warehouse történik, és az adatmennyiség még mindig a Azure Storage Blob.

A **Location** paraméter a Azure Storage blob gyökérkönyvtárában található mappa. Minden táblázat egy másik mappában található.

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
A külső adatforrásokhoz többféleképpen férhet hozzá.  Az adatok közvetlenül a külső táblákból is lefoglalhatók, az adatok betölthetők az adatraktár új tábláiba, vagy külső adatok is hozzáadhatók a meglévő adattárház-táblákhoz.  

### <a name="41-create-a-new-schema"></a>4.1. Új séma létrehozása
A CTAS egy új táblát hoz létre, amely tartalmaz egy adatkészletet.  Először hozzon létre egy sémát a contoso-adatkészlethez.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Az adatgyűjtés új táblákba
Az Azure Blob Storage-ból származó adatok az adatraktár táblájába való betöltéséhez használja a [CREATE TABLE as Select (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] utasítást. Az CTAS-be való betöltéssel a létrehozott, szigorúan beírt külső táblákat használja. Az adatok új táblákba való betöltéséhez használjon egy [CTAS][CTAS] utasítást egy táblában. 
 
A CTAS létrehoz egy új táblát, és feltölti azt egy SELECT utasítás eredményeivel. A CTAS határozza meg, hogy az új tábla ugyanazokat az oszlopokat és adattípusokat tartalmazza, mint a SELECT utasítás eredményei. Ha az összes oszlopot kiválasztja egy külső táblából, az új tábla a külső tábla oszlopainak és adattípusának replikája lesz.

Ebben a példában a dimenziót és a Fact táblát is létrehozjuk kivonatoló elosztott táblákként. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4,3 a betöltési folyamat nyomon követése
A terhelés előrehaladását a dinamikus felügyeleti nézetek (DMV) használatával követheti nyomon. 

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
Alapértelmezés szerint a SQL Data Warehouse fürtözött oszlopcentrikus indexként tárolja a táblát. A betöltés befejeződése után előfordulhat, hogy egyes adatsorok nem lesznek tömörítve a oszlopcentrikus.  Ennek számos oka lehet. További információ: [oszlopcentrikus indexek kezelése][manage columnstore indexes].

A lekérdezés teljesítményének és a oszlopcentrikus a terhelés utáni tömörítésének optimalizálásához építse újra a táblázatot a oszlopcentrikus index kikényszerítéséhez az összes sor tömörítéséhez. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

A oszlopcentrikus indexek karbantartásával kapcsolatos további információkért tekintse meg a [oszlopcentrikus-indexek kezelése][manage columnstore indexes] című cikket.

## <a name="6-optimize-statistics"></a>6. Statisztikák optimalizálása
A legjobb, ha egy terhelés után azonnal létrehoz egy egyoszlopos statisztikát. Ha tudja, hogy bizonyos oszlopok nem a lekérdezési predikátumokban lesznek, kihagyhatja az oszlopok statisztikáinak létrehozását. Ha egyoszlopos statisztikát hoz létre minden oszlophoz, hosszú időt is igénybe vehet az összes statisztika újraépítéséhez. 

Ha úgy dönt, hogy egyoszlopos statisztikát hoz létre minden táblázat minden oszlopához, használhatja a [statisztikai][statistics] cikk tárolt eljárás `prc_sqldw_create_stats` kódja mintáját.

Az alábbi példa jó kiindulási pont a statisztikák létrehozásához. Egyoszlopos statisztikát hoz létre a dimenzió tábla minden egyes oszlopán, valamint a táblák egyes összekapcsolási oszlopaiban. Később is hozzáadhat egy vagy több oszlopos statisztikát más táblák oszlopaihoz.

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

## <a name="achievement-unlocked"></a>A megvalósítás feloldva!
Sikeresen betöltötte a nyilvános adatbevitelt Azure SQL Data Warehouseba. Remek munka!

Most már megkezdheti a táblázatok lekérdezését, hogy megismerkedjen az adataival. Futtassa a következő lekérdezést a teljes értékesítés/márka megállapításához:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>További lépések
A teljes adathalmaz betöltéséhez futtassa a példát a [teljes contoso kiskereskedelmi adattárház](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) betöltésére a Microsoft SQL Server Samples adattárból.

További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés][SQL Data Warehouse development overview].

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
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
