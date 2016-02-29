<properties 
    pageTitle="使用 PowerShell 為 Azure SQL Database 設定異地複寫 | Microsoft Azure" 
    description="使用 PowerShell 為 Azure SQL Database 進行異地複寫" 
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

# 使用 PowerShell 為 Azure SQL Database 設定異地複寫



> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


本文說明如何使用 PowerShell 為 Azure SQL Database 設定異地複寫。

異地複寫可讓您在不同的資料中心位置 (區域) 最多建立 4 個複本 (次要) 資料庫。 在資料中心中斷或在無法連線至主要資料庫的情況下，便可使用次要資料庫。

異地複寫僅適用於 Standard 和 Premium 資料庫。 

Standard 資料庫可以有一個不可讀取次要複本，並且必須使用建議的區域。 Premium 資料庫最多可以有四個位於任何可用區域並且可讀取次要複本。

若要設定異地複寫，您需要下列項目：

- Azure 訂用帳戶。 如果您需要 Azure 訂用帳戶，可以按一下 **免費試用版** 頂端的這個頁面上，然後再回來完成這篇文章。
- Azure SQL Database - 您想要複寫到不同地理區域的主要資料庫。
- Azure PowerShell 1.0 或更新版本。 您可以下載並安裝 Azure PowerShell 模組 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。




## 設定您的認證並選取您的訂用帳戶

您必須先建立 Azure 帳戶的存取權，因此請啟動 PowerShell 並執行下列 Cmdlet。 在登入畫面中，請輸入與登入 Azure 入口網站相同的電子郵件和密碼。


    Login-AzureRmAccount

成功登入後，您將會在畫面中看到一些資訊，包括用於登入的 ID 與可以存取的 Azure 訂用帳戶。


### 選取您的 Azure 訂用帳戶

若要選取所需的訂用帳戶，您必須提供訂用帳戶 ID。 您可以從上一個步驟中，從顯示的資訊複製訂用帳戶 Id，或者如果您有多個訂閱，而且需要更多詳細資料也可以執行 **Get AzureRmSubscription** 指令程式並複製結果集中所需的訂閱資訊。 下列 Cmdlet 使用訂用帳戶 ID 來設定目前的訂用帳戶：

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

成功執行之後， **選取 AzureRmSubscription** 您會回到 PowerShell 提示。



## 加入次要資料庫


下列步驟會在異地複寫合作關係中建立新的次要資料庫。  
  
若要啟用次要複本，您必須是訂用帳戶擁有者或共同擁有者。 

您可以使用 **新增 AzureRmSqlDatabaseSecondary** 指令程式可新增夥伴伺服器上的次要資料庫，您會在伺服器上的本機資料庫連接 (主要資料庫)。 

此指令程式取代 **Start-azuresqldatabasecopy** 與 **– IsContinuous** 參數。  它會輸出 **AzureRmSqlDatabaseSecondary** 可以使用其他指令程式，以清楚地識別特定的複寫連結的物件。 建立次要資料庫並將其完全植入時，這個 Cmdlet 會傳回。 視資料庫的大小而定，可能需要從數分鐘到數小時的時間。

次要伺服器上的複寫資料庫會具備與主要伺服器上的資料庫相同的名稱，並且預設具有相同的服務層級。 次要資料庫可以是可讀取或不可讀取，並且可以是單一資料庫或彈性資料庫。 如需詳細資訊，請參閱 [新增 AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) 和 [服務層](sql-database-service-tiers.md)。
建立並植入次要複本之後，就會開始從主要資料庫將資料複寫到新的次要資料庫。 下列步驟說明如何使用 PowerShell 完成這項工作，以使用單一資料庫或彈性資料庫來建立不可讀取和可讀取次要複本。

如果夥伴資料庫已經存在 (例如，因為終止先前的異地複寫關聯性的緣故)，命令將會失敗。



### 加入不可讀取次要複本 (單一資料庫)

下列命令會在資源群組 "rg2" 伺服器 "srv2" 中建立資料庫 "mydb" 的不可讀取次要複本：

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "None"



### 加入可讀取次要複本 (單一資料庫)

下列命令會在資源群組 "rg2" 伺服器 "srv2" 中建立資料庫 "mydb" 的可讀取次要複本：

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### 加入不可讀取次要複本 (彈性資料庫)

下列命令會在資源群組 "rg2" 伺服器 "srv2" 中名為 "ElasticPool1" 的彈性資料庫集區建立資料庫 "mydb" 的不可讀取次要複本：

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "None"


### 加入可讀取次要複本 (彈性資料庫)

下列命令會在資源群組 "rg2" 伺服器 "srv2" 中名為 "ElasticPool1" 的彈性資料庫集區建立資料庫 "mydb" 的可讀取次要複本：

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## 移除次要資料庫

