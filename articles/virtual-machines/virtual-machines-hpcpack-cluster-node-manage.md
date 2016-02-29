<properties
 pageTitle="管理 HPC Pack 叢集運算節點 |Microsoft Azure"
 description="了解可在 Azure 中新增、移除、啟動和停止 HPC Pack 叢集運算節點的 PowerShell 指令碼工具"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# 在 Azure 的 HPC Pack 叢集中管理運算節點的數目和可用性

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


如果您在 Azure Vm 中建立的 HPC Pack 叢集，您可以輕鬆地新增、 移除方法
啟動 (佈建) 或停止 (解除佈建) 數目計算節點 Vm
帳戶。 若要執行這些工作，執行 PowerShell 指令碼
安裝在前端節點 VM (從 HPC Pack 2012 R2 Update 1 開始)。 這些指令碼可協助您控制數目
並讓您能夠控制成本 HPC Pack 叢集資源的可用性。

>[AZURE.NOTE] 這些指令碼位於前端節點上的 %CCP\_HOME%bin 資料夾。 您必須
系統管理員身分執行每個指令碼。

## 必要條件

* **在 Azure Vm 中的 HPC Pack 叢集** -在傳統 (服務管理) 部署模型中建立的 HPC Pack 叢集，至少使用 HPC Pack 2012 R2 Update 1。 例如，您可以使用 Azure Marketplace 中的 HPC Pack VM 映像和 Azure PowerShell 指令碼，將部署自動化。 資訊和必要條件，請參閱 [使用 HPC Pack IaaS 部署指令碼建立 HPC 叢集](virtual-machines-hpcpack-cluster-powershell-script.md)。

* **Azure 發行設定檔] 或 [管理憑證** -您需要執行的前端節點上的下列其中一項:

    * **匯入 Azure 發行設定檔**。 若要這麼做，請在前端節點上執行下列 Azure PowerShell Cmdlet：

    ```
    Get-AzurePublishSettingsFile 
         
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
    
    * **設定前端節點上的 Azure 管理憑證**。 如果您有 .cer 檔案，請在 CurrentUser\My certificate store 中將其匯入，並為您的 Azure 環境 (AzureCloud 或 AzureChinaCloud) 執行下列 Azure PowerShell Cmdlet：

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## 新增運算節點 VM

加入計算節點與 **Add-hpciaasnode.ps1** 指令碼。

### 語法
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### 參數

* **ServiceName** -將會加入 [新增計算節點 Vm 的雲端服務名稱。

* **ImageName** -Azure VM 映像名稱，可以透過 Azure 傳統入口網站或 Azure PowerShell cmdlet 取得 **Get-azurevmimage**。 這些映像必須符合下列需求：

    1. 必須安裝 Windows 作業系統。

    2. 必須在運算節點角色中安裝 HPC Pack。

    3. 映像必須是使用者類別中的私人映像，而不是公用 Azure VM 映像。

* **Quantity**-運算節點 Vm 加入的數目。

* **InstanceSize** -運算節點 Vm 的大小。

* **DomainUserName** -網域使用者名稱，將用來將新的 Vm 加入網域。

* **DomainUserPassword*** 網域使用者的密碼

* **NodeNameSeries** (選擇性) 0 的運算節點的命名模式。 格式必須是 & l t;*Root\_Name*& gt; & l t;*Start\_Number*& gt; %。 例如，MyCN%10% 表示從 MyCN11 開始的一系列運算節點名稱。 如果未指定，指令碼會使用 HPC 叢集中已設定的節點命名序列。

### 範例

下列範例會將 20 大型運算節點 Vm 加入定域機組中
服務 hpcservice1，根據 VM 映像 hpccnimage1。

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## 移除運算節點 VM

移除運算節點 **Remove-hpciaasnode.ps1** 指令碼。

### 語法

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### 參數

 * **名稱** -要移除的叢集節點名稱。 支援萬用字元。 參數集名稱是 Name。 您無法同時指定 **名稱** 和 **節點** 參數。

* **節點** * HpcNode 物件，可透過 HPC PowerShell cmdlet 取得要移除節點 [Get-hpcnode](https://technet.microsoft.com/library/dn887927.aspx)。 參數集名稱是 Node。 您無法同時指定 **名稱** 和 **節點** 參數。

* **DeleteVHD** (選用)-若要移除的 vm 刪除相關聯的磁碟設定。

* **強制** (選用)-設為 [強制移除之前，先 HPC 節點離線。

* **確認** (選用)-執行命令之前確認提示。

* **WhatIf** -設定，以說明當您執行此命令沒有實際執行命令，會發生什麼情況。

### 範例

下列範例會強制離線的節點名稱開頭
*為 Hpcnode-cn-CN-* 並加以移除節點和其相關聯的磁碟。

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## 啟動運算節點 VM

開始計算節點 **Start-hpciaasnode.ps1** 指令碼。

### 語法

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### 參數

* **名稱** -啟動叢集節點的名稱。 支援萬用字元。 參數集名稱是 Name。 您無法同時指定 **名稱** 和 **節點** 參數。

* **節點**-HpcNode 物件，為已啟動，而這可以透過 HPC PowerShell cmdlet 取得的節點 [Get-hpcnode](https://technet.microsoft.com/library/dn887927.aspx)。 參數集名稱是 Node。 您無法同時指定 **名稱** 和 **節點** 參數。

### 範例

下列範例會啟動節點，名稱開頭 *為 Hpcnode-cn-CN-*。

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## 停止運算節點 VM

停止運算節點與 **Stop-hpciaasnode.ps1** 指令碼

### 語法

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### 參數


* **名稱**-要停止叢集節點的名稱。 支援萬用字元。 參數集名稱是 Name。 您無法同時指定 **名稱** 和 **節點** 參數。

* **節點** -HpcNode 物件，會停止運作，可透過 HPC PowerShell cmdlet Get-hpcnode 取得的節點。 參數集名稱是 Node。 您無法同時指定 **名稱** 和 **節點** 參數。

* **強制** (選用)-設為 [HPC 節點強制離線之前加以停止。

### 範例

下列範例會強制離線的節點名稱開頭
*為 Hpcnode-cn-CN-* ，然後停止節點。

Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force

## 後續步驟

* 如果您想要的方式
自動成長或壓縮根據 Azure 運算資源
目前的工作負載的作業和工作，在叢集上，請參閱 [漲大及縮小 Azure 計算資源中的 HPC Pack 叢集](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)。

