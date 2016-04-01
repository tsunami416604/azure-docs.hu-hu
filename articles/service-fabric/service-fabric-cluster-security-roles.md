
<properties
   pageTitle="Service Fabric 叢集安全性：用戶端角色 |Microsoft Azure"
   description="本文說明兩個用戶端角色及提供給這些角色的權限。" 
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="subramar"/>



# 角色型存取控制 (適用於 Service Fabric 用戶端)

Service Fabric 針對連線到 Service Fabric 叢集的用戶端，支援兩種不同的存取控制類型：系統管理員和使用者。 存取控制可讓叢集系統管理員針對不同的使用者群組限制特定叢集作業的存取權，讓叢集更加安全。  系統管理員可以完整存取管理功能 (包括讀取/寫入功能)，而使用者預設只具有對管理的讀取存取權 (例如查詢)，以及解析應用程式和服務的能力。

這兩個用戶端角色 (系統管理員或用戶端) 的指定，是在建立叢集時，透過為每個角色提供個別的憑證來指定。 請參閱 [Service Fabric 叢集安全性](service-fabric-cluster-security.md) 如需有關設定安全的 Service Fabric 叢集。


## 預設存取控制設定


系統管理員存取控制類型可以完整存取所有的 FabricClient API。 它可以對 Service Fabric 叢集執行任何讀取和寫入作業，包括下列作業：

### 應用程式和服務作業 
* **CreateService** ︰ 服務建立                          
* **CreateServiceFromTemplate** ︰ 服務建立的範本                             
* **UpdateService** ︰ 服務更新                           
* **DeleteService** ︰ 服務刪除                          
* **ProvisionApplicationType** ︰ 佈建應用程式類型                          
* **CreateApplication** ︰ 應用程式的建立                          
* **DeleteApplication** ︰ 應用程式刪除                          
* **UpgradeApplication** ︰ 啟動或中斷應用程式升級                            
* **UnprovisionApplicationType** ︰ 應用程式類型解除佈建                          
* **MoveNextUpgradeDomain** ︰ 繼續使用明確的升級網域的應用程式升級                             
* **ReportUpgradeHealth** ︰ 繼續使用目前的升級程序的應用程式升級                             
* **ReportHealth** ︰ 報告健全狀況                           
* **PredeployPackageToNode** ︰ 預先部署的 api                            
* **CodePackageControl** ︰ 重新啟動的程式碼封裝                             
* **RecoverPartition** ︰ 復原磁碟分割                             
* **RecoverPartitions** ︰ 復原磁碟分割                             
* **RecoverServicePartitions** ︰ 復原服務的資料分割                          
* **RecoverSystemPartitions** ︰ 復原系統服務資料分割                            


### 叢集作業
* **ProvisionFabric** : MSI 和/或叢集資訊清單佈建                            
* **UpgradeFabric** ︰ 啟動叢集升級                             
* **UnprovisionFabric** : MSI 和/或叢集資訊清單解除佈建                            
* **MoveNextFabricUpgradeDomain** ︰ 繼續使用明確的升級網域的叢集升級                          
* **ReportFabricUpgradeHealth** ︰ 繼續使用目前的升級程序的叢集升級                           
* **StartInfrastructureTask** ︰ 啟動基礎結構的工作                           
* **FinishInfrastructureTask** ︰ 完成基礎結構工作                             
* **InvokeInfrastructureCommand** ︰ 基礎結構工作管理命令                             
* **ActivateNode** ︰ 啟動節點                          
* **DeactivateNode** ︰ 停用節點                          
* **DeactivateNodesBatch** ︰ 停用多個節點                            
* **RemoveNodeDeactivations** ︰ 多個節點上的還原停用                            
* **GetNodeDeactivationStatus** ︰ 檢查停用狀態                          
* **NodeStateRemoved** ︰ 移除報告節點狀態                           
* **ReportFault** ︰ 報告錯誤                             
* **FileContent** ︰ 映像存放區的用戶端檔案傳輸 （叢集外部）                          
* **FileDownload** ︰ 映像存放區用戶端檔案下載初始 （外部叢集）                          
* **InternalList** ︰ 映像存放區用戶端檔案清單作業 （內部）                          
* **刪除** ︰ 映像存放區的用戶端刪除作業                          
* **上傳** ︰ 映像存放區的用戶端上傳作業                          
* **NodeControl** ︰ 啟動; 停止; 以及重新啟動節點                            
* **MoveReplicaControl** ︰ 從某個節點的複本移到另一個                           

### 其他作業
* **Ping** ︰ 用戶端 ping                           
* **查詢** ︰ 允許所有查詢
* **NameExists** ︰ 命名 URI 是否存在檢查                          



使用者存取控制類型預設限制為下列作業 (系統管理員存取控制也允許存取這些作業)：

* **EnumerateSubnames** ︰ 命名 URI 列舉                            
* **EnumerateProperties** ︰ 命名屬性的列舉型別                             
* **PropertyReadBatch** ︰ 命名屬性讀取作業                           
* * * GetServiceDescription ︰ 長時間輪詢服務通知和讀取服務描述                          
* **Resolveservice 時傳入** ︰ 相容服務解析                           
* **ResolveNameOwner** ︰ 解析命名 URI 擁有者                             
* **ResolvePartition** ︰ 解決系統服務                          
* **ServiceNotifications** ︰ 事件為基礎的服務通知                          
* **GetUpgradeStatus** ︰ 輪詢應用程式升級狀態                             
* **GetFabricUpgradeStatus** ︰ 輪詢叢集升級狀態                           
* **InvokeInfrastructureQuery** ︰ 查詢基礎結構工作                             
* **清單** ︰ 映像存放區的用戶端檔案清單作業                             
* **ResetPartitionLoad** : failoverUnit 的重設負載                            
* **ToggleVerboseServicePlacementHealthReporting：切換詳細服務位置健康情況報告                           

## 變更用戶端角色的預設設定

在叢集資訊清單檔案中，可以視需要提供系統管理員功能給用戶端。 您可以變更預設值，請前往 **網狀架構設定* 選項， [叢集建立](service-fabric-cluster-creation-via-portal.md) 提供名為依照上述方式中的設定和 **名稱** ] 欄位中，和 **admin 使用者** 值欄位中。 

## 後續步驟

[Service Fabric 叢集安全性](service-fabric-cluster-security.md)

[Service Fabric 叢集建立](service-fabric-cluster-creation-via-portal.md)


