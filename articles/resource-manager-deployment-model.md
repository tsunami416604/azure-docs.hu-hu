<properties
   pageTitle="了解資源管理員與傳統部署模型之間的差異"
   description="描述資源管理員部署模型與傳統 (或服務管理) 部署模型之間的差異。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="tomfitz"/>

# 了解資源管理員部署和傳統部署

資源管理員部署模型提供新的方式來部署和管理組成應用程式的服務。 這個新的模型包含重要 
從傳統部署模型和兩個模型的差異並不是彼此完全相容的。 為了簡化部署 
與資源的管理，Microsoft 建議您使用新的資源，資源管理員，可能的話，請重新部署現有的資源透過資源管理員。

您可能也知道傳統部署模型也就是服務管理模型。

本主題描述這兩個模型之間的差異，以及從傳統模型轉換為資源管理員時可能會遇到的部分問題。 
它提供模型的概觀但未詳述個別服務的差異。 如需詳細資訊轉換運算、 儲存和 
網路資源，請參閱 [Azure 運算、 網路和存放裝置提供者 Azure 資源管理員底下](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md)。

許多資源可同時在傳統模型和資源管理員中沒有問題的運作。 這些資源完全支援資源管理員，即使建立中 
傳統的模型。 您可以在無需任何顧慮或額外動作下轉換為資源管理員。

不過，由於模型之間的架構差異，一些資源提供者會提供兩個版本的資源 (一個用於傳統和一個用於資源管理員)。 
區別兩種模型之間的資源提供者是：

- 計算
- 儲存體
- 網路

針對這些資源類型，您必須知道您使用哪一個版本，因為支援的作業將會不同。 

若要了解兩個模型之間的架構差異，請參閱 [Azure 資源管理員架構](virtual-machines/virtual-machines-azure-resource-manager-architecture.md)。

## 資源管理員特性

透過資源管理員建立的資源都具有下列特性：

