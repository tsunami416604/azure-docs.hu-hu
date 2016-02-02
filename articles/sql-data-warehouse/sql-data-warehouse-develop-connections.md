<properties
   pageTitle="連線到 SQL 資料倉儲 | Microsoft Azure"
   description="連線到 SQL 資料倉儲以開發解決方案的秘訣。"
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
   ms.date="10/21/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>


# 連線到 SQL 資料倉儲

若要連線到 SQL 資料倉儲，您必須傳入安全性認證進行驗證用途。 建立連線時，您也會看到特定連線設定都設定為建立查詢工作階段的一部分。

本文詳細說明連線到 SQL 資料倉儲的下列層面：

- 驗證
- 連線設定
- 工作階段和要求識別碼


## 驗證

若要連線到 SQL 資料倉儲，您必須提供下列資訊：

- 完整伺服器名稱
- 指定 SQL 驗證
- 使用者名稱
- 密碼
- 預設資料庫 (選擇性)

請務必注意，使用者必須使用 SQL 驗證進行驗證。 此時不支援受信任的驗證。

根據預設，您的連線會連線至主要資料庫，而非您的使用者資料庫。 若要連線到您的使用者資料庫，您可以選擇執行下列兩個動作之一：

1. 使用伺服器註冊 SSDT 或應用程式連接字串中的 SQL Server 物件總管時，請指定預設資料庫。 例如，藉由包含 ODBC 連線的 InitialCatalog 參數。
2. 在 SSDT 中建立工作階段之前先反白顯示使用者資料庫。

> [AZURE.NOTE] 如需連線到 SQL 資料倉儲與 SSDT 的指引，請參閱上一步 [連接並查詢 []][] 入門文章。 

一次是很重要的 TRANSACT-SQL 陳述式 * * 使用 <your DB>* * 不支援變更的資料庫連接

## 應用程式連線通訊協定

您可以使用下列任合通訊協定連線到 SQL 資料倉儲：

- ADO.NET
- ODBC
- PHP
- JDBC

### 範例 ADO.NET 連接字串

```
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### 範例 ODBC 連接字串

```
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### 範例 PHP 連接字串

```
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### 範例 JDBC 連接字串

```
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Connection settings
SQL Data Warehouse standardizes a few settings during connection and object creation. These cannot be overridden.

| Database Setting   | Value                        |
| :----------------- | :--------------------------- |
| ANSI_NULLS         | ON                           |
| QUOTED_IDENTIFIERS | ON                           |
| NO_COUNT           | OFF                          |
| DATEFORMAT         | mdy                          |
| DATEFIRST          | 7                            |
| Database Collation | SQL_Latin1_General_CP1_CI_AS |

## Sessions and requests
Once a connection has been made and a session has been established you are ready to write and submit queries to SQL Data Warehouse.

Each query will be represented by one or more request identifiers. All queries submitted on that connection are part of a single session and will therefore be represented by a single session id.

However, as SQL Data Warehouse is a distributed MPP system both session and request identifiers are exposed a little differently when compared to SQL Server. 

Sessions and requests are logically represented by their respective identifiers.

| Identifier | Example value |
| :--------- | :------------ |
| Session ID | SID123456     |
| Request ID | QID123456     |

Notice that the Session ID is prefixed by SID - shorthand for Session ID - and the requests are prefixed by QID which is shorthand for Query ID.

You will need this information to help you identify your query when monitoring your query performance. You can monitor your query performance by using either the [Azure Portal] and the dynamic management views.

To identify which session you are currently using the following function:
```
選取 SESSION_ID()
;
```

To view all the queries that are either running or have recently run against your data warehouse you can use a query like the one below:
```
CREATE VIEW dbo.vSessionRequests
AS
選取 [session_id] AS Session_ID s。
        [狀態] AS Session_Status s。
        [login_name] AS Session_LoginName s。
        [login_time] AS Session_LoginTime s。
        [request_id] AS Request_ID。
        [狀態] AS Request_Status。
        [submit_time] AS Request_SubmitTime。
        [start_time] AS Request_StartTime。
        [end_compile_time] AS Request_EndCompileTime。
        [end_time] AS Request_EndTime。
        [total_elapsed_time] AS Request_TotalElapsedDuration_ms。
        DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        DATEDIFF(ms,[start_time],[end_compile_time]) AS Request_CompileDuration_ms
        DATEDIFF(ms,[end_compile_time],[end_time]) AS Request_ExecDuration_ms
        [標籤] AS Request_QueryLabel
        [命令] AS Request_Command
        [database_id] AS Request_Database_ID
從 sys.dm_pdw_exec_requests r
聯結 sys.dm_pdw_exec_sessions s ON [session_id] = s [session_id]
在 s [session_id] <> SESSION_ID()。
;
```

請注意，這項查詢已封裝在檢視中，所以您可以將其併入您的解決方案。 不過，若要查看結果，您必須建立檢視並加以執行。

## 後續步驟

一但連線之後，您就可以開始設計您的資料表。 請參閱 [資料表設計] 文件以取得詳細資料。











[connect and query]: ./sql-data-warehouse-get-started-connect.md 
[table design]: ./sql-data-warehouse-develop-table-design.md 

