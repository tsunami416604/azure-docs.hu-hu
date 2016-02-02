<properties
   pageTitle="從 SQL 資料倉儲中的使用者錯誤復原資料庫 | Microsoft Azure"
   description="從 SQL 資料倉儲中的使用者錯誤復原資料庫的步驟 "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/23/2015"
   ms.author="sahajs"/>


# 從 SQL 資料倉儲中的使用者錯誤復原資料庫

SQL 資料倉儲提供兩個核心功能，可從造成非預期之資料損毀或刪除的使用者錯誤復原。

- 還原即時資料庫
- 還原已刪除的資料庫

這兩個功能會在相同的伺服器上還原至新的資料庫。

## 復原即時資料庫

當使用者錯誤造成非預期的資料修改時，您可以在保留期限內將資料庫還原到任何還原點。 即時資料庫的資料庫快照集每 8 小時會作用一次，並且保留 7 天。

### PowerShell

使用 Azure PowerShell 可以程式設計方式執行資料庫還原。若要下載 Azure PowerShell 模組，請執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)。

若要還原資料庫，請使用 [Start-azuresqldatabaserestore []][] 指令程式。

1. 開啟 Microsoft Azure PowerShell。
2. 連接到您的 Azure 帳戶，並列出與您的帳戶相關聯的所有訂用帳戶。
3. 選取包含要還原之資料庫的訂用帳戶。
4. 列出資料庫的還原點 (需要 Azure 資源管理模式)。
5. 使用 RestorePointCreationDate 挑選出想要的還原點。
6. 將資料庫還原到想要的還原點。
7. 監視還原的進度。

```

Add-AzureAccount
Get-AzureSubscription
Select-AzureSubscription -SubscriptionName "<Subscription_name>"

# List database restore points
Switch-AzureMode AzureResourceManager
Get-AzureSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database to restore
Switch-AzureMode AzureServiceManagement
$Database = Get-AzureSqlDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為上述 Powershell Cmdlet 中的 -ServerName。

### REST API

使用 REST 可以程式設計方式執行資料庫還原。

1. 使用 Get 資料庫還原點作業取得資料庫還原點清單。
2. 開始使用還原 [建立資料庫還原要求 []][] 作業。
3. 使用追蹤還原狀態 [資料庫作業狀態]][] 作業。

還原完成之後，您可以設定復原的資料庫可供遵循 [完成復原的資料庫 []][] 指南。

## 復原已刪除的資料庫

若資料庫已刪除，您可以將已刪除的資料庫還原至刪除的時間點。 Azure SQL 資料倉儲會在卸除資料庫前採用資料庫快照集，並保留 7 天。

### PowerShell

使用 Azure PowerShell 以程式設計方式還原已刪除的資料庫。若要下載 Azure PowerShell 模組，請執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)。

若要還原已刪除的資料庫，請使用 [Start-azuresqldatabaserestore []][] 指令程式。

1. 開啟 Microsoft Azure PowerShell。
2. 連接到您的 Azure 帳戶，並列出與您的帳戶相關聯的所有訂用帳戶。
3. 選取包含要還原之已刪除資料庫的訂用帳戶。
4. 從已刪除資料庫的清單中找出資料庫及其刪除日期。

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

5. 取得特定已刪除的資料庫，並開始進行還原。

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

請注意，如果您的伺服器是 foo.database.windows.net，請使用 "foo" 作為上述 Powershell Cmdlet 中的 -ServerName。

### REST API

使用 REST 可以程式設計方式執行資料庫還原。

1.  列出所有可還原的已刪除資料庫使用 [清單可還原的已卸除資料庫 []][] 作業。
2.  取得您想要使用還原已刪除的資料庫的詳細資訊 [取得可還原的已卸除資料庫 []][] 作業。
3.  開始使用還原 [建立資料庫還原要求 []][] 作業。
4.  使用追蹤還原狀態 [資料庫作業狀態]][] 作業。

還原完成之後，您可以設定復原的資料庫可供遵循 [完成復原的資料庫 []][] 指南。


## 後續步驟

若要深入了解其他 Azure SQL Database 版本的業務持續性功能，請閱讀 [Azure SQL Database 商務持續性概觀 []][]。











[azure sql database business continuity overview]: sql-database/sql-database-business-continuity.md 
[finalize a recovered database]: sql-database/sql-database-recovered-finalize.md 
[create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx 
[database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx 
[get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx 
[list restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx 
[start-azuresqldatabaserestore]: https://msdn.microsoft.com/library/dn720218.aspx 

