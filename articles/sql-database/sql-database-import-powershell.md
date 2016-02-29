<properties 
    pageTitle="使用 PowerShell 匯入 BACPAC 檔案以建立新的 Azure SQL Database" 
    description="使用 PowerShell 匯入 BACPAC 檔案以建立新的 Azure SQL Database" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="sstein"/>

# 使用 PowerShell 匯入 BACPAC 檔案以建立新的 Azure SQL Database

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)


本文提供使用 PowerShell 匯入 BACPAC 以建立 Azure SQL Database 的說明。

BACPAC 是一種包含資料庫結構描述和資料的 .bacpac 檔案。 如需詳細資訊，請參閱備份封裝 (.bacpac) 中 [資料層應用程式](https://msdn.microsoft.com/library/ee210546.aspx)。

資料庫是透過由 Azure 儲存體 Blob 容器匯入的 BACPAC 來建立。 如果您沒有 Azure 儲存體中的.bacpac 檔案可以建立一個執行中的步驟 [建立和匯出 Azure SQL Database 的 BACPAC](sql-database-export-powershell.md)。

> [AZURE.NOTE] Azure SQL Database 會自動建立，並維護每個使用者資料庫，您可以還原的備份。 如需詳細資訊，請參閱 [業務持續性概觀](sql-database-business-continuity.md)。


若要匯入 SQL Database，您需要下列項目：

- Azure 訂用帳戶。 如果您需要 Azure 訂用帳戶，可以按一下 **免費試用版** 頂端的這個頁面上，然後再回來完成這篇文章。
- 您要還原之資料庫的 .bacpac 檔案 (BACPAC)。 BACPAC 必須處於 [Azure 儲存體帳戶 (傳統)](storage-create-storage-account.md) blob 容器。


> [AZURE.IMPORTANT] 這份文件版本的 Azure PowerShell 命令最多包含 *但不是包括* 版本 1.0 和更新版本。 您可以檢查您的 Azure PowerShell 與版本 **Get-module azure | 格式化表格版本** 命令。



## 設定您的認證並選取您的訂用帳戶

您必須先建立 Azure 帳戶的存取權，因此請啟動 PowerShell 並執行下列 Cmdlet。 在登入畫面中，請輸入與登入 Azure 入口網站相同的電子郵件和密碼。

    Add-AzureAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID。 您可以從上一個步驟中，從顯示的資訊複製訂用帳戶 Id，或者如果您有多個訂閱，而且需要更多詳細資料也可以執行 **Get-azuresubscription** 指令程式並複製結果集中所需的訂閱資訊。 當您的訂用帳戶 ID 執行了以下 Cmdlet 之後：

    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

成功執行之後， **Select-azuresubscription** 您會回到 PowerShell 提示。 如果您有一個以上的訂閱可以執行 **Get-azuresubscription** ，並確認您所選取顯示的訂閱 **IsCurrent: True**。


## 為您的環境設定變數

在下列幾個變數中，您要將範例值取代為您的資料庫和儲存體帳戶的特定值。

伺服器名稱必須為目前存在於上一個步驟所選取之訂用帳戶中的伺服器，而且是您要在其中建立資料庫的目的地伺服器。

資料庫名稱是您想要為新資料庫命名的名稱。

    $ServerName = "servername"
    $DatabaseName = "databasename"


下列變數是來自您 BACPAC 所在的儲存體帳戶。 在 [Azure 入口網站](https://portal.azure.com) 瀏覽至您的儲存體帳戶，以取得這些值。 尋找主要存取金鑰，可以按一下 **所有設定** 然後 **金鑰** 從儲存體帳戶的刀鋒視窗。

Blob 名稱是您想要用來建立資料庫之現有的 .bacpac 檔案名稱。 您需要包含 .bacpac 副檔名。

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"

## 建立您的伺服器與儲存體帳戶的指標

執行 **Get-credential** 指令程式會開啟一個視窗詢問您的使用者名稱和密碼。 請輸入您想要在其中建立資料庫的 SQL Server 系統管理員登入和密碼 (上述的 $ServerName) ，而不是您的 Azure 帳戶使用者名稱和密碼。

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## 匯入資料庫

這個命令會將匯入資料庫要求提交給服務。 視資料庫大小而定，匯入作業可能需要一些時間才能完成。

    $importRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## 監視作業的進度

執行之後 **開始 AzureSqlDatabaseImport** 您可以檢查要求的狀態。 

在要求之後立即檢查狀態通常會傳回狀態 **暫止**, ，或 **執行** 會提供完成目前的百分比，因此您可以執行這個項目多次，直到您看到 **狀態: 完成** 輸出中。 

執行此命令時，會提示您輸入密碼。 請輸入您的 SQL Server 系統管理員登入和密碼。


    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
 


## SQL Database PowerShell 還原指令碼


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $ImportRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    

## 後續步驟

- [連接 SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)




## 其他資源

- [業務續航力概觀](sql-database-business-continuity.md)
- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

