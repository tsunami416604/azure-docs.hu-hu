<properties
    pageTitle="在 VM 中的 SQL Server 自動化修補 | Microsoft Azure"
    description="針對在 Azure 中執行的 SQL Server 虛擬機器，說明自動修補功能。"
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

# Azure 虛擬機器中的 SQL Server 自動修補

自動修補會針對執行 SQL Server 2012 或 2014 的 Azure 虛擬機器建立維護時間範圍。 自動更新只能在此維護時間範圍內安裝。 對於 SQL Server，這可以確保系統更新和任何相關聯的重新啟動會在對資料庫最好的時間發生。 這取決於 SQL Server IaaS 代理程式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。

## 在 Azure 入口網站中設定自動修補

您可以使用 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409) 來設定自動修補時建立新的 SQL Server 虛擬機器。 

>[AZURE.NOTE] 自動的修補相依於 SQL Server IaaS 代理程式。 若要安裝並設定該代理程式，您必須要有在目標虛擬機器上執行的 Azure VM 代理程式。 較新的虛擬機器資源庫映像預設會啟用此選項，但現有的 VM 上可能會遺失了 Azure VM 代理程式。 如果您正在使用自己的 VM 映像，也需要安裝 SQL Server IaaS 代理程式。 如需詳細資訊，請參閱 [VM 代理程式和擴充功能](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)。

下列 Azure 入口網站螢幕擷取畫面顯示這些選項下的 **選擇性組態** | **SQL 自動化修補**。

![Azure 入口網站中的 SQL 自動修補](./media/virtual-machines-sql-server-automated-patching/IC778484.jpg)

現有的 SQL Server 2012 或 2014年虛擬機器，請選取 **自動修補功能** 中的設定 **組態** 虛擬機器屬性的區段。 在 **自動化修補** ] 視窗中，您可以啟用功能、 設定維護排程和啟動時間，並選擇維護時段持續時間。 如下列螢幕擷取畫面所示。

![Azure 入口網站中的自動修補設定](./media/virtual-machines-sql-server-automated-patching/IC792132.jpg)

>[AZURE.NOTE] 當您啟用自動修補的第一次時，Azure 會在背景中設定 SQL Server IaaS 代理程式。 在此期間，Azure 入口網站不會顯示已設定自動修補。 請等候幾分鐘的時間來安裝及設定代理程式。 之後，Azure 入口網站將會反映新的設定。

## 使用 PowerShell 設定自動修補

您也可以使用 PowerShell 來設定自動修補。

在下列範例中，會使用 PowerShell 在現有的 SQL Server VM 上設定自動修補。  **New-azurevmsqlserverautopatchingconfig** 命令會設定為自動更新的新維護視窗。

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

下表會根據此範例來描述對目標 Azure VM 的實際效果：

|參數|效果|
|---|---|
|**DayOfWeek**|在每個星期四安裝修補程式。|
|**MaintenanceWindowStartingHour**|在上午 11:00 開始更新。|
|**MaintenanceWindowsDuration**|必須在 120 分鐘內安裝修補程式。 根據開始時間，其必須在下午 1:00 之前完成。|
|**PatchCategory**|此參數唯一可能的設定是 "Important"。|

可能需要幾分鐘的時間來安裝及設定 SQL Server IaaS 代理程式。

若要停用自動修補，請執行相同的指令碼，但不要對 New-AzureVMSqlServerAutoPatchingConfig 使用 -Enable 參數。 和安裝一樣，可能需要幾分鐘的時間來停用自動修補。

## 停用及解除安裝 SQL Server IaaS 代理程式

如果您想要停用自動備份和修補作業的 SQL Server IaaS 代理程式，請使用下列命令：

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

若要解除安裝 SQL Server IaaS 代理程式，請使用下列語法：

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

您也可以解除安裝延伸模組使用 **Remove-azurevmsqlserverextension** 命令:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

## 相容性

下列產品與自動修補的 SQL Server IaaS 代理程式功能相容：

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2012

- SQL Server 2014

## 後續步驟

在 Azure 中的 SQL Server Vm 的相關的功能是 [Azure 虛擬機器中 SQL Server 的自動化備份](virtual-machines-sql-server-automated-backup.md)。

檢閱其他 [的 Azure 虛擬機器中執行 SQL Server 資源](virtual-machines-sql-server-infrastructure-services.md)。

