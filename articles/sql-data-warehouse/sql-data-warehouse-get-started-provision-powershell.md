<properties
   pageTitle="使用 Powershell 建立 SQL 資料倉儲 | Microsoft Azure"
   description="使用 Powershell 建立 SQL 資料倉儲"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="lodipalm"/>

# 使用 Powershell 建立 SQL 資料倉儲

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

> [AZURE.NOTE]  若要使用 Microsoft Azure Powershell 與 SQL 資料倉儲，您將需要版本 0.9.4 或更高。  您可以在 Powershell 中執行 (Get-Module Azure).Version 來檢查您的版本。

## 取得和執行 Azure PowerShell Cmdlet
如果您尚未安裝 PowerShell，您可以：

1. 若要下載 Azure PowerShell 模組，請執行 [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)。
2. 若要執行模組，在開始視窗中鍵入 **Microsoft Azure PowerShell**。
3. 如果您尚未將帳戶加入電腦，請執行下列 Cmdlet。 (如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell][]):

            Add-AzureAccount

4. 您也必須在 ARM 模式中執行 PowerShell。  您可以藉由執行下列命令切換到此模式：

            switch-azuremode AzureResourceManager

## 建立 SQL 資料倉儲
只有在您確定 Powershell 已成功安裝在您的帳戶之後，您才可以執行下列動作來部署新的 SQL 資料倉儲：

        New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"

這個 Cmdlet 的必要參數如下所示：

 + **RequestedServiceObjectiveName**: 要求的 DWU 數量、 在表單中 「 DWXXX 」 目前支援的值為: 100、 200、 300、 400、 500，600、 1000，1200年，1500年，2000年。
 + **DatabaseName**: 您所建立的 SQL 資料倉儲的名稱。
 + **ServerName**: 用來建立伺服器的名稱 (必須是 V12)。
 + **ResourceGroupName**: 您正在使用的資源群組。  若要尋找訂用帳戶中可用的資源，請使用 Get-AzureResource。
 + **Edition**: 您必須設定 「 資料倉儲 」 來建立 SQL 資料倉儲的版本。 

## 後續步驟
SQL 資料倉儲可以 [載入範例資料] [] 或簽出如何 [開發] []、 [載入] [] 中，佈建完成之後或 [移轉] []。

如果您感興趣需如何以程式設計方式管理 SQL 資料倉儲的詳細資訊，請查看我們 [Powershell] [] 或 [REST API] [] 文件。



<!--Image references-->

<!--Article references-->
[migrate]:https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-overview-migrate/
[develop]:https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-overview-develop/
[load]:https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-overview-load/
[load sample data]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Powershell]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-reference-powershell-cmdlets/
[REST API]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]:https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]:https://azure.microsoft.com/en-us/documentation/articles/sql-database-configure-firewall-settings/
[How to install and configure Azure PowerShell]: powershell-install-configure.md

