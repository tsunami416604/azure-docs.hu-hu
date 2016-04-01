<properties
    pageTitle="SQL Server 虛擬機器的自動備份 | Microsoft Azure"
    description="針對在 Azure 虛擬機器中，以資源管理員部署模型來執行的 SQL Server，說明自動備份功能。"
    services="virtual-machines"
    documentationCenter="na"
    authors="rothja"
    manager="jeffreyg"
    editor="monicar"
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="11/12/2015"
    ms.author="jroth" />

# Azure 虛擬機器中的 SQL Server 自動備份

自動的備份會自動設定 [Managed Backup to Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) 執行 SQL Server 2014 Standard 或 Enterprise 的 Azure VM 上的所有現有和新資料庫。 這可讓您設定採用持久性 Azure Blob 儲存體的一般資料庫備份。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。

## 自動備份設定

下表說明可以為自動備份設定的選項。 實際的設定步驟會依據您是使用 Azure 入口網站或 Azure Windows PowerShell 命令而有所不同。

|設定|範圍 (預設值)|說明|
|---|---|---|
|**自動備份**|啟用/停用 (已停用)|針對執行 SQL Server 2014 Standard 或 Enterprise 的 Azure VM，啟用或停用自動備份。|
|**保留期限**|1-30 天 (30 天)|保留備份的天數。|
|**儲存體帳戶**|Azure 儲存體帳戶 (針對指定 VM 建立的儲存體帳戶)|將自動備份檔案儲存在 Blob 儲存體中時，所使用的 Azure 儲存體帳戶。 這個位置會建立一個容器來儲存所有備份檔案。 備份檔案命名慣例包括日期、時間和電腦名稱。|
|**加密**|啟用/停用 (已停用)|啟用或停用加密。 啟用加密時，用來還原備份的憑證會放在與使用相同命名慣例之相同 automaticbackup 容器中的指定儲存體帳戶裡。 如果密碼變更，就會以該密碼產生新的憑證，但是舊的憑證還是會保留，以還原先前的備份。|
|**密碼**|密碼文字 (無)|加密金鑰的密碼。 唯有啟用加密時，才需要此密碼。 若要還原加密的備份，您必須要有建立備份時所使用的正確密碼和相關憑證。|

## 在 Azure 入口網站中設定自動備份

在建立新的 SQL Server 2014 虛擬機器時，您可以透過 Azure 入口網站設定自動備份。 

>[AZURE.NOTE] 自動的備份依賴 SQL Server IaaS 代理程式。 若要安裝並設定該代理程式，您必須要有在目標虛擬機器上執行的 Azure VM 代理程式。 較新的虛擬機器資源庫映像預設會啟用此選項，但現有的 VM 上可能會遺失了 Azure VM 代理程式。 如果您正在使用自己的 VM 映像，也需要安裝 SQL Server IaaS 代理程式。 如需詳細資訊，請參閱 [VM 代理程式和擴充功能](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)。

下列 Azure 入口網站螢幕擷取畫面顯示這些選項下的 **選擇性組態** | **SQL 自動化備份**。

![Azure 入口網站中的 SQL 自動備份設定](./media/virtual-machines-sql-server-automated-backup/IC778483.jpg)

現有的 SQL Server 2014 虛擬機器，請選取 **自動備份** 中的設定 **組態** 虛擬機器屬性的區段。 在 **自動備份** ] 視窗中，您可以啟用功能、 設定保留期限、 選取儲存體帳戶，以及設定加密。 如下列螢幕擷取畫面所示。

![Azure 入口網站中的自動備份設定](./media/virtual-machines-sql-server-automated-backup/IC792133.jpg)

>[AZURE.NOTE] 當您第一次啟用自動備份時，則 Azure 會在背景中設定 SQL Server IaaS 代理程式。 在此期間，Azure 入口網站不會顯示已設定自動備份。 請等候幾分鐘的時間來安裝及設定代理程式。 之後，Azure 入口網站將會反映新的設定。

## 使用 PowerShell 設定自動備份

在下列 PowerShell 範例中，是針對現有的 SQL Server 2014 VM 設定自動備份。  **New-azurevmsqlserverautobackupconfig** 命令會設定自動備份設定，來儲存備份 $storageaccount 變數所指定的 Azure 儲存體帳戶中。 這些備份將會保留 10 天。  **Set-azurevmsqlserverextension** 命令會使用這些設定更新指定的 Azure VM。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

可能需要幾分鐘的時間來安裝及設定 SQL Server IaaS 代理程式。

若要啟用加密，請修改先前的指令碼，為 CertificatePassword 參數傳遞 EnableEncryption 參數和密碼 (安全字串)。 下列指令碼會啟用上述範例中的自動備份設定，並新增加密。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

若要停用自動備份，請執行相同的指令碼，而無須 **-啟用** 參數 **New-azurevmsqlserverautobackupconfig**。 和安裝一樣，可能需要幾分鐘的時間來停用自動備份。

## 停用及解除安裝 SQL Server IaaS 代理程式

如果您想要停用自動備份和修補作業的 SQL Server IaaS 代理程式，請使用下列命令：

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

若要解除安裝 SQL Server IaaS 代理程式，請使用下列語法：

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

您也可以解除安裝延伸模組使用 **Remove-azurevmsqlserverextension** 命令 ︰

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

>[AZURE.NOTE] 停用及解除安裝 SQL Server IaaS 代理程式不會移除先前設定的受管理備份設定。 在停用或解除安裝 SQL Server IaaS 代理程式之前，應先停用自動備份。

## 相容性

下列產品與自動備份的 SQL Server IaaS 代理程式功能相容：

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2014 Standard

- SQL Server 2014 Enterprise

## 後續步驟

自動備份會在 Azure VM 上設定受管理備份。 因此請務必 [檢閱受管理備份的文件](https://msdn.microsoft.com/library/dn449496.aspx) 來了解的行為與隱含意義。

您可以尋找其他備份和還原 Azure Vm 上的 SQL Server 的下列主題中的指引 ︰ [備份和還原 SQL Server Azure 虛擬機器中的](virtual-machines-sql-server-backup-and-restore.md)。

在 Azure 中的 SQL Server Vm 的相關的功能是 [在 Azure 虛擬機器的 SQL Server 自動修補](virtual-machines-sql-server-automated-patching.md)。

檢閱其他 [的 Azure 虛擬機器中執行 SQL Server 資源](virtual-machines-sql-server-infrastructure-services.md)。


