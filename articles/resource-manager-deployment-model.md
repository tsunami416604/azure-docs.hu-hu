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

資源管理員部署模型提供新的方式來部署和管理組成應用程式的服務。 
 


您可能也知道傳統部署模型也就是服務管理模型。

本主題描述這兩個模型之間的差異，以及從傳統模型轉換為資源管理員時可能會遇到的部分問題。 
它提供模型的概觀但未詳述個別服務的差異。 


許多資源可同時在傳統模型和資源管理員中沒有問題的運作。 
您可以在無需任何顧慮或額外動作下轉換為資源管理員。

不過，由於模型之間的架構差異，一些資源提供者會提供兩個版本的資源 (一個用於傳統和一個用於資源管理員)。 
區別兩種模型之間的資源提供者是：

- 計算
- 儲存體
- 網路

針對這些資源類型，您必須知道您使用哪一個版本，因為支援的作業將會不同。



## 資源管理員特性

透過資源管理員建立的資源都具有下列特性：

- 透過下列其中一個方法建立：

- 

    ![Azure portal](./media/resource-manager-deployment-model/preview-portal.png)
    
    For Compute, Storage, and Networking resources, you have the option of using either Resource Manager or Classic deployment. Select **Resource Manager**.
    
    ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

- 對於比 Azure PowerShell 1.0 版還舊的版本，這些命令會在 **AzureResourceManager** 模式中執行。

          PS C:\> Switch-AzureMode -Name AzureResourceManager

- 對於 Azure PowerShell 1.0 版，請使用資源管理員版本的命令。 這些命令的格式為 *verb-AzureRm*，如下所示。

          PS C:\> Get-AzureRmResourceGroupDeployment

- 
- Azure CLI 命令會在 **arm** 模式中執行。

          azure config mode arm


- 資源類型的名稱中不包括 **(傳統)**。 下圖顯示的類型為**儲存體帳戶**。

    ![Web 應用程式](./media/resource-manager-deployment-model/resource-manager-type.png)

## 傳統部署特性

在傳統部署模型中建立的資源都具有下列特性：

- 透過下列其中一個方法建立：

- 

    ![Classic portal](./media/resource-manager-deployment-model/azure-portal.png)
    
    Or, the portal and you specify **Classic** deployment (for Compute, Storage, and Networking).
    
    ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

- 對於比 Azure PowerShell 1.0 版還舊的版本，命令會在 **AzureServiceManagement** 模式中執行 (這是預設模式，因此如果未特意切換至 AzureResourceManager，您就會在 AzureServiceManagement 模式中執行)。

          PS C:\> Switch-AzureMode -Name AzureServiceManagement

- 對於 Azure PowerShell 1.0 版，請使用服務管理版本的命令。 這些命令名稱的格式**不是** *verb-AzureRm*，如下所示。

          PS C:\> Get-AzureDeployment

- 
- Azure CLI 命令會在 **asm** 或預設模式中執行。
- 資源類型的名稱中包括 **(傳統)**。 下圖顯示的類型為**儲存體帳戶 (傳統)**。

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


在資源管理員之前，您透過傳統部署建立的每個資源未存在於資源群組內。 加入資源管理員時，所有資源會追溯地加入至預設資源群組。 
不過，只是存在於資源群組內並不表示該資源已轉換成資源管理員模型。 對於虛擬機器、儲存體和虛擬網路，如果是透過傳統部署建立資源，您必須繼續透過傳統作業來運作。

您可以將資源移至不同的資源群組，並將新的資源加入至現有的資源群組。 
 


透過使用宣告式範本，您可以簡化用於部署的指令碼。 


## 使用標記

標籤可讓您以邏輯方式組織您的資源。 只有透過資源管理員建立的資源支援標記。 您無法將標籤套用到傳統資源。



## 部署模型支援的作業

在傳統部署模型中所建立的資源不支援資源管理員作業。 
 
 
如果您執行下列 PowerShell 命令，您會看到所有虛擬機器的：

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





 




## 後續步驟

- 
- 






