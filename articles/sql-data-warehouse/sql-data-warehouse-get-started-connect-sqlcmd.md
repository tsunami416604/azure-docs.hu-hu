<properties
   pageTitle="入門：連接到 Azure SQL 資料倉儲 | Microsoft Azure"
   description="開始連線到 SQL 資料倉儲並執行一些查詢。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager=""
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="twounder"/>

# 使用 SQLCMD 連接及查詢

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

本逐步解說將示範使用 sqlcmd.exe 公用程式在短時間內連接和查詢 Azure SQL 資料倉儲資料庫的方式。 在本逐步解說中，您將：

+ 安裝必要軟體
+ 連接到包含 AdventureWorksDW 範例資料庫的資料庫
+ 針對範例資料庫執行查詢  

## 先決條件

+ [sqlcmd.exe](https://msdn.microsoft.com/library/azure/ms162773.aspx) -若要下載 sqlcmd.exe，請參閱 [Microsoft Command Line Utilities 11 for SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501)。

## 取得您的完整 Azure SQL 伺服器名稱

若要連接到您的資料庫中，您需要伺服器的完整名稱 (***servername**。.database.windows.net*)，其中包含您想要連接到的資料庫。

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至您想連接的資料庫。
3. 找出完整的伺服器名稱 (我們將在下列步驟中使用此名稱)：

![][1]


## 使用 sqlcmd 連接到 SQL 資料倉儲

若要使用的 sqlcmd，您必須開啟命令提示字元並輸入連接到 SQL 資料倉儲的特定執行個體 **sqlcmd** 後面接著 SQL 資料倉儲資料庫的連接字串。 連接字串必須包含下列參數：

+ **使用者 (-U):** 表單中的伺服器使用者 `<`使用者`>`
+ **密碼 (-P):** 與使用者相關聯的密碼。
+ **伺服器 (-S):** Server 形式 `<`伺服器名稱`>`。.database.windows.net
+ **資料庫 (-D):** 資料庫名稱。
+ **啟用引號識別項 (-我):** 必須啟用引號識別項，才能連接到 SQL 資料倉儲執行個體。

因此，若要連接到 SQL 資料倉儲執行個體，您需輸入下列資訊：

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## 執行範例查詢

連線之後，您可以對執行個體發出任何支援的 Transact-SQL 陳述式。 

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT COUNT(*) FROM dbo.FactInternetSales;
2> GO
3> QUIT
```

如需 sqlcmd 的其他資訊請參閱 [sqlcmd 文件](https://msdn.microsoft.com/library/azure/ms162773.aspx)。


## 後續步驟

現在，您可以連接和查詢，請嘗試 [連接與 PowerBI][]。

[connecting with PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
 

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png

