<properties
   pageTitle="將範例資料載入 SQL 資料倉儲 | Microsoft Azure"
   description="將範例資料載入 SQL 資料倉儲"
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
   ms.date="11/02/2015"
   ms.author="lodipalm;barbkess"/>


# 將範例資料載入 SQL 資料倉儲

一旦您 [建立的 SQL 資料倉儲資料庫執行個體 ][create a sql data warehouse database instance] 下一個步驟是建立並載入某些資料表。 您可以使用我們為 SQL 資料倉儲建立的 Adventure Works 範例指令碼，建立並載入名為 Adventure Works 之虛構公司的資料表。 這些指令碼使用 sqlcmd 來執行 SQL，並使用 bcp 載入資料。 如果您還沒有安裝這些工具，請遵循下列連結以 [安裝 ][] 及 [安裝 sqlcmd []][]。

請遵循下列簡單的步驟，將 Adventure Works 範例資料庫載入至 SQL DW...

1. 下載 [Adventure Works 的範例指令碼的 SQL 資料倉儲 []][]。

2. 請從下載的 zip 將檔案解壓縮到本機電腦上的目錄。

3. 編輯解壓縮的 aw_create.bat 檔案，並設定下列可在檔案頂端找到的變數。 請務必不要在 "=" 和參數之間留有空白。 以下是您可能會想看看如何編輯的範例。

        server=mylogicalserver.database.windows.net
        user=mydwuser
        password=Mydwpassw0rd
        database=mydwdatabase

4. 從 Windows 命令提示字元執行編輯過的 aw_create.bat。 請確定您所在的目錄是您儲存編輯過之 aw_create.bat 版本的位置。
此指令碼將...
    * 卸除任何 Adventure Works 資料表或任何已存在資料庫中的檢視表
    * 建立 Adventure Works 資料表和檢視表
    * 使用 bcp 載入每個 Adventure Works 資料表
    * 驗證每個 Adventure Works 資料表的資料列計數
    * 收集每個 Adventure Works 資料表之所有資料行上的統計資料


## 查詢範例資料

一旦已經將某些範例資料載入您的 SQL 資料倉儲中，就可以快速地執行幾個查詢。 若要執行查詢時，連接到使用 Visual Studio 和 SSDT，Azure SQL DW 中新建立的 Adventure Works 資料庫中所述 [連接 []][] 文件。

可取得員工之所有資訊的簡單 select 陳述式範例：

    SELECT * FROM DimEmployee;

使用建構 (例如 GROUP BY) 來查看每一天所有銷售之總金額的較複雜查詢範例：

    SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
    FROM FactInternetSales
    GROUP BY OrderDateKey
    ORDER BY OrderDateKey;

可篩選掉特定日期前之訂單的 SELECT 搭配 WHERE 子句的範例：

    SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
    FROM FactInternetSales
    WHERE OrderDateKey > '20020801'
    GROUP BY OrderDateKey
    ORDER BY OrderDateKey;

SQL 資料倉儲幾乎支援所有 SQL Server 支援的 T-SQL 建構。 所有差異都記錄於我們 [移轉程式碼 []][] 文件。

## 後續步驟

現在您已經有機會嘗試一些範例資料的查詢時，請參閱如何 [開發 []][], ，[載入 []][], ，或 [移轉 []][] 到 SQL 資料倉儲。






[migrate]: ./sql-data-warehouse-overview-migrate.md 
[develop]: ./sql-data-warehouse-overview-develop.md 
[load]: ./sql-data-warehouse-overview-load.md 
[connect]: ./sql-data-warehouse-get-started-connect.md 
[migrate code]: ./sql-data-warehouse-migrate-code.md 
[create a sql data warehouse database instance]: ./sql-data-warehouse-get-started-provision.md 
[install bcp]: ./sql-data-warehouse-load-with-bcp.md 
[install sqlcmd]: ./sql-data-warehouse-get-started-connect-query-sqlcmd.md 
[adventure works sample scripts for sql data warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip 

