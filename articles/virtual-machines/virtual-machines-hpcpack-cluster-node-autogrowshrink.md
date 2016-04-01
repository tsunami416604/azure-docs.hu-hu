<properties
 pageTitle="自動調整 HPC 叢集中的運算資源 | Microsoft Azure"
 description="了解在 Azure 的 HPC Pack 叢集中自動增加和縮減運算資源的方法"
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

# 在 HPC Pack 叢集中根據叢集工作負載自動向上和向下調整 Azure 運算資源

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


如果您部署 HPC Pack 叢集，或者您的 Azure 「 高載 」 節點
在 Azure Vm 中建立的 HPC Pack 叢集，您可能會想辦法
自動成長或壓縮根據 Azure 運算資源
工作在叢集上的工作的目前的工作負載。 這可讓您
若要更有效率地使用您的 Azure 資源，並控制其成本。
若要這樣做，請使用
**AzureAutoGrowShrink.ps1** 一起安裝的 HPC PowerShell 指令碼
HPC Pack。

>[AZURE.TIP] 從 HPC Pack 2012 R2 Update 2 開始，HPC Pack 包含內建
服務會自動放大和縮小 Azure 高載節點或
Azure VM 運算節點。 將服務設定中設定 [HPC
Pack IaaS 部署指令碼](virtual-machines-hpcpack-cluster-powershell-script.md) 或手動設定 **AutoGrowShrink** 叢集
-Eigenschaft. 請參閱 [的 Microsoft HPC Pack 2012 R2 更新新功能
2](https://technet.microsoft.com/library/mt269417.aspx).

## 必要條件

* **HPC Pack 2012 R2 Update 1 或更新版本的叢集** - **AzureAutoGrowShrink.ps1** 指令碼安裝在 %ccp_home%bin 資料夾中。 叢集前端節點可以部署在內部部署或在 Azure VM 中。 請參閱 [設定混合式 HPC pack 叢集](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) 若要開始使用內部部署前端節點和 Azure 「 高載 」 節點。 請參閱 [HPC Pack IaaS 部署指令碼](virtual-machines-hpcpack-cluster-powershell-script.md)) 來快速部署 Azure Vm 中的 HPC Pack 叢集。

* **針對 Azure 的叢集高載節點** -HPC Pack 安裝在哪裡，用戶端電腦上或在前端節點上執行指令碼。 如果是在用戶端電腦上執行，請確定您正確設定變數 $env:CCP_SCHEDULER 以指向前端節點。 Azure「高載」節點必須已經加入到叢集，但它們可能處於「未部署」狀態。


* **叢集部署於 Azure Vm 中的** -前端節點 VM 上執行指令碼，因為它相依於 **Start-hpciaasnode.ps1** 和 **Stop-hpciaasnode.ps1** 所安裝的指令碼。 這些指令碼此外需要 Azure 管理憑證或發行設定檔 (請參閱 [管理計算節點的 HPC Pack 叢集在 Azure 中](virtual-machines-hpcpack-cluster-node-manage.md))。 請確定您所需的所有運算節點 VM 已加入叢集，但是它們可能處於「已停止」狀態。

## 語法

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
## 參數

 * **NodeTemplates** -節點範本的名稱，以定義要擴增和縮減之節點的範圍。 如果未指定 (預設值是 @ （)），在所有節點 **AzureNodes** 節點群組在範圍時 **NodeType** 的值為 AzureNodes，並在所有節點 **ComputeNodes** 節點群組在範圍時 **NodeType** 都會納入範圍內的值。

 * **Jobtemplate** -工作範本的名稱，以定義要擴增之節點的範圍。

 * **NodeType** -放大和縮小節點的類型。 支援的值包括：

     * **AzureNodes** – Azure PaaS （高載） 節點，在內部或 Azure IaaS 叢集。

     * **ComputeNodes** -只有在 Azure IaaS 叢集中的運算節點 Vm。

* **NumOfQueuedJobsPerNodeToGrow** -要擴增某個節點所需的佇列工作數目。

* **NumOfQueuedJobsToGrowThreshold** -排入佇列的工作來啟動擴增程序的閾值。

* **NumOfActiveQueuedTasksPerNodeToGrow** -數字作用中的已排入佇列要擴增某個節點所需的工作。 如果 **NumOfQueuedJobsPerNodeToGrow** 指定的值大於 0，會忽略此參數。

* **NumOfActiveQueuedTasksToGrowThreshold**-的作用中佇列工作來啟動擴增程序的臨界值數目。

* **NumOfInitialNodesToGrow** -初始最小數目時範圍內的所有節點都都會持續成長的節點 **未部署** 或 **已停止 （取消配置）**。

* **GrowCheckIntervalMins** -間隔擴增檢查之間的分鐘數。

* **ShrinkCheckIntervalMins** -間隔縮減檢查之間的分鐘數。

* **ShrinkCheckIdleTimes**-數字的連續縮減檢查 (以分隔 **ShrinkCheckIntervalMins**)，表示節點處於閒置狀態。

* **UseLastConfigurations*** 前述引數檔案中儲存的組態。

* **ArgFile**-用來儲存和更新的設定執行指令碼的引數檔案名稱。

* **LogFilePrefix**-記錄檔的首碼名稱。 您可以指定路徑。 記錄檔預設會寫入目前的工作目錄。

### 範例 1

下列範例會設定以部署的 Azure 高載節點
預設 AzureNode 範本，以自動擴增和縮減。 如果要將所有
節點是一開始處於 **未部署** 狀態，至少 3 個節點都是
才能開始。 如果佇列工作數目超過 8 個，指令碼會啟動節點
直到其數目超過佇列工作與比率
**NumOfQueuedJobsPerNodeToGrow**。 如果發現節點處於閒置狀態 3
連續的閒置時間，便會停止。

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### 範例 2

下列範例會設定 Azure 計算節點部署 Vm
使用預設 ComputeNode 範本來自動擴增和縮減。
預設工作範本所設定的工作定義的範圍
在叢集上的工作負載。 如果一開始會停止所有節點，在
至少 5 個節點會啟動。 如果作用中佇列工作數目超過
15，指令碼會啟動節點，直到其數目超過的比率
使用中工作佇列至 **NumOfActiveQueuedTasksPerNodeToGrow**。 如果
閒置 10 個連續的閒置時間中發現的節點時，它會停止。

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```


