<properties 
    pageTitle="使用 PowerShell 變更 Azure SQL Database 的服務層級和效能等級" 
    description="變更 Azure SQL Database 的服務層級和效能等級說明如何使用 PowerShell 相應增加或減少您的 SQL Database。 使用 PowerShell 變更 Azure SQL Database 的定價層。" 
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


# 使用 PowerShell 變更 SQL Database 的服務層級級和效能等級 (定價層)

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


本文將說明如何使用 PowerShell 變更您的 SQL Database 的服務層級和效能等級。

使用中的資訊 [SQL Database Web/Business 資料庫升級至新的服務層](sql-database-upgrade-new-service-tiers.md) 和 [Azure SQL Database 服務層和效能層級](sql-database-service-tiers.md) 來判斷適當的服務層和效能層級為您的 Azure SQL 資料庫。

> [AZURE.IMPORTANT] 變更 SQL 資料庫的服務層和效能層級是一項線上作業。 這表示您的資料庫在整個作業過程中，將維持在線上可供使用的狀態而不需停機。

- 若要將資料庫降級，資料庫應該小於目標服務層允許的大小上限。 
- 升級的資料庫時 [標準異地複寫](https://msdn.microsoft.com/library/azure/dn758204.aspx) 或 [作用中地理複寫](https://msdn.microsoft.com/library/azure/dn741339.aspx) 啟用，您必須先升級次要資料庫所需的效能層然後再升級主要資料庫。
- 從高階服務層降級時，您必須先終止所有的「異地複寫」關聯性。 您可以依照所述的步驟 [終止連續複製關聯性](https://msdn.microsoft.com/library/azure/dn741323.aspx) 停止主要資料庫與作用中次要資料庫之間的複寫程序主題。
- 還原服務會針對各種服務層提供不同的選項。 降級後您可能會無法還原至某個時間點，或具有較短的備份保留期限。 如需詳細資訊，請參閱 [Azure SQL Database 備份和還原](https://msdn.microsoft.com/library/azure/jj650016.aspx)。
- 您可以在 24 小時期間內，變更最多四個個別的資料庫 (服務層或效能等級)。
- 完成變更之前，不會將新屬性套用至資料庫。



**若要完成本文，您需要下列項目：**

- Azure 訂閱。 如果您需要 Azure 訂用帳戶，可以按一下 **免費試用版** 頂端的這個頁面上，然後再回來完成這篇文章。
- Azure SQL Database。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟 ︰ [建立您的第一個 Azure SQL Database](sql-database-get-started.md)。
- Azure PowerShell。


若要執行 PowerShell Cmdlet，Azure PowerShell 必須已安裝且正在執行中。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。



## 設定您的認證並選取您的訂用帳戶

您必須先建立 Azure 帳戶的存取權，因此請啟動 PowerShell 並執行下列 Cmdlet。 在登入畫面中，請輸入與登入 Azure 入口網站相同的電子郵件和密碼。

    Add-AzureRmAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取的訂閱中，您需要訂用帳戶識別碼或訂用帳戶名稱 (**-SubscriptionName**)。 您可以從上一個步驟中，從顯示的資訊複製訂用帳戶 Id，或者如果您有多個訂閱，而且需要更多詳細資料也可以執行 **Get-azuresubscription** 指令程式並複製結果集中所需的訂閱資訊。 當您的訂用帳戶執行了以下 Cmdlet 之後：

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

成功執行之後， **Select-azuresubscription** 您會回到 PowerShell 提示。 如果您有一個以上的訂閱可以執行 **Get-azuresubscription** ，並確認您想要使用顯示的訂閱 **IsCurrent: True**。


 


## 變更您的 SQL Database 的服務層級和效能等級

執行 **組 AzureRmSqlDatabase** 指令程式，並設定 **-RequestedServiceObjectiveName** 效能層級的所需的定價層，例如 *S0*, ，*S1*, ，*S2*, ，*S3*, ，*P1*, ，*P2*, ，...

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## 變更您的 SQL Database 之服務層級和效能等級的範例 PowerShell 指令碼

    

    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## 後續步驟

- [相應放大和縮小](sql-database-elastic-scale-get-started.md)
- [使用 SSMS 連接和查詢 SQL Database](sql-database-connect-query-ssms.md)
- [匯出 Azure SQL Database](sql-database-export-powershell.md)

## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure SQL Database Cmdlet](https://msdn.microsoft.com/library/azure/mt163521.aspx)