使用 **移除 AzureRmSqlDatabaseSecondary** 永久終止主要與次要資料庫的複寫合作關係的 cmdlet。 關聯性終止之後，次要資料庫會成為讀寫資料庫。 如果與次要資料庫的連線中斷，命令將會成功，但次要資料庫必須等到連線恢復後才會變成可讀寫。 如需詳細資訊，請參閱 [移除 AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) 和 [服務層](sql-database-service-tiers.md)。

這個 Cmdlet 會取代用於複寫的 Stop-AzureSqlDatabaseCopy。 

這項移除作業相當於強制終止，會移除複寫連結並將先前的次要資料庫保留做為終止之前未完全複寫的獨立資料庫。 將會清除先前的主要和先前的次要資料庫上連結的所有資料 (若有)。 移除複寫連結時這個 Cmdlet 會傳回。 


為了移除次要資料庫，根據 RBAC，使用者應該具備主要和次要資料庫的寫入權限。 如需詳細資料，請參閱角色型存取控制。

以下會移除資源群組 "rg2" 的伺服器 "srv2" 名為 "mydb" 的資料庫複寫連結。 

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 




## 起始規劃的容錯移轉

使用 **組 AzureRmSqlDatabaseSecondary** 指令程式搭配 **容錯移轉** 升級成為新主要資料庫，降級現有的主要變成次要資料庫的次要資料庫的參數。 這項功能是為了規劃的容錯移轉 (例如災害復原鑽研期間) 設計，並且需要主要資料庫可供使用。

此命令會執行下列工作流程：

1. 暫時將複寫切換到同步模式。 這會導致將所有未處理的交易排清至次要資料庫。

2. 切換異地複寫關係中兩個資料庫的角色。  

這個順序可確保不會發生任何資料遺失。 切換角色時，會有一小段時間無法使用這兩個資料庫 (大約為 0 到 25 秒)。 在正常情況下，完成整個作業所需的時間應該少於一分鐘。 如需詳細資訊，請參閱 [組 AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx)。


> [AZURE.NOTE] 如果主要資料庫無法使用此命令發行時它將會失敗並出現錯誤訊息，指出沒有可用的主要伺服器。 在少數情況下，作業會無法完成並且可能會出現停滯。 在此情況下，使用者可以呼叫強制容錯移轉命令 (未規劃的容錯移轉) 並接受資料遺失。



將次要資料庫切換為主要程序完成時，這個 Cmdlet 將傳回。

下列命令會將資源群組 "rg2" 下伺服器 "srv2" 上名為 "mydb" 的資料庫角色切換為主要資料庫。 "db2" 所連線的原始主要資料庫，在兩個資料庫完全同步處理之後會切換為次要資料庫。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover



## 起始從主要資料庫到次要資料庫的未規劃的容錯移轉


您可以使用 **組 AzureRmSqlDatabaseSecondary** 指令程式搭配 **– 容錯移轉** 和 **-AllowDataLoss** 升級次要資料庫變成非計劃的方式，強制降級的現有主要變成次要，當主要資料庫已無法再使用一次新的主要資料庫的參數。

這項功能是針對還原資料庫的可用性非常重要而且部分資料遺失是可接受時的災害復原所設計。 叫用強制容錯移轉時，指定的次要資料庫立即成為主要資料庫，並開始接受寫入交易。 在強制容錯移轉作業後，原始主要資料庫能夠與這個新的主要資料庫重新連線時，會在原始主要資料庫執行增量備份，而舊的主要資料庫會變成新主要資料庫的次要資料庫；之後就只是新的主要資料庫的複本。

但因為還原時間點在次要資料庫不受支援，如果您想要復原已認可到舊主要資料庫但尚未被複寫到新主要資料庫的資料，您應該接洽 CSS 來將資料庫還原至已知的記錄備份。

> [AZURE.NOTE] 如果當主要和次要都已上線，舊主要會發出此命令會變成新的次要資料庫，但資料同步處理不會嘗試讓某些可能會發生資料遺失。


如果主要資料庫中有多個次要複本，命令將只有部分成功。 執行命令的次要複本會變成主要複本。 不過舊的主要複本將仍為主要複本，亦即，兩個主要複本最終會處於不一致狀態並透過擱置的複寫連結連接。 使用者必須在主要資料庫上使用「移除次要複本」API 手動修復此組態。


下列命令在主要複本無法使用時，將名為 "mydb" 的資料庫角色切換為主要複本。 "mydb" 所連線的原始主要複本，將在回到線上之後切換為次要複本。 在該時間點，同步處理可能會導致資料遺失。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss



## 監視異地複寫組態和健康狀態

監視工作包括異地複寫組態的監視和監視資料複寫健康狀態。  

[Get AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) 可用來擷取 sys.geo_replication_links 目錄檢視中顯示的正向的複寫連結的相關資訊。

下列命令會擷取主要資料庫 "mydb" 與資源群組 "rg2" 上伺服器 "srv2" 上的次要複本之間複寫連結的狀態。

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb”
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”



   

## 後續步驟

- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)




## 其他資源

- [新異地複寫功能要點](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication)
- [使用異地複寫設計業務持續性的雲端應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

