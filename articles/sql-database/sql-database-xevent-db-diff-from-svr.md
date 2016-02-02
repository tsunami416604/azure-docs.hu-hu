<properties 
    pageTitle="SQL Database 中的擴充事件 | Microsoft Azure" 
    description="描述 Azure SQL Database 中的擴充事件 (XEvents)，以及事件工作階段與 Microsoft SQL Server 中的事件工作階段有如何的些微不同。" 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jeffreyg" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/13/2015" 
    ms.author="genemi"/>



# SQL Database 中的擴充事件

本主題說明 Azure SQL Database 中的擴充事件的實作方式與 Microsoft SQL server 中的擴充事件相較之下，有如何的些微不同。


- SQL Database V12 在 2015 年行事曆下半年度獲得擴充事件功能。
- SQL Server 自 2008 開始即具有擴充事件。
- SQL Database 上的擴充事件功能集是強大的 SQL Server 功能子集。


*XEvents* 是非正式暱稱，有時在部落格或其他非正式位置用於「擴充事件」。

> [AZURE.NOTE] 從 2015 年 10 月起，擴充事件工作階段功能會在 Azure SQL Database 的預覽層級中啟動。 尚未設定公開上市 (GA) 日期。
> 
> Azure [服務更新](http://azure.microsoft.com/updates/?service=sql-database) GA 公告時，頁面會有貼文。


## 必要條件

本主題假設您已經有一些下列項目的知識：


- [Azure SQL Database 服務](http://azure.microsoft.com/services/sql-database/)。

- [擴充事件](http://msdn.microsoft.com/library/bb630282.aspx) Microsoft SQL Server 中。
 - 我們的大部分文件是關於適用於 SQL Server 和 SQL Database 的擴充事件。


選擇做為事件檔案時，先前曝露在下列項目是很有幫助 [目標](#AzureXEventsTargets):


- [Azure 儲存體服務](http://azure.microsoft.com/services/storage/)

- PowerShell
 - [使用 Azure PowerShell 與 Azure 儲存體](storage-powershell-guide-full.md) -提供 PowerShell 和 Azure 儲存體服務的完整資訊。


## 程式碼範例

相關的主題提供兩個程式碼範例：


- [環緩衝區目標的程式碼 SQL 資料庫中的擴充事件](sql-database-xevent-code-ring-buffer.md)
 - 簡短的簡單 Transact-SQL 指令碼。
 - 我們強調主題中的程式碼範例，當您完成信號緩衝區目標之後，您應該釋放其資源執行卸除 alter `更改事件工作階段...ON 資料庫卸除目標...;` 陳述式。 稍後您可以加入另一個執行個體的信號緩衝區 `ALTER EVENT SESSION......資料庫新增目標上`。

- [SQL 資料庫中的擴充事件的事件檔案目標程式碼](sql-database-xevent-code-event-file.md)
 - 階段 1 是 PowerShell，建立 Azure 儲存體容器。
 - 階段 2 是 Transact-SQL，使用 Azure 儲存體容器。


## Transact-SQL 差異

- 當您執行 [CREATE EVENT SESSION](http://msdn.microsoft.com/library/bb677289.aspx) 命令在您使用 SQL Server 上 **ON SERVER** 子句。 但是在 SQL Database 上，您改為使用 **ON DATABASE** 子句。

- **ON DATABASE** 子句也適用於 [ALTER EVENT SESSION](http://msdn.microsoft.com/library/bb630368.aspx) 和 [卸除事件工作階段](http://msdn.microsoft.com/library/bb630257.aspx) TRANSACT-SQL 命令。

- 最佳作法是要包含的事件工作階段選項 **STARTUP_STATE = ON** 中您 **CREATE EVENT SESSION**  或 **ALTER EVENT SESSION** 陳述式。
 - **= ON** 值支援在由於容錯移轉而進行邏輯資料庫的重新設定之後，自動重新啟動。


## 新的目錄檢視

擴充的事件功能支援多個 [目錄檢視](http://msdn.microsoft.com/library/ms174365.aspx)。 目錄檢視會告訴您目前資料庫中使用者建立事件工作階段的*中繼資料或定義*的相關資訊。 檢視不會傳回作用中事件工作階段的執行個體的相關資訊。


| 名稱<br/>目錄檢視| 說明|
| :-- | :-- |
| **sys.database_event_session_actions**| 針對事件工作階段的每個事件上的每個動作傳回資料列。|
| **sys.database_event_session_events**| 針對事件工作階段中的每個事件傳回資料列。|
| **sys.database_event_session_fields**| 針對已在事件和目標上明確設定的每個可自訂資料行傳回資料列。|
| **sys.database_event_session_targets**| 針對事件工作階段的每個事件目標傳回資料列。|
| **sys.database_event_sessions**| 針對 SQL Database 資料庫中的每個事件工作階段傳回資料列。|


在 Microsoft SQL Server，類似目錄檢視都有包含 *。 server\_* 而不是 *。 database\_*。 名稱模式類似 **sys.server_event_%**。


## 新的動態管理檢視 [(Dmv)](http://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database 擁有 [動態管理檢視 (Dmv)](http://msdn.microsoft.com/library/bb677293.aspx) 支援擴充的事件。 DMV 會告訴您*作用中*事件工作階段的相關資訊。


| DMV 的名稱| 說明|
| :-- | :-- |
| **sys.dm_xe_database_session_event_actions**| 會傳回事件工作階段動作的相關資訊。|
| **sys.dm_xe_database_session_events**| 會傳回工作階段事件的相關資訊。|
| **sys.dm_xe_database_session_object_columns**| 顯示繫結至工作階段的物件的組態值。|
| **sys.dm_xe_database_session_targets**| 會傳回工作階段目標的相關資訊。|
| **sys.dm_xe_database_sessions**| 針對範圍為目前資料庫的每個事件工作階段傳回資料列。|


在 Microsoft SQL Server，類似目錄檢視名稱不含 *\_database* 部分名稱，例如:


- **sys.dm_xe_sessions**, ，而不是名稱<br/>**sys.dm_xe_database_sessions，以查看**。


### 兩者通用的 DMV

對於擴充事件，有通用於 Azure SQL Database 和 Microsoft SQL Server 的其他 DMV：


- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**


 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## 尋找可用擴充事件、動作和目標

您可以執行簡單 SQL **SELECT** 以取得可用事件、動作和目標的清單。


```
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```



<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a>

&nbsp;

## 您的 SQL Database 事件工作階段的目標

以下是可以從您的 SQL Database 上的事件工作階段擷取結果的目標：


- [信號緩衝區目標](http://msdn.microsoft.com/library/ff878182.aspx) -短暫保留在記憶體中的事件資料。
- [事件計數器目標](http://msdn.microsoft.com/library/ff878025.aspx) -計算擴充的事件工作階段期間發生的所有事件。
- [事件檔案目標](http://msdn.microsoft.com/library/ff878115.aspx) -完整緩衝區寫入 Azure 儲存體容器。


[事件追蹤的 Windows (ETW)](http://msdn.microsoft.com/library/ms751538.aspx) API 不能用於在 SQL Database 上擴充的事件。


## 限制

有幾個安全性相關差異適用於 SQL Database 的雲端環境：


- 擴充事件是在單一租用戶隔離模型中建構。 一個資料庫中的事件工作階段無法從另一個資料庫存取資料或事件。

- 無法在 **master** 資料庫的內容中發出 **CREATE EVENT SESSION** 陳述式。


## 權限模型

您必須擁有資料庫的**控制**權限，才能發出 **CREATE EVENT SESSION** 陳述式。 資料庫擁有者 (dbo) 有**控制**權限。


### 儲存體容器授權

您針對 Azure 儲存體容器產生的 SAS 權杖必須指定權限的 **rwl**。 提供下列權限：


- 讀取
- 寫入
- 列出


## 效能考量

有大量使用擴充事件會累積超出整體系統狀況良好所允許的更多作用中記憶體的案例。 因此，Azure SQL Database 系統動態設定和調整事件工作階段可以累積的作用中記憶體數量的限制。 許多因素會列入動態計算。


如果您收到錯誤訊息，指出已強制執行記憶體最大值，您可以採取的一些更正動作為：


- 執行較少的並行事件工作階段。

- 透過您 **建立** 和 **ALTER** 陳述式中的事件工作階段，減少您在指定的記憶體數量 **MAX\_MEMORY** 子句。


### 網路延遲

**事件檔案**目標在將資料保存到 Azure 儲存體 Blob 時可能會遇到網路延遲或失敗。 SQL Database 中的其他事件可能會延遲，因為它們會等待網路通訊完成。 此延遲會降低您的工作負載。

- 若要減輕這個效能風險，請避免在您的事件工作階段定義中將 **EVENT_RETENTION_MODE** 選項設為 **NO_EVENT_LOSS**。


## 相關連結

- [使用 Azure PowerShell 與 Azure 儲存體](storage-powershell-guide-full.md)。
- [Azure 儲存體 Cmdlet](http://msdn.microsoft.com/library/dn806401.aspx)

- [使用 Azure PowerShell 與 Azure 儲存體](storage-powershell-guide-full.md) -提供 PowerShell 和 Azure 儲存體服務的完整資訊。
- [如何使用.net 的 Blob 儲存體](storage-dotnet-how-to-use-blobs.md)

- [建立認證 (TRANSACT-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [建立事件工作階段 (TRANSACT-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)

- [關於 Microsoft SQL Server 中的擴充事件 Jonathan Kehayias 部落格文章](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


下列連結提供擴充事件的其他程式碼範例主題。 不過，您必須定期檢查所有範例以查看範例是否適用於 Microsoft SQL Server 與 Azure SQL Database。 然後您可以決定是否需要稍加變更來執行範例。








