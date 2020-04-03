---
title: Contoso kiskereskedelmi adatainak betöltése egy Synapse SQL adattárházba
description: A PolyBase és a T-SQL parancsokkal két táblát tölthet be a Contoso kiskereskedelmi adataiból a Synapse SQL-be.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7460a59dd2a7a5906a483195929136391657fa50
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584011"
---
# <a name="load-contoso-retail-data-to-a-synapse-sql-data-warehouse"></a>Contoso kiskereskedelmi adatainak betöltése egy Synapse SQL adattárházba

Ebben az oktatóanyagban megtudhatja, hogy a PolyBase és a T-SQL parancsokkal két táblát tölthet be a Contoso kiskereskedelmi adataiból egy Synapse SQL-adattárházba.

Ebben a tutorial lesz:

1. A PolyBase konfigurálása az Azure blob storage-ból történő betöltéshez
2. Nyilvános adatok betöltése az adatbázisba
3. A betöltés befejezése után végezze el az optimalizálást.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag futtatásához olyan Azure-fiókra van szükség, amely már rendelkezik egy Synapse SQL-adattárházsal. Ha nincs kiépítve adattárház, olvassa el az [Adatraktár létrehozása és a kiszolgálószintű tűzfalszabály beállítása című témakört.](create-data-warehouse-portal.md)

## <a name="configure-the-data-source"></a>Az adatforrás konfigurálása

A PolyBase T-SQL külső objektumokat használ a külső adatok helyének és attribútumainak meghatározásához. A külső objektumdefiníciók a Szinapsze SQL-adattárházban tárolódnak. Az adatok tárolása külsőleg történik.

## <a name="create-a-credential"></a>Hitelesítő adatok létrehozása

**Hagyja ki ezt** a lépést, ha a Contoso nyilvános adatok betöltése. Nincs szükség biztonságos hozzáférésre a nyilvános adatokhoz, mivel azok már bárki számára elérhetők.

**Ne hagyja ki ezt a lépést,** ha ezt az oktatóanyagot sablonként használja a saját adatok betöltéséhez. Az adatok hitelesítő adatokeléréséhez használja a következő parancsfájlt egy adatbázis-hatókörű hitelesítő adatok létrehozásához. Ezután használja az adatforrás helyének meghatározásakor.

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

## <a name="create-the-external-data-source"></a>Külső adatforrás létrehozása

Ezzel [a KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-ver15) paranccsal tárolhatja az adatok helyét és típusát. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [!IMPORTANT]
> Ha úgy dönt, hogy az azure blob tárolótárolók nyilvános, ne feledje, hogy az adatok tulajdonosaként az adatok kimenő forgalom díjakat, amikor az adatok elhagyja az adatközpontot. 
> 

## <a name="configure-the-data-format"></a>Az adatformátum konfigurálása

Az adatok az Azure blob storage-ban lévő szöveges fájlokban tárolódnak, és minden mező egy határolóelést tartalmaz. Az SSMS-ben futtassa a következő CREATE EXTERNAL FILE FORMAT parancsot a szövegfájlokban lévő adatok formátumának megadásához. A Contoso-adatok tömörítetlenek és csővel vannak elválasztva.

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

## <a name="create-the-external-tables"></a>A külső táblák létrehozása
Most, hogy megadta az adatforrást és a fájlformátumot, készen áll a külső táblák létrehozására. 

## <a name="create-a-schema-for-the-data"></a>Séma létrehozása az adatokhoz
Ha a Contoso-adatokat az adatbázisban tárolhatja, hozzon létre egy sémát.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>A külső táblák létrehozása

Futtassa a következő parancsfájlt a DimProduct és factOnlineSales külső táblák létrehozásához. Itt mindössze oszlopneveket és adattípusokat határoz meg, és köti őket az Azure blob storage-fájlok helyéhez és formátumához. A definíció az adatraktárban tárolódik, és az adatok továbbra is az Azure Storage Blob.

A **LOCATION** paraméter az Azure Storage Blob gyökérmappája alatti mappa. Minden tábla más mappában van.

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

