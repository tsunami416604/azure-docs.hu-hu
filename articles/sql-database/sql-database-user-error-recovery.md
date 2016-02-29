<properties 
   pageTitle="SQL Database 使用者錯誤復原" 
   description="了解如何使用 Azure SQL Database 的時間點還原 (PITR) 功能，從使用者錯誤、意外的資料損毀或已刪除的資料庫復原。" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="11/09/2015"
   ms.author="elfish"/>

# 從使用者錯誤復原 Azure SQL Database

Azure SQL Database 提供兩個核心功能，從使用者錯誤或非預期的資料修改復原。

- 時間點還原 
- 還原已刪除的資料庫

您可以進一步了解這些功能在此 [部落格文章](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)。

Azure SQL Database 一律會還原至新的資料庫。 所有「基本」、「標準」和「高階」資料庫都提供這些還原功能。

##時間點還原
發生使用者錯誤或非預期的資料修改時，時間點還原可用來將資料庫還原至資料庫保留期限內的任何時間點。 

「基本」資料庫有 7 天的保留期，「標準」資料庫有 14 天的「保留期」，「高階」資料庫則有 35 天的保留期。 若要深入了解資料庫保留，請參閱 [業務持續性概觀](sql-database-business-continuity.md)。

> [AZURE.NOTE] 還原資料庫，建立新的資料庫。 請務必確定您要還原到的伺服器有足夠的 DTU 容量供新的資料庫使用。 您可以要求增加此配額的 [連絡支援人員](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)。

###Azure 入口網站
若要在 Azure 入口網站中使用「還原時間點」，請按照下列步驟執行。

1. 登入 [Azure 入口網站](https://portal.Azure.com)
2. 在畫面左側選取 **瀏覽** ，然後選取 **SQL 資料庫**。
3. 巡覽至您的資料庫，然後加以選取。
4. 在您的資料庫刀鋒視窗頂端，選取 **還原**。
5. 指定資料庫名稱、 時間點，然後按一下 **建立**。
6. 資料庫還原程序就會開始，您可以使用監視 **通知** 螢幕的左邊。

###PowerShell
使用 PowerShell 可以程式設計方式執行時間點還原與 [Start-azuresqldatabaserestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396) 指令程式。 如需詳細的逐步執行，請 [觀看影片，此程序的](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell/)。

> [AZURE.IMPORTANT] 這份文件版本的 Azure PowerShell 命令最多包含 *但不是包括* 版本 1.0 和更新版本。 您可以檢查您的 Azure PowerShell 與版本 **Get-module azure | 格式化表格版本** 命令。

        $Database = Get-AzureSqlDatabase -ServerName "YourServerName" –DatabaseName “YourDatabaseName”
        $RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –PointInTime “2015-01-01 06:00:00”
        Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
         

###REST API 
使用 REST 可以程式設計方式執行資料庫還原。

1. 取得您想要使用還原的資料庫 [取得資料庫](http://msdn.microsoft.com/library/azure/dn505708.aspx) 作業。

2.  建立還原要求使用 [建立資料庫還原要求](http://msdn.microsoft.com/library/azure/dn509571.aspx) 作業。
    
3.  追蹤還原要求使用 [資料庫操作狀態](http://msdn.microsoft.com/library/azure/dn720371.aspx) 作業。

##還原已刪除的資料庫
若資料庫被刪除，Azure SQL Database 可讓您將已刪除的資料庫還原至刪除的時間點。 Azure SQL Database 會將已刪除的資料庫備份，儲存一段資料庫保留期間的時間。

已刪除資料庫的保留期間取決於資料庫所在的服務層，或是資料庫存在的天數，兩者中較少的天數。 若要深入了解資料庫保留，請閱讀我們 [業務持續性概觀](sql-database-business-continuity.md)。

> [AZURE.NOTE] 還原資料庫，建立新的資料庫。 請務必確定您要還原到的伺服器有足夠的 DTU 容量供新的資料庫使用。 您可以要求增加此配額的 [連絡支援人員](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)。

###Azure 入口網站
若要使用 Azure 入口網站來還原已刪除的資料庫，請按照下列步驟執行。

1. 登入 [Azure 入口網站](https://portal.Azure.com)
2. 在畫面左側選取 **瀏覽** ，然後選取 **SQL 伺服器**。
3. 瀏覽至您的伺服器，然後選取它。
4. 在 **作業** 在伺服器刀鋒視窗中，選取 **已刪除的資料庫**。
5. 按一下您想要還原的已刪除資料庫。
6. 指定資料庫名稱，然後按一下 **建立**。
7. 資料庫還原程序就會開始，您可以使用監視 **通知** 螢幕的左邊。

###PowerShell
若要還原已刪除的資料庫使用 PowerShell，使用 [Start-azuresqldatabaserestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396) 指令程式。  如需詳細的逐步執行，請 [觀看此程序](http://azure.microsoft.com/documentation/videos/restore-a-deleted-sql-database-with-microsoft-azure-powershell/)。

1. 從已刪除資料庫的清單中找出已刪除的資料庫及其刪除日期。
        
        Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName"

2. 取得特定已刪除的資料庫，並開始進行還原。

        $Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName" –DatabaseName “YourDatabaseName” -DeletionDate "1/01/2015 12:00:00 AM""
        $RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “NewDatabaseName”
        Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
         

###REST API 
使用 REST 可以程式設計方式執行資料庫還原。

1.  列出所有可還原的已刪除資料庫使用 [列出可還原的已卸除資料庫](http://msdn.microsoft.com/library/azure/dn509562.aspx) 作業。
    
2.  取得您想要使用還原已刪除的資料庫的詳細資訊 [取得可還原的已卸除資料庫](http://msdn.microsoft.com/library/azure/dn509574.aspx) 作業。

3.  開始使用還原 [建立資料庫還原要求](http://msdn.microsoft.com/library/azure/dn509571.aspx) 作業。
    
4.  使用追蹤還原狀態 [資料庫操作狀態](http://msdn.microsoft.com/library/azure/dn720371.aspx) 作業。

