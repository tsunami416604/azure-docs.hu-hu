<properties
    pageTitle="關於虛擬機器的映像 | Microsoft Azure"
    description="深入了解如何透過 Azure 的虛擬機器使用映像。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/13/2015"
    ms.author="cynthn"/>

# 關於虛擬機器的映像

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。

Azure 會使用映像透過作業系統提供新的虛擬機器。 一個映像可能有一或多個資料磁碟。 映像可來自多個來源：

-   Azure 提供的映像中的 [Marketplace](http://azure.microsoft.com/gallery/virtual-machines/)。 您可以找到最新版本的 Windows Server 和 Linux 作業系統散發套件。 有些映像也包含應用程式，例如 SQL Server。 MSDN 權益與 MSDN 隨收隨付的訂閱者可以存取其他映像。
-   開放原始碼社群提供的映像，透過 [VM Depot](http://vmdepot.msopentech.com/List/Index)。
-   您也可以擷取現有的 Azure 虛擬機器作為映像或上傳映像，並在 Azure 中儲存和使用自己的映像。

## 關於 VM 映像和 OS 映像

在 Azure 中可用的映像的兩種類型: *VM 映像* 和 *OS 映像*。 VM 映像包含作業系統和建立映像時所有連接至虛擬機器的磁碟。 這是較新的映像類型。 導入 VM 映像前，Azure 中的映像可能只會有一般化的作業系統，而沒有其他磁碟。 僅包含一般化作業系統的 VM 映像基本上與映像的原始類型相同，即 OS 映像。

您可以根據 Azure 上的虛擬機器，或您複製及上傳並正在其他地方執行的虛擬機器，來建立自己的映像。 如果您想要使用一個映像來建立多個虛擬機器，您必須先將映像一般化，以便準備作為映像使用。 若要建立 Windows Server 映像，請在上傳.vhd 檔案前，先在伺服器上執行 Sysprep 命令進行一般化。 如需有關 Sysprep 的詳細資訊，請參閱 [如何使用 Sysprep: 簡介](http://go.microsoft.com/fwlink/p/?LinkId=392030)。 若要建立 Linux 映像，取決於該軟體發佈，您需要執行一組特定用於該發佈的命令，並執行 Azure Linux 代理程式。

## 使用映像

您可以使用適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (CLI)，或使用 Azure PowerShell 模組來管理您的 Azure 訂用帳戶可使用的映像。 您也可以使用 Azure 傳統入口網站進行部分映像工作，但命令列則提供您更多選項。

如需使用這些工具與資源管理員部署的詳細資訊，請參閱 [利用 PowerShell 和 Azure CLI 瀏覽和選取 Azure 虛擬機器映像](resource-groups-vm-searching.md)。

在傳統部署中使用這些工具的範例：

- CLI，請參閱 < 命令來管理您的 Azure 虛擬機器映像 >，在 [使用 Azure CLI for Mac、 Linux 和 Windows 搭配 Azure 服務管理](virtual-machines-command-line-tools.md)
- 針對 Azure PowerShell，請參閱下列命令清單。 尋找建立的 VM 映像的範例，請參閱 「 步驟 3:: 決定 ImageFamily 」，在 [使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](virtual-machines-ps-create-preconfigure-windows-vms.md)

-   **取得所有影像**:`Get-AzureVMImage`會傳回一份所有目前訂閱中可用的映像: 您的映像，以及 Azure 或協力廠商所提供。 這表示您可能會收到龐大的清單。 下面的範例示範如何取得較短的清單。
-   **取得映像系列**:`Get-AzureVMImage | select ImageFamily` 顯示字串所取得的映像系列清單 **ImageFamily** 屬性。
-   **取得特定系列中的所有映像**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-   **尋找 VM 映像**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` 運作的方式是篩選 DataDiskConfiguration 屬性僅適用於 VM 映像。 此範例也會篩選輸出並僅列出標籤和映像名稱。
-   **將一般化映像儲存**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-   **將特定的映像儲存**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>如果您想要建立的 VM 映像，其中包括資料磁碟以及作業系統磁碟，則需要 [Azure.Tip] OSState 參數。 如果您不使用參數，Cmdlet 就會建立 OS 映像。 根據作業系統磁碟是否準備為重複使用，參數的值會表示是否要將映像一般化或特殊化。
-   **刪除映像**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## 其他資源

[建立 Linux 虛擬機器的不同方式](virtual-machines-linux-choices-create-vm.md)

[建立 Windows 虛擬機器的不同方式](virtual-machines-windows-choices-create-vm.md)

