<properties
   pageTitle="疑難排解 |Microsoft Azure"
   description="疑難排解 SQL 資料倉儲的問題。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager=""
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/11/2015"
   ms.author="twounder"/>


# 疑難排解

下列主題列出一些客戶在使用 SQL 資料倉儲時常見的問題。

## 連線能力

連接到 Azure SQL 資料倉儲可能會因為下列幾個常見的原因而失敗：

- 未設定防火牆規則
- 使用不支援的工具/通訊協定

### 防火牆規則

為確保只有已知的 IP 位址可以存取資料庫，Azure SQL 資料庫受到伺服器及資料庫層級的防火牆所保護。 防火牆預設受到保護，亦即您必須允許您的 IP 位址存取，才能連線。

若要設定您的防火牆，允許存取，請依照 [設定伺服器防火牆存取用戶端 IP](sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip) 區段 [佈建](sql-data-warehouse-get-started-provision.md) 頁面。

### 使用不支援的工具/通訊協定

SQL 資料倉儲支援 [Visual Studio 2013/2015年](sql-data-warehouse-get-started-connect.md) 為開發環境和 [SQL Server Native Client 10/11 (ODBC)](https://msdn.microsoft.com/library/ms131415.aspx) 用戶端連接性。

請參閱我們 [連線](sql-data-warehouse-get-started-connect.md) 若要了解更多的頁面。

## 查詢效能

SQL 資料倉儲會使用一般的 SQL Server 建構來執行查詢 (含統計資料)。 [統計資料](sql-data-warehouse-develop-statistics.md) 所包含的範圍和資料庫資料行中值的頻率的相關資訊的物件。 查詢引擎會使用這些統計資料最佳化查詢執行，以及改善查詢效能。 您可以使用下列查詢，判斷您的上次更新了哪些統計資料。

```
SELECT
    sm.[name]                                   AS [schema_name],
    tb.[name]                                   AS [table_name],
    co.[name]                                   AS [stats_column_name],
    st.[name]                                   AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id])    AS [stats_last_updated_date]
FROM
    sys.objects             AS ob
    JOIN sys.stats          AS st   ON  ob.[object_id]      = st.[object_id]
    JOIN sys.stats_columns  AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
    JOIN sys.columns        AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
    JOIN sys.types           AS ty  ON  co.[user_type_id]   = ty.[user_type_id]
    JOIN sys.tables          AS tb  ON  co.[object_id]      = tb.[object_id]
    JOIN sys.schemas         AS sm  ON  tb.[schema_id]      = sm.[schema_id]
WHERE
    1=1 
    AND st.[user_created] = 1;
```

請參閱我們 [統計資料](sql-data-warehouse-develop-statistics.md) 若要了解更多的頁面。

## 關鍵效能概念

請參閱下列文章，以瞭解一些關鍵效能與調整的其他概念：

- [效能和調整]][]
- [並行存取模型]][]
- [設計資料表]][]
- [選擇雜湊散發索引鍵的資料表]][]
- [統計資料來改善效能]][]

## 後續步驟

請參閱 [開發概觀 []][] 文章，以取得有關建置 SQL 資料倉儲解決方案的一些指引。












[performance and scale]: sql-data-warehouse-performance-scale.md 
[concurrency model]: sql-data-warehouse-develop-concurrency.md 
[designing tables]: sql-data-warehouse-develop-table-design.md 
[choose a hash distribution key for your table]: sql-data-warehouse-develop-hash-distribution-key 
[statistics to improve performance]: sql-data-warehouse-develop-statistics.md 
[development overview]: sql-data-warehouse-overview-develop.md 