- 透過下列其中一個方法建立：

  -  [Azure 入口網站](https://portal.azure.com/)。

        ![Azure portal](./media/resource-manager-deployment-model/preview-portal.png)

        For Compute, Storage, and Networking resources, you have the option of using either Resource Manager or Classic deployment. Select **Resource Manager**.

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - Azure PowerShell 版本早於 1.0 時，命令執行 **AzureResourceManager** 模式。

            PS C:\> Switch-AzureMode -Name AzureResourceManager

  - 對於 Azure PowerShell 1.0 版，請使用資源管理員版本的命令。 這些命令的格式 *動詞 AzureRm*, ，如下所示。

            PS C:\> Get-AzureRmResourceGroupDeployment

  - [Azure 資源管理員 REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx) REST 作業。
  - 在執行 azure CLI 命令 **arm** 模式。

            azure config mode arm

- 資源類型不包含 **（傳統）** 名稱中。 下圖顯示與類型 **儲存體帳戶**。

    ![Web 應用程式](./media/resource-manager-deployment-model/resource-manager-type.png)

## 傳統部署特性

在傳統部署模型中建立的資源都具有下列特性：

- 透過下列其中一個方法建立：

  - [傳統入口網站](https://manage.windowsazure.com)

        ![Classic portal](./media/resource-manager-deployment-model/azure-portal.png)

        Or, the portal and you specify **Classic** deployment (for Compute, Storage, and Networking).

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - 命令執行之前的版本 PowerShell 1.0，如 **AzureServiceManagement** 模式 (這是預設模式，因此如果不這麼做特別切換至 AzureResourceManager、 AzureServiceManagement 模式中執行)。

            PS C:\> Switch-AzureMode -Name AzureServiceManagement

  - 對於 Azure PowerShell 1.0 版，請使用服務管理版本的命令。 這些命令名稱 **不** 格式 *動詞 AzureRm*, ，如下所示。

            PS C:\> Get-AzureDeployment

  - [服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx) REST 作業。
  - 在執行 azure CLI 命令 **asm** 或預設模式。
- 資源類型包含 **（傳統）** 名稱中。 下圖顯示與類型 **儲存體帳戶 （傳統）**。

    ![傳統類型](./media/resource-manager-deployment-model/classic-type.png)

您仍然可以使用入口網站來管理透過傳統部署所建立的資源。

## 使用資源管理員及資源群組的優點

資源管理員加入資源群組的概念。 透過資源管理員建立的每個資源都會存在於資源群組內。 資源管理員部署模型可提供數個優點：

- 您可以以群組形式部署、管理及監視方案的所有服務，而不是個別處理這些服務。
- 您可以在整個應用程式週期重複部署應用程式，並確信您的資源會部署在一致的狀態中。
- 您可以使用宣告式範本來定義您的部署。 
- 您可以定義之間的相依性，使得以正確的順序部署資源。
- 因為角色型存取控制 (RBAC) 會原生整合至管理平台，您可以將存取控制套用至資源群組中的所有服務。
- 您可以將標籤套用至資源，以便以邏輯方式組織訂用帳戶中的所有資源。


在資源管理員之前，您透過傳統部署建立的每個資源未存在於資源群組內。 加入資源管理員時，所有資源會追溯地加入至預設資源群組。 如果您建立透過傳統部署現在的資源，資源是 
即使您未指定在部署該資源群組，會自動建立該服務的預設資源群組內。 不過，只是存在於資源群組內並不表示該資源已轉換成資源管理員模型。 對於虛擬機器、儲存體和虛擬網路，如果是透過傳統部署建立資源，您必須繼續透過傳統作業來運作。 

您可以將資源移至不同的資源群組，並將新的資源加入至現有的資源群組。 因此，可以包含您的資源群組 
透過資源管理員和傳統部署建立的資源混用。 此資源的組合可以建立非預期的結果，因為資源 
不支援相同的作業。

透過使用宣告式範本，您可以簡化用於部署的指令碼。 而不是嘗試從服務管理資源管理員 」 來轉換現有的指令碼，請考慮重新執行到您的部署策略 
利用在範本中定義您的基礎結構和組態。

## 使用標記

標籤可讓您以邏輯方式組織您的資源。 只有透過資源管理員建立的資源支援標記。 您無法將標籤套用到傳統資源。

如需資源管理員中使用標記的詳細資訊，請參閱 [使用標記來組織您的 Azure 資源](resource-group-using-tags.md)。

## 部署模型支援的作業

在傳統部署模型中所建立的資源不支援資源管理員作業。 在某些情況下，可以擷取資源管理員命令 
資源的資訊建立透過傳統部署，或可以執行管理工作，例如將傳統資源移至另一個資源群組，但 
這種情況下不應該為指定的型別支援資源管理員操作的印象。 例如，假設有一個資源群組 
其中包含所建立的資源管理員和傳統的虛擬機器。 如果您執行下列 PowerShell 命令，您會看到所有虛擬機器的：

    PS C:\> Get-AzureRmResourceGroup -Name ExampleGroup
    ...
    Resources :
     Name                 Type                                          Location
     ================     ============================================  ========
     ExampleClassicVM     Microsoft.ClassicCompute/domainNames          eastus
     ExampleClassicVM     Microsoft.ClassicCompute/virtualMachines      eastus
     ExampleResourceVM    Microsoft.Compute/virtualMachines             eastus
    ...

不過，如果您執行 Get-AzureVM 命令，則只會出現使用資源管理員建立的虛擬機器。

    PS C:\> Get-AzureVM -ResourceGroupName ExampleGroup
    ...
    Id       : /subscriptions/xxxx/resourceGroups/ExampleGroup/providers/Microsoft.Compute/virtualMachines/ExampleResourceVM
    Name     : ExampleResourceVM
    ...

一般而言，您不應該預期透過傳統部署所建立的資源可使用資源管理員命令。

使用透過資源管理員建立的資源時，您應該使用資源管理員作業，而不是切換回服務管理作業。

## 虛擬機器的考量

使用虛擬機器時有一些重要的考量。

- 使用傳統部署模型部署的虛擬機器不能包含在使用資源管理員部署的虛擬網路中。
- 使用資源管理員部署模型部署的虛擬機器必須包含在虛擬網路中。
- 使用傳統部署模型部署的虛擬機器不一定要包含在虛擬網路中。

如果您為虛擬機器，您可以負擔停機時間，您可以轉換從傳統部署至資源管理員與 [ASM2ARM PowerShell 指令碼](https://github.com/fullscale180/asm2arm)。 

如需對等的 Azure CLI 命令轉換從傳統部署至資源管理員時，請參閱 [的 VM 操作的對等資源管理員和服務管理命令](./virtual-machines/xplat-cli-azure-manage-vm-asm-arm.md)。

如需詳細資訊轉換運算、 儲存和 
網路資源，請參閱 [Azure 運算、 網路和存放裝置提供者 Azure 資源管理員底下](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md)。

若要深入了解虛擬網路連線在從不同的部署模型，請參閱 [傳統 Vnet 連接到新的 Vnet](./virtual-network/virtual-networks-arm-asm-s2s.md)。

## 後續步驟

- 若要了解如何建立宣告式部署範本，請參閱 [撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。
- 部署範本的命令，請參閱 [應用程式使用 Azure 資源管理員範本部署](resource-group-template-deploy.md)。



