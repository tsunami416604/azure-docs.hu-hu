<properties
   pageTitle="管理 SQL 資料倉儲中的統計資料 | Microsoft Azure"
   description="在 Azure SQL 資料倉儲中管理統計資料以便開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/11/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>


# 管理 SQL 資料倉儲中的統計資料

 SQL 資料倉儲使用統計資料來評估以不同方式執行分散式查詢的成本。 如果統計資料很精確，查詢最佳化工具即可產生高品質查詢計劃，以改善查詢效能。

建立和更新統計資料極為重要，以便達到 SQL 資料倉儲預計提供的查詢效能。 本指南提供統計資料的概觀，然後顯示如何：

- 在資料庫設計階段建立統計資料
- 在資料庫維護階段更新統計資料
- 利用系統檢視和函式檢視統計資料

## 統計資料簡介

單一資料行統計資料是含有單一資料行中值範圍和次數相關資訊的物件。 查詢最佳化工具會使用此長條圖來估計查詢結果中的資料列數目。 這會直接影響如何最佳化查詢的相關決策。

多重資料行統計資料是對一份資料行清單建立的統計資料。 其中包含清單中第一個資料行的單一資料行統計資料，再加上一些跨資料行關聯性資訊 (稱為密度)。 多重資料行統計資料可以改善某些作業 (例如複合 joins 和 group by) 的查詢效能。

如需詳細資訊，請參閱 [DBCC SHOW_STATISTICS []][] MSDN 上。

## 為何需要統計資料？

若無正確的統計資料，您將無法獲得 SQL 資料倉儲預計提供的效能。 資料表和資料行都沒有 SQL 資料倉儲自動產生的統計資料，所以您需要自行建立。 在建立資料表時建立統計資料，然後在您填入統計資料後予以更新是個不錯的主意。
> [AZURE.NOTE] 如果您使用 SQL Server，您可能會視需要依賴 SQL Server 為您建立和更新單一資料行統計資料。 SQL 資料倉儲在這方面有所不同。 由於資料已分散，所以 SQL 資料倉儲不會自動彙總所有分散式資料的統計資料。 它只會在您建立和更新統計資料時產生彙總統計資料。

## 何時建立統計資料

一組一致的最新統計資料是 SQL 資料倉儲的重要部分。 因此，務必在設計資料表階段建立統計資料。

建立每個資料行的單一資料行統計資料是開始使用統計資料的簡單方式。 不過，建立和更新統計資料的效能與成本之間總有一些取捨。 如果您建立所有資料行的單一資料行統計資料，而後發現更新所有統計資料花費太長的時間，您可以捨棄一些統計資料，或較常更新其中一些統計資料。

只有在資料行位於複合 joins 或 group by 子句時，查詢最佳化工具才會使用多重資料行統計資料。 複合篩選條件目前並未受益於多重資料行統計資料。

啟動 SQL 資料倉儲開發時因此它是個不錯的主意實作下列模式:
- 每個資料表上的每個資料行上建立單一資料行統計資料
- 使用聯結和群組中的查詢子句的資料行上建立多個資料行統計資料。

