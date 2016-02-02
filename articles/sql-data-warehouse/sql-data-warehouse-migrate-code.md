<properties
   pageTitle="將您的 SQL 程式碼移轉至 SQL 資料倉儲 | Microsoft Azure"
   description="將 SQL 程式碼移轉至 Azure SQL 資料倉儲來開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/09/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>


# 將您的 SQL 程式碼移轉至 SQL 資料倉儲

為了確保您的程式碼符合 SQL 資料倉儲，您很可能需要變更您的程式碼基底。 某些 SQL 資料倉儲功能設計為直接以分散式方式運作，可以大幅改善效能。 不過，為了維持效能和延展性，某些功能也無法使用。

## Transact-SQL 程式碼變更

下列清單摘要說明 Azure SQL 資料倉儲中不支援的主要功能。 此連結會帶您前往不支援功能的因應措施：

- 
- 
- 
- 跨資料庫聯結
- 
- 
- 
- output 子句
- 內嵌使用者定義函數
- 多重陳述式函式
- 
- [遞迴通用資料表運算式 (CTE)](#Recursive-common-table-expressions-(CTE)
- CLR 函式和程序
- $partition 函式
- 資料表變數
- 資料表值參數
- 分散式交易
- 認可/回復工作
- 儲存交易
- 執行內容 (EXECUTE AS)
- 
- 
- 
- 
- 

幸好這些限制大部分都可以克服。 上面提及的相關開發文章中已提供說明。

### 通用資料表運算式

目前在 SQL 資料倉儲內實作的通用資料表運算式 (CTE) 具有下列功能和限制：


+ 
+ 
+ 
+ 
+ 
+ 
+ 
+ 


+ 
+ 
+ 
+ 
+ 
+ 不過，如果 CTE 是做為 sp_prepare 所準備之 CETAS 中的一部分時，就會因為針對 sp_prepare 實作繫結的方式而導致其行為與 SQL Server 和其他 PDW 陳述式不同。 如果參考 CTE 的 SELECT 使用不存在於 CTE 的錯誤資料行，sp_prepare 將會通過而不會偵測到錯誤，但在 sp_execute 期間則會擲回錯誤。

### 遞迴通用資料表運算式 (CTE)

這是複雜的移轉案例，最佳的程序是拆解 CTE 並以多個步驟進行處理。 一般來說，您可以使用迴圈，並在逐一執行遞迴中間的查詢時填入暫存資料表。 一旦暫存資料表填完之後，您可以將資料傳回做為單一結果集。

### 系統函數

另外還有一些不支援的系統函式。 您通常可能會發現資料倉儲中使用的主要函式包括：

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

同樣地許多這些問題都可以克服。

例如，下列程式碼擷取 @@ROWCOUNT 資訊的替代解決方案：

```
SELECT  SUM(row_count) AS row_count 
FROM    sys.dm_pdw_sql_requests 
WHERE   row_count <> -1 
AND     request_id IN 
                    (   SELECT TOP 1    request_id 
                        FROM            sys.dm_pdw_exec_requests 
                        WHERE           session_id = SESSION_ID() 
                        ORDER BY end_time DESC
                    )
;
```

## 後續步驟













[ansi joins on updates]: sql-data-warehouse-develop-ctas.md 
[ansi joins on deletes]: sql-data-warehouse-develop-ctas.md 
[merge statement]: sql-data-warehouse-develop-ctas.md 
[insert..exec]: sql-data-warehouse-develop-temporary-tables.md 
[cursors]: sql-data-warehouse-develop-loops.md 
[select..into]: sql-data-warehouse-develop-ctas.md 
[group by clause with rollup / cube / grouping sets options]: sql-data-warehouse-develop-group-by-options.md 
[nesting levels beyond 8]: sql-data-warehouse-develop-transactions.md 
[updating through views]: sql-data-warehouse-develop-views.md 
[use of select for variable assignment]: sql-data-warehouse-develop-variable-assignment.md 
[no max data type for dynamic sql strings]: sql-data-warehouse-develop-dynamic-sql.md 
[development overview]: sql-data-warehouse-overview-develop.md 