## <a name="load-the-data"></a>Az adatok betöltése
A külső adatok elérésének különböző módjai vannak.  Lekérdezheti az adatokat közvetlenül a külső táblákból, betöltheti az adatokat az adatraktár új tábláiba, vagy külső adatokat adhat hozzá a meglévő adatraktártáblákhoz.  

###  <a name="create-a-new-schema"></a>Új séma létrehozása

A CTAS új táblát hoz létre, amely adatokat tartalmaz.  Először hozzon létre egy sémát a contoso adatokhoz.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Az adatok betöltése új táblákba

Ha adatokat szeretne betölteni az Azure blob storage-ból az adatraktár-táblába, használja a [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) utasítást. A [CTAS-sel](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md) való betöltés a létrehozott, erősen beírt külső táblákat használja. Ha az adatokat új táblákba szeretné betölteni, táblánként egy CTAS utasítást használjon. 
 
A CTAS létrehoz egy új táblázatot, és feltölti azt egy select utasítás eredményeivel. A CTAS úgy határozza meg az új táblát, hogy ugyanazok az oszlopok és adattípusok legyenek, mint a select utasítás eredményei. Ha egy külső tábla összes oszlopát kijelöli, az új tábla a külső tábla oszlopainak és adattípusainak kópiája lesz.

Ebben a példában a dimenziót és a ténytáblát kivonatoló elosztott táblákként hozzuk létre. 

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>A terhelés imázsának nyomon követése

A terhelés előrehaladását dinamikus felügyeleti nézetek (DMV-k) segítségével követheti nyomon. 

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

## <a name="optimize-columnstore-compression"></a>Oszlopcentrikus tömörítés optimalizálása

Alapértelmezés szerint a Synapse SQL adattárház tárolja a táblát fürtözött oszlopcentrikus indexként. A betöltés befejezése után előfordulhat, hogy az adatsorok egy része nem lesz tömörítve az oszloptárba.  Ennek különböző okai lehetnek. További információ: [Oszlopcentrikus indexek kezelése.](sql-data-warehouse-tables-index.md)

A lekérdezési teljesítmény és az oszlopcentrikus tömörítés terhelés utáni optimalizálásához építse újra a táblát úgy, hogy az oszlopcentrikus index az összes sor tömörítése. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Az oszlopcentrikus indexek karbantartásáról az [oszlopcentrikus indexek kezelése](sql-data-warehouse-tables-index.md) című cikkben olvashat bővebben.

## <a name="optimize-statistics"></a>Statisztika optimalizálása

A legjobb, ha a terhelés után azonnal létrehozhat egyoszlopos statisztikákat. Ha tudja, hogy bizonyos oszlopok nem lesznek lekérdezési predikátumokban, kihagyhatja a statisztikák létrehozását ezeken az oszlopokon. Ha minden oszlopban egyoszlopos statisztikákat hoz létre, az összes statisztika újraépítése hosszú időt vehet igénybe. 

Ha úgy dönt, hogy minden tábla minden oszlopában egyoszlopos statisztikákat hoz `prc_sqldw_create_stats` létre, használhatja a [statisztikai](sql-data-warehouse-tables-statistics.md) cikkben tárolt eljáráskódmintát.

A következő példa jó kiindulási pont a statisztikák létrehozásához. Egyoszlopos statisztikákat hoz létre a dimenziótábla minden oszlopán és a ténytáblák minden egyes illesztési oszlopán. Később bármikor hozzáadhat egy- vagy többoszlopos statisztikákat más ténytábla-oszlopokhoz.

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

## <a name="achievement-unlocked"></a>Eredmény nyitva!
Sikeresen betöltött nyilvános adatokat az adattárházba. Remek munka!

Most már elkezdheti lekérdezni a táblákat az adatok feltárásához. Futtassa a következő lekérdezést a márkánkénti összes értékesítés megkereséséhez:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>További lépések
A teljes adatkészlet betöltéséhez futtassa a példa [töltse be a teljes Contoso kiskereskedelmi adattár](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) a Microsoft SQL Server minttárház.
További fejlesztési tippeket az [adatraktárak tervezési döntései és kódolási technikái című témakörben talál.](sql-data-warehouse-overview-develop.md)
