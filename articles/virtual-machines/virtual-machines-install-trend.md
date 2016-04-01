<properties
    pageTitle="在 VM 上安裝 Trend Micro Deep Security | Microsoft Azure"
    description="本文說明如何在 Azure 中，在以傳統部署模型建立的 VM 上安裝和設定 Trend Micro 安全性。"
    services="virtual-machines"
    documentationCenter=""
    authors="dsk-2015"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2015"
    ms.author="dkshir"/>


# 如何在 Windows VM 上安裝和設定 Trend Micro Deep Security as a Service

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


本文說明如何在執行 Windows Server 的新或現有虛擬機器 (VM) 上，安裝和設定 Trend Micro Deep Security as a Service。 Deep Security as a Service 包括反惡意程式碼防護、防火牆、入侵防禦系統及完整監視。

透過 VM 代理程式，用戶端會安裝為安全性延伸模組。 在新的虛擬機器上，您將安裝 VM 代理程式與 Deep Security 代理程式。 在沒有 VM 代理程式的現有虛擬機器上，您必須先下載與安裝 VM 代理程式。 本文將探討這兩種狀況。

如果您已有 Trend Micro 的內部部署解決方案現有訂用帳戶，您可以用它來協助保護 Azure 虛擬機器的安全。 如果您還不是 Trend 的客戶，您可以註冊試用訂閱。 如需此解決方案的詳細資訊，請參閱 Trend Micro 部落格文章 [Microsoft Azure VM 代理程式延伸模組適用於 Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945)。

## 在新的虛擬機器上安裝 Deep Security 代理程式

 [Azure 傳統入口網站](http://manage.windowsazure.com) 可讓您安裝 VM 代理程式和 Trend Micro 安全性延伸模組，當您使用 **從組件庫** 選項來建立虛擬機器。 如果您打算建立單一虛擬機器，使用此方法可輕易地新增 Trend Micro 的防護。

這 **從組件庫** 選項會開啟可協助您設定虛擬機器的精靈。 您可以使用精靈的最後一個頁面，來安裝 VM 代理程式和 Trend Micro 安全性延伸模組。 如需一般指示，請參閱 [建立在 Azure 傳統入口網站中執行 Windows 的虛擬機器](virtual-machines-windows-tutorial-classic-portal.md)。 當您進入精靈的最後一個頁面時，請執行下列動作：

1.  在 **VM 代理程式**, ，檢查 **安裝 VM 代理程式**。

2.  在 **安全性延伸模組**, ，檢查 **Trend Micro Deep Security Agent**。

    ![Install the VM Agent and the Deep Security Agent](./media/virtual-machines-install-trend/InstallVMAgentandTrend.png)

3.  按一下核取記號以建立虛擬機器。

## 在現有虛擬機器上安裝 Deep Security 代理程式

若要執行此作業，您將需要下列項目：

- 在本機電腦上安裝 Azure PowerShell 模組 0.8.2 版或更新版本。 您可以檢查您已安裝所使用的 Azure PowerShell 版本 **Get-module azure | 格式化表格版本** 命令。 如需指示和最新版本的連結，請參閱 [如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)。

- 在目標虛擬機器上安裝 VM 代理程式。

首先，確認已安裝 VM 代理程式。 填寫雲端服務名稱和虛擬機器名稱，然後在系統管理員層級 Azure PowerShell 命令提示字元上執行下列命令。 取代引號中，包括裡面 < 和 > 字元。

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

如果您不知道雲端服務和虛擬機器名稱，執行 **Get-azurevm** 以顯示目前訂閱中的所有虛擬機器的該項資訊。

如果 **寫入主機** 命令會傳回 **True**, ，已安裝 VM 代理程式。 如果它傳回 **False**, ，請參閱指示和 Azure 部落格文章中的下載連結 [VM 代理程式與延伸模組-第 2 部分](http://go.microsoft.com/fwlink/p/?LinkId=403947)。

如果已安裝 VM 代理程式，請執行這些命令。

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## 後續步驟

讓代理程式安裝並開始執行需要幾分鐘的時間。 之後，您必須在虛擬機器上啟用 Deep Security，才能由 Deep Security Manager 進行管理。 如需其他指示，請參閱：

- 此解決方案相關文章 [trend 定域機組安全性，Microsoft azure](http://go.microsoft.com/fwlink/?LinkId=404101)
- A [範例 Windows PowerShell 指令碼](http://go.microsoft.com/fwlink/?LinkId=404100) 設定虛擬機器
- [指示](http://go.microsoft.com/fwlink/?LinkId=404099)  範例

## 其他資源

[如何登入執行 Windows Server 的虛擬機器]

[Azure VM 延伸模組與功能]


<!--Link references-->
[How to log on to a virtual machine running Windows Server]: virtual-machines-log-on-windows-server.md
[Azure VM Extensions and features]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409


