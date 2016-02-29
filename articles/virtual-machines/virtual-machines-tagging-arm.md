<properties
   pageTitle="如何標記 VM |Microsoft Azure"
   description="了解如何標記以資源管理員部署模型建立的 Azure 虛擬機器。"
   services="virtual-machines"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="dkshir;memccror"/>

# 如何在 Azure 中標記虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。


本文說明在 Azure 中透過 Azure 資源管理員標記虛擬機器的不同方式。 標記是使用者定義的索引鍵/值組，可直接置於資源或資源群組。 Azure 目前對每一個資源和資源群組最多支援 15 個標記。 標記可在建立或加入至現有資源時置於資源上。 請注意，標籤只支援透過 Azure 資源管理員建立的資源。

## 透過範本標記虛擬機器

首先，我們來看一下透過範本進行標記。 [此範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm) 標記置於下列資源: 運算 (虛擬機器)、 儲存體 (儲存體帳戶) 和網路 (公用 IP 位址、 虛擬網路，以及網路介面)。

按一下 [ **部署至 Azure** 按鈕 [範本連結](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm)。 這會導覽至 [Azure 入口網站](http://portal.azure.com/) 您可以在其中部署此範本。

![使用標記的簡單部署](./media/virtual-machines-tagging-arm/deploy-to-azure-tags.png)

此範本包含下列標記: *部門*, ，*應用程式*, ，和 *建立者*。 如果您想要不同的標記名稱，您可以直接在範本中新增/編輯這些標記。

![範本中的 Azure 標記](./media/virtual-machines-tagging-arm/azure-tags-in-a-template.png)

如您所見，標記會定義為索引鍵/值組並以冒號 (:) 分隔。 標記必須以這種格式定義：

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

在您使用您所選擇的標籤完成編輯後，儲存範本檔案。

接下來，在 **編輯參數** ] 區段中，您可以為您的標記填入相關值。

![在 Azure 入口網站中編輯標記](./media/virtual-machines-tagging-arm/edit-tags-in-azure-portal.png)

按一下 [ **建立** 來部署您的標記值與此範本。


## 透過入口網站進行標記

使用標記建立您的資源之後，您可以在入口網站中檢視、新增和刪除標記。

選取標記圖示以檢視您的標記：

![Azure 入口網站中的標記圖示](./media/virtual-machines-tagging-arm/azure-portal-tags-icon.png)

透過入口網站定義自己的索引鍵/值組，以加入新標記並加以儲存。

![在 Azure 入口網站中新增標記](./media/virtual-machines-tagging-arm/azure-portal-add-new-tag.png)

新標籤現在應出現在您的資源的標記清單中。

![Azure 入口網站中儲存的新標記](./media/virtual-machines-tagging-arm/azure-portal-saved-new-tag.png)


## 使用 PowerShell 來標記

若要建立，，加上刪除標記透過 PowerShell，您必須先設定您 [PowerShell 環境與 Azure 資源管理員][]。 完成設定之後，您可以在建立標記時或在透過 PowerShell 建立資源之後，將標記置於運算、網路和儲存體上。 本文將專注於檢視/編輯置於虛擬機器上的標記。

首先，透過 `Get-AzureVM` Cmdlet 瀏覽至虛擬機器。

        PS C:\> Get-AzureVM -ResourceGroupName "MyResourceGroup" -Name "MyWindowsVM"

如果虛擬機器已經包含標記，您則會看到資源上的所有標記：

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

如果要透過 PowerShell 新增標記，您可以使用 `Set-AzureResource` 命令。 請注意，透過 PowerShell 標記更新時，標記會整體進行更新。 所以，如果您將一個標記新增至已有標記的資源，您必須包含想要置於資源上的所有標記。 以下是如何透過 PowerShell Cmdlet 將其他標記新增至資源的範例。

這個第一個指令程式可設定的所有標記置於 *MyWindowsVM* 至 *標記* 變數時，使用 `Get-AzureResource` 和 `Tags` 函式。 請注意，參數 `ApiVersion` 為選擇性；如果未指定，則會使用資源提供者的最新 API 版本。 

        PS C:\> $tags = (Get-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/virtualmachines" -ApiVersion 2015-05-01-preview).Tags

第二個命令會顯示指定變數的標記。

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment

第三個命令會將附加標記以 *標記* 變數。 請注意使用 **+ =** 要附加至新的索引鍵/值組 *標記* 清單。

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

第四個命令會將所有定義的標記 *標記* 變數設為指定的資源。 在此情況下是 MyWindowsVM。

        PS C:\> Set-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview -Tag $tags

第五個命令顯示資源上的所有標記。 如您所見， *位置* 現在已定義為具有標記 *MyLocation* 做為值。

        PS C:\> (Get-AzureResource -ResourceName "MyWindowsVM" -ResourceGroupName "MyResourceGroup" -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview).Tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment
        Value       MyLocation
        Name        Location

若要深入了解透過 PowerShell 標記，請參閱 [Azure 資源 Cmdlet][]。


## 透過 Azure CLI 進行標記

已透過 Azure CLI 建立的資源也支援標記。 若要開始，設定您 [Azure CLI 環境][]。 透過 Azure CLI 登入您的訂用帳戶並切換至 ARM 模式。 您可以使用這個命令來檢視指定之虛擬機器的所有屬性，包括標記：

        azure vm show -g MyResourceGroup -n MyVM

不同於 PowerShell 的是，如果您要將標記新增至已含有標記的資源，不需先指定所有標記 (無論新舊)，即可使用 `azure vm set` 命令。 相反地，此命令可讓您將標記附加至您的資源。 若要加入新的 VM 標記，透過 Azure CLI，您可以使用 `azure vm set` 命令與標記參數 **-t**:

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

若要移除所有標記，您可以使用 **– T** 中的參數 `azure vm set` 命令。

        azure vm set – g MyResourceGroup –n MyVM -T


既然我們已透過 PowerShell、Azure CLI 和入口網站，將標記套用至我們的資源，就讓我們來看一下使用量詳細資料，以在計費入口網站中查看標記。


## 在使用量詳細資料中檢視標記

標記置於運算、 網路和存放裝置資源透過 Azure 資源管理員就會填入您的使用量詳細資料中 [計費入口網站](https://account.windowsazure.com/)。

按一下 [ **下載使用量詳細資料** 在您的訂閱檢視使用量詳細資料。

![Azure 入口網站中的使用量詳細資料](./media/virtual-machines-tagging-arm/azure-portal-tags-usage-details.png)

選取您的計費和 **第 2 版** 使用量詳細資料:

![Azure 入口網站中的版本 2 預覽使用量詳細資料](./media/virtual-machines-tagging-arm/azure-portal-version2-usage-details.png)

從使用量詳細資料，您可以看到所有的標記中 **標記** 資料行:

![Azure 入口網站中的標記資料行](./media/virtual-machines-tagging-arm/azure-portal-tags-column.png)

藉由分析這些標記和使用量，組織將可對其耗用資料產生新的見解。


## 其他資源

* [Azure Resource Manager 概觀][]
* [使用標記來組織 Azure 資源][]
* [了解 Azure 帳單][]
* [深入瞭解 Microsoft Azure 資源耗用量][]




[PowerShell environment with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../resource-group-using-tags.md
[Understanding your Azure Bill]: ../billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../billing-usage-rate-card-overview.md