當您了解要如何查詢您的資料時，您可能想要修改此模型 - 尤其在資料表的範圍很廣時。 如需更進階的方法，請參閱 [實作統計資料管理] (## 實作統計資料管理) 一節。

## 何時更新統計資料

請務必在您的資料庫管理例行工作中納入更新統計資料。 資料庫中的資料散發情況改變時，就需要更新統計資料。 否則，您可看見次佳的查詢效能，而進一步排解查詢疑難所做的付出可能不值得。

因此，進行查詢疑難排解時，首先要詢問的問題之一就是「統計資料是最新的嗎？」

這個問題不是可依存留期回答的問題。 最新的統計資料物件有可能非常舊。 當資料列數目或特定資料行的值散發有實質變更時，您*就* 需要更新統計資料。

例如，資料倉儲中的日期資料行通常需要經常更新統計資料。 每次有新資料列載入資料倉儲時，就會加入新的載入日期或交易日期。 這些會改變資料散發情況並使統計資料過時。

相反地，客戶資料表上性別資料行的統計資料可能永遠不需要更新。 假設客戶間的散發固定不變，將新資料列加入至資料表變化並不會改變資料散發情況。 不過，如果資料倉儲只包含一種性別，而新的需求導致多種性別，您肯定需要更新性別資料行的統計資料。

如需進一步說明，請參閱 [統計資料 []][] MSDN 上。

## 實作統計資料管理

擴充您的資料載入程序通常是個不錯的主意，可確保在載入結束時更新統計資料。 當資料表變更其大小和/或其值散發時，資料載入最為頻繁。 因此，這是實作某些管理程序的合理位置。

以下提供一些指導原則，以便在載入過程中更新您的統計資料：

- 確保每個載入的資料表至少都更新一個統計資料物件。 這會在統計資料更新過程中更新資料表大小 (資料列計數和頁面計數) 資訊。
- 將焦點放在參與 JOIN、GROUP BY、ORDER BY 和 DISTINCT 子句的資料行。
- 考慮較頻繁更新「遞增索引鍵」資料行 (例如交易日期)，因為這些值不會包含在統計資料長條圖中。
- 考慮較不常更新靜態散發資料行。
- 請記得，每個統計資料物件會依序更新。只實作 `更新統計資料 < TABLE_NAME >` 可能不太理想-尤其是對具有許多統計資料物件的寬資料表。

> [AZURE.NOTE] 如需 [遞增索引鍵] 的詳細資訊，請參閱 SQL Server 2014 基數估計模型白皮書。

如需進一步說明，請參閱  [基數估計 []][] MSDN 上。

## 範例：建立統計資料

下列範例顯示如何使用各種選項來建立統計資料。 您用於每個資料行的選項取決於您的資料特定以及在查詢中使用資料行的方式。

### 答：使用預設選項建立單一資料行統計資料

若要建立資料行的統計資料，只需提供統計資料物件的名稱和資料行的名稱。

此語法會使用所有預設選項。 根據預設，SQL 資料倉儲在建立統計資料時會取樣 20% 的資料表。

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

例如：

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### B.檢查每個資料列以建立單一資料行統計資料

20% 的預設取樣率足以應付大部分的情況。 不過，您可以調整取樣率。

若要取樣整個資料表，請使用此語法：

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

例如：

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### C.指定取樣大小以建立單一資料行統計資料

或者，您可以以百分比指定取樣大小：

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### D.只對某些資料列建立單一資料行統計資料

另一個選項是，您可以對資料表中部分的資料列建立統計資料。 這稱為篩選的統計資料。

例如，當您計劃查詢大型分割資料表的特定分割時，可以使用篩選的統計資料。 只對分割值建立統計資料，統計資料的精確度將會改善，並因而改善查詢效能。

這個範例會建立某個值範圍的統計資料。 您可以輕鬆地定義這些值以符合分割中的值範圍。

```
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE] 若要讓查詢最佳化工具在選擇分散式查詢計劃時考慮使用篩選的統計資料，查詢必須符合統計資料物件的定義。 使用上述範例，查詢的 where 子句需要指定介於 2000101 和 20001231 之間的 col1 值。

### E.使用所有選項建立單一資料行統計資料

您當然可以將選項合在一起。 以下範例會使用自訂樣本大小建立篩選的統計資料物件：

```
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

如需完整的參考，請參閱 [建立統計資料 []][] MSDN 上。

### F.建立多重資料行統計資料

若要建立多重資料行統計資料，只需利用上述範例，但要指定更多資料行。
> [AZURE.NOTE] 用來估計查詢結果中資料列數目的長條圖，只適用於統計資料物件定義中所列的第一個資料行。

在此範例中，長條圖位於 *product\_category*。 跨資料行統計資料就會計算 *product\_category* 和 *product\_sub_c\ategory*:

```
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

因為沒有之間的相互關聯 *product\_category* 和 *product\_sub\_category*, ，多重資料行統計可以同時存取這些資料行時很有用。

### G.對資料表中的所有資料行建立統計資料

建立統計資料的其中一個方法是在建立資料表後發出 CREATE STATISTICS 命令。

```
CREATE TABLE dbo.table1 
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### H.使用預存程序對資料庫中的所有資料行建立統計資料

SQL 資料倉儲並沒有系統預存程序相當於 [[sp_create_stats]][] SQL Server 中。 此預存程序會對資料庫中還沒有統計資料的每個資料行建立單一資料行統計資料物件。

這會協助您開始進行資料庫設計。 請放心地依照您的需求進行調整。

```
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN   sys.[external_tables] e ON  e.[object_id]       = t.[object_id]
WHERE       l.[object_id] IS NULL
AND         e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

若要利用此程序對資料表中的所有資料行建立統計資料，只需呼叫程序即可。

```
prc_sqldw_create_stats;
```

## Examples: update statistics

To update statistics, you can:

1. Update one statistics object. Specify the name of the statistics object you wish to update.
2. Update all statistics objects on a table. Specify the name of the table instead of one specific statistics object.


### A. Update one specific statistics object ###
Use the following syntax to update a specific statistics object:
```
更新統計資料 [schema_name]。[table_name]([stat_name]);
```

For example:
```
更新統計資料 [dbo]。[] table1([stats_col1])。
```

By updating specific statistics objects, you can minimize the time and resources required to manage statistics. This requires some thought, though, to choose the best statistics objects to update.


### B. Update all statistics on a table ###
This shows a simple method for updating all the statistics objects on a table.
```
更新統計資料 [schema_name]。[]; table_name
```

For example:
```
更新統計資料 dbo.table1。
```

This statement is easy to use. Just remember this updates all statistics on the table, and therefore might perform more work than is necessary. If the performance is not an issue, this is definitely the easiest and most complete way to guarantee statistics are up-to-date.

> [AZURE.NOTE] When updating all statistics on a table, SQL Data Warehouse does a scan to sample the table for each statistics. If the table is large, has many columns, and many statistics, it might be more efficient to udpate individual statistics based on need.

For an implementation of an `UPDATE STATISTICS` procedure please see the [temporary tables] article. The implementation method is slightly different to the `CREATE STATISTICS` procedure above but the end result is the same.

For the full syntax, see [Update Statistics][] on MSDN.

## Statistics metadata
There are several system view and functions that you can use to find information about statistics. For example, you can see if a statistics object might be out-of-date by using the stats-date function to see when statistics were last created or updated.

### Catalog views for statistics
These system views provide information about statistics:

| Catalog View | Description |
| :----------- | :---------- |
| [sys.columns][]  | One row for each column. |
| [sys.objects][]  | One row for each object in the database. |  |
| [sys.schemas][]  | One row for each schema in the database. |  |
| [sys.stats][] | One row for each statistics object. |
| [sys.stats_columns][] | One row for each column in the statistics object. Links back to sys.columns. |
| [sys.tables][] | One row for each table (includes external tables). |
| [sys.table_types][] | One row for each data type. |


### System functions for statistics
These system functions are useful for working with statistics:

| System Function | Description |
| :-------------- | :---------- |
| [STATS_DATE][]    | Date the statistics object was last updated. |
| [DBCC SHOW_STATISTICS][] | Provides summary level and detailed information about the distribution of values as understood by the statistics object. |

### Combine statistics columns and functions into one view

This view brings columns that relate to statistics, and results from the [STATS_DATE()][]function together.
```
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[名稱] 為 [schema_name]
,       tb.[名稱]                          為 [table_name]
聖 [名稱] 為 [stats_name]
聖 [filter_definition] 為 [stats_filter_defiinition]
聖 [has_filter] 為 [stats_is_filtered]
STATS_DATE(st.[object_id],st.[stats_id])
                                            為 [stats_last_updated_date]
co [名稱] 為 [stats_column_name]
,       ty.[名稱]                          為 [column_type]
co [max_length] 為 [column_max_length]
co [精確度] 為 [column_precision]
co [小數位數] 為 [column_scale]
co [is_nullable] 為 [column_is_nullable]
co [collation_name] 為 [column_collation_name]
QUOTENAME(sm.[name]) +'。 '+ QUOTENAME(tb.[]) name
                                            為 two_part_name
QUOTENAME(DB_NAME()) +'。 '+ QUOTENAME(sm.[name]) +'。 '+ QUOTENAME(tb.[]) name
                                            為 three_part_name
從 sys.objects ob 為
St ON ob [object_id] 作為聯結 sys.stats = 聖 [object_id]
Sc ON 聖 [stats_id] 作為聯結 sys.stats_columns = sc [stats_id]
                            和聖 [object_id] = sc [object_id]
Co ON sc [column_id] 作為聯結 sys.columns = co.[column_id]
                            和 sc [object_id] = co.[object_id]
聯結 sys.types 為 ty ON co.[user_type_id] = 智。[] user_type_id
聯結 sys.tables 為 tb ON co.[object_id] = tb。[] object_id
您可以加入 sys.schemas，sm ON tb。[schema_id] = sm.[schema_id]
其中 1 = 1 
和聖 [user_created] = 1
;
```

## DBCC SHOW_STATISTICS() examples

DBCC SHOW_STATISTICS() shows the data held within a statistics object. This data comes in three parts.

1. Header
2. Density Vector
3. Histogram

The header metadata about the statistics. The histogram displays the distribution of values in the first key column of the statistics object. The density vector measures cross-column correlation. SQLDW computes cardinality estimates with any of the data in the statistics object.

### Show header, density, and histogram

This simple example shows all three parts of a statistics object.
```
DBCC SHOW_STATISTICS ([<schema_name>。<table_name>]，<stats_name>)
```

For example:
```
DBCC SHOW_STATISTICS dbo.table1 (stats_col1);
```

### Show one or more parts of DBCC SHOW_STATISTICS();

If you are only interested in viewing specific parts, use the `WITH` clause and specify which parts you want to see:
```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) 與 stat_header、 長條圖、 density_vector
```

For example:
```
DBCC SHOW_STATISTICS (dbo.table1、 stats_col1) 與長條圖 density_vector
```

## DBCC SHOW_STATISTICS() 差異

相較於 SQL Server，DBCC SHOW_STATISTICS() 在 SQL 資料倉儲中會更嚴格地實作。

1. 不支援未記載的功能
- 無法使用 Stats_stream
- 無法聯結特定統計資料子集的結果，例如 (STAT_HEADER JOIN DENSITY_VECTOR)
2. 無法針對訊息隱藏項目設定 NO_INFOMSGS
3. 無法使用統計資料名稱前後的方括號
4. 無法使用資料行名稱來識別統計資料物件
5. 不支援自訂錯誤 2767


## 後續步驟

如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀 []][]。






[sql data warehouse development overview]: ./sql-data-warehouse-overview-develop.md 
[temporary tables]: ./sql-data-warehouse-develop-temporary-tables.md 
[cardinality estimation]: https://msdn.microsoft.com/library/dn600374.aspx 
[create statistics]: https://msdn.microsoft.com/library/ms188038.aspx 
[dbcc show_statistics]: https://msdn.microsoft.com/library/ms174384.aspx 
[statistics]: https://msdn.microsoft.com/library/ms190397.aspx 
[stats_date]: https://msdn.microsoft.com/library/ms190330.aspx 
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx 
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx 
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx 
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx 
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx 
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx 
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx 
[update statistics]: https://msdn.microsoft.com/library/ms187348.aspx 

