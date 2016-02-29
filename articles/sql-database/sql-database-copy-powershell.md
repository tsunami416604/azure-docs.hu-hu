<properties 
    pageTitle="使用 PowerShell 建立 Azure SQL Database 的複本" 
    description="使用 PowerShell 建立 Azure SQL Database 的複本" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="12/01/2015"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# 使用 PowerShell 建立 Azure SQL Database 的複本

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)



下列步驟說明如何利用 PowerShell 複製 SQL Database。 資料庫複製作業會將 SQL 資料庫複製到新的資料庫使用 [Start-azuresqldatabasecopy](https://msdn.microsoft.com/library/dn720220.aspx) 指令程式。 副本是您在同一部伺服器或不同伺服器上建立的資料庫快照備份。

> [AZURE.NOTE] Azure SQL Database 會自動建立，並維護每個使用者資料庫，您可以還原的備份。 如需詳細資訊，請參閱 [業務持續性概觀](sql-database-business-continuity.md)。

複製程序完成時，新的資料庫是功能完整的資料庫，獨立於來源資料庫。 複製完成時，新資料庫與來源資料庫在交易上一致。 資料庫副本與來源資料庫的服務層和效能層級 (定價層) 相同。 複製完成之後，副本會變成功能完整的獨立資料庫。 可以個別管理登入、使用者和權限。


當您將資料庫複製到相同的邏輯伺服器時，可以在這兩個資料庫上使用相同的登入。 您用來複製資料庫的安全性主體會變成新資料庫的資料庫擁有者 (DBO)。 所有資料庫使用者、其權限及其安全性識別碼 (SID) 都會複製到資料庫副本。


若要完成本文，您需要下列項目：

- Azure 訂用帳戶。 如果您需要 Azure 訂用帳戶，可以按一下 **免費試用版** 頂端的這個頁面上，然後再回來完成這篇文章。
- Azure SQL Database。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟: [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
- Azure PowerShell。 您可以下載並安裝 Azure PowerShell 模組執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。



## 設定您的認證並選取您的訂用帳戶

您必須先建立 Azure 帳戶的存取權，因此請啟動 PowerShell 並執行下列 Cmdlet。 在登入畫面中，請輸入與登入 Azure 傳統入口網站相同的電子郵件和密碼。

    Add-AzureAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取的訂閱中，您需要訂用帳戶識別碼或訂用帳戶名稱 (**-SubscriptionName**)。 您可以從上一個步驟中，從顯示的資訊複製訂用帳戶 Id，或者如果您有多個訂閱，而且需要更多詳細資料也可以執行 **Get-azuresubscription** 指令程式並複製結果集中所需的訂閱資訊。 當您的訂用帳戶執行了以下 Cmdlet 之後：

    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

成功執行之後， **Select-azuresubscription** 您會回到 PowerShell 提示。 如果您有一個以上的訂閱可以執行 **Get-azuresubscription** ，並確認您想要使用顯示的訂閱 **IsCurrent: True**。


## 為您的特定環境設定變數

在下列幾個變數中，您要將範例值取代為您的資料庫和伺服器的特定值。

以您的環境的值取代預留位置值：

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





## 將 SQL Database 複製到相同伺服器

這個命令會將複製資料庫要求提交給服務。 視資料庫大小而定，複製作業可能需要一些時間才能完成。

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

## 將 SQL Database 複製到不同伺服器

這個命令會將複製資料庫要求提交給服務。 視資料庫大小而定，複製作業可能需要一些時間才能完成。

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## 監視複製作業的進度

執行之後 **Start-azuresqldatabasecopy** 您可以檢查複製要求的狀態。 之後立即執行此要求會通常傳回 **狀態: 擱置** 或 **狀態: 執行** 讓您可以執行這個項目多次，直到您看到 **狀態: 完成** 輸出中。 


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName


## 複製 SQL Database PowerShell 指令碼

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## 後續步驟

- [連接 SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)
- [將資料庫匯出至 BACPAC](sql-database-export-powershell.md)


## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

