<properties
    pageTitle="在 VM 上安裝 Symantec Endpoint Protection | Microsoft Azure"
    description="了解如何在以傳統部署模型所建立新的或現有的 Azure VM 上安裝和設定 Symantec Endpoint Protection 安全性延伸模組。"
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

# 如何在 Windows VM 上安裝和設定 Symantec Endpoint Protection

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


本文說明如何在執行 Windows Server 的新或現有虛擬機器 (VM) 上，安裝和設定 Symantec Endpoint Protection 用戶端。 這是包括服務 (例如病毒和間諜軟體防護、防火牆及入侵防禦) 的完整用戶端。

透過使用 VM 代理程式，用戶端會安裝為安全性延伸模組。 在新的虛擬機器上，您將安裝代理程式與端點用戶端。 在沒有代理程式的現有虛擬機器上，您必須先下載與安裝代理程式。 本文將探討這兩種狀況。

如果您已有 Symantec 的內部部署解決方案現有訂閱，您可以用它來保護 Azure 虛擬機器的安全。 如果您還不是 Trend 的客戶，您可以註冊試用訂閱。 如需此解決方案的詳細資訊，請參閱 [在 Microsoft Azure 平台上 Symantec Endpoint Protection] [Symantec]。 此頁面還提供授權資訊與指示，說明如何安裝用戶端，如果您已經是 Symantec 客戶的連結。

## 在新的虛擬機器上安裝 Symantec Endpoint Protection

 [Azure 傳統入口網站][Portal] 可讓您安裝 VM 代理程式和 Symantec 安全性延伸模組，當您使用 **從組件庫** 選項來建立虛擬機器。 如果您打算建立單一虛擬機器，使用此方法可輕易地新增 Symantec 的防護。

這 **從組件庫** 選項會開啟可協助您設定虛擬機器的精靈。 您可以使用精靈的最後一個頁面，來安裝 VM 代理程式和 Symantec 安全性延伸模組。

如需一般指示，請參閱 [建立虛擬機器執行 Windows Server 的][Create]。 當您進入精靈的最後一個頁面時：

1.  VM 代理程式，在 **安裝 VM 代理程式** 應已勾選。

2.  在安全性延伸模組，請檢查 **Symantec Endpoint Protection**。


    ![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.  按一下頁面底部的核取記號來建立虛擬機器。

## 在現有虛擬機器上安裝 Symantec Endpoint Protection

在開始之前，您將需要下列項目：

- 在本機電腦上安裝 Azure PowerShell 模組 0.8.2 版或更新版本。 您可以檢查與已安裝 Azure PowerShell 版本 **Get-module azure | 格式化表格版本** 命令。 如需指示和最新版本的連結，請參閱 [如何安裝和設定 Azure PowerShell][PS]。 請確定登入您的 Azure 訂用帳戶。

- 在 Azure 虛擬機器上執行的 VM 代理程式。

首先，請確認虛擬機器上已安裝 VM 代理程式。 填寫雲端服務名稱和虛擬機器名稱，然後在系統管理員層級 Azure PowerShell 命令提示字元上執行下列命令。 取代引號中，包括裡面 < 和 > 字元。

> [AZURE.TIP] 如果您不知道雲端服務和虛擬機器名稱，執行 **Get-azurevm** 列出目前訂閱中的所有虛擬機器的名稱。

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

如果 **寫入主機** 命令會顯示 **True**, ，已安裝 VM 代理程式。 如果顯示 **False**, ，請參閱指示和 Azure 部落格文章中的下載連結 [VM 代理程式與延伸模組-第 2 部分][Agent]。

如果已安裝 VM 代理程式，請執行下列命令來安裝 Symantec Endpoint Protection 代理程式。

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

若要確認 Symantec 安全性延伸模組已安裝且是最新的：

1.  登入虛擬機器。 如需指示，請參閱 [如何登入虛擬機器執行 Windows Server 的][Logon]。
2.  Windows Server 2008 R2 中，按一下 [ **開始 > Symantec Endpoint Protection**。 Windows Server 2012 或 Windows Server 2012 R2，從 [開始] 畫面中，輸入 **Symantec**, ，然後按一下 [ **Symantec Endpoint Protection**。
3.  從 **狀態** ] 索引標籤的 **狀態-Symantec Endpoint Protection** ] 視窗中，套用更新或視需要重新啟動。

## 其他資源

[如何登入執行 Windows Server 的虛擬機器][Logon]

[Azure VM 延伸模組與功能][Ext]


<!--Link references-->
[Symantec]: http://go.microsoft.com/fwlink/p/?LinkId=403942

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-tutorial-classic-portal.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-log-on-windows-server.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

