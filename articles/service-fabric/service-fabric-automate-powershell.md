<properties
    pageTitle="藉由使用 PowerShell 自動化 Service Fabric 應用程式管理 | Microsoft Azure"
    description="藉由使用 PowerShell 來部署、升級、測試和移除 Service Fabric 應用程式。"
    services="service-fabric"
    documentationCenter=".net"
    authors="rwike77"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-fabric"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="12/14/2015"
    ms.author="ryanwi"/>

# 藉由使用 PowerShell 來部署、升級、測試和移除 Service Fabric 應用程式

本文示範如何使用 Powershell，自動化部署、升級、移除和測試 Azure Service Fabric 應用程式的常見工作。  

## 先決條件

您移至文件中的工作之前，務必先 [安裝執行階段、 SDK 和工具](service-fabric-get-started.md), ，這也會安裝 **ServiceFabric** 和 **ServiceFabricTestability** PowerShell 模組。 [啟用 PowerShell 指令碼執行](service-fabric-get-started.md#enable-powershell-script-execution) 和 [安裝並啟動本機叢集](service-fabric-get-started.md#install-and-start-a-local-cluster) 以便執行範例文件中。

在此範例本文使用 [**WordCount** 範例應用程式](http://aka.ms/servicefabricsamples) (位於快速入門範例)。 下載和建置範例應用程式。

在本文中執行任何 PowerShell 命令之前，先使用連線到本機 Service Fabric 叢集 [**Connect-servicefabriccluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx):

```powershell
Connect-ServiceFabricCluster localhost:19000
```

## 工作：部署 Service Fabric 應用程式

在您建置應用程式且已封裝應用程式類型之後，您可以將應用程式部署至本機 Service Fabric 叢集。 首先，以滑鼠右鍵按一下封裝在 Visual Studio 中的 WordCount 應用程式 **WordCount** 方案總管] 中選取 **封裝**。  請參閱 [模型化應用程式在 Service Fabric](service-fabric-application-model.md) 如需有關服務、 應用程式資訊清單，以及封裝配置資訊。  部署牽涉到上傳應用程式封裝、註冊應用程式類型，以及建立應用程式執行個體。 使用本節中的指示，將新的應用程式部署至叢集。

### 步驟 1：上傳應用程式封裝
上傳應用程式封裝至映像存放區，會將它放在一個可存取至內部 Service Fabric 元件的位置。  應用程式封裝包含必要的應用程式資訊清單、服務資訊清單，以及用來建立應用程式和服務執行個體的程式碼/組態/資料封裝。   [**Copy-servicefabricapplicationpackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) 命令將上傳封裝。 例如：

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### 步驟 2：註冊應用程式類型
註冊應用程式封裝，讓應用程式類型和應用程式資訊清單中宣告的版本可供使用。 系統會讀取上一個步驟中上傳的封裝，請確認封裝 (相當於執行 [**Test-servicefabricapplicationpackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) 本機)、 處理封裝內容，然後將處理過的封裝複製到內部系統位置。  執行 [**Register-servicefabricapplicationtype**](https://msdn.microsoft.com/library/azure/mt125958.aspx) 指令程式:

```powershell
Register-ServiceFabricApplicationType WordCount
```
若要查看在叢集中註冊的應用程式類型，執行 Cmdlet：

```powershell
Get-ServiceFabricApplicationType
```

### 步驟 3：建立應用程式執行個體
應用程式可以使用任何已順利註冊所使用的應用程式類型版本來具現化 [**New-servicefabricapplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) 命令。 每個應用程式名稱開頭必須 **網狀架構:** 配置，並且是唯一的每個應用程式執行個體。 在中宣告的應用程式類型名稱和應用程式類型版本 **ApplicationManifest.xml** 檔案。 如果已在目標應用程式類型的應用程式資訊清單中定義預設服務，則這些服務也會一併建立。

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

 [**Get-servicefabricapplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) 命令會列出所有已成功建立，及其整體狀態的應用程式執行個體。  [**Get-servicefabricservice**](https://msdn.microsoft.com/library/azure/mt125889.aspx) 命令會列出所有已成功建立指定的應用程式執行個體內的服務執行個體。 會列出預設的服務 (若有)。

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## 工作：升級 Service Fabric 應用程式

您可以升級先前部署的 Service Fabric 應用程式。 這項工作會升級先前在「工作：部署 Service Fabric 應用程式」中部署的 WordCount 應用程式。 閱讀 [Service Fabric 應用程式升級](service-fabric-application-upgrade.md) 如需詳細資訊。

### 步驟 1：更新應用程式

變更 WordCount 服務中的程式碼。

在更新之後的服務程式碼，您必須遞增中的服務版本號碼 **ServiceManifest.xml** 檔案 (位於 **PackageRoot** WordCount 專案目錄)。 尋找 **CodePackage** 的資訊清單和變更到 2.0.0 的服務版本的項目。  ServiceManifest.xml 檔案中的對應程式碼行應該會如下所示：

```xml
<ServiceManifest Name="WordCountServicePkg" Version="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="WordCountServiceType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="2.0.0">
      ...
```

現在，您需要更新 ApplicationManifest.xml 檔案 (可於 WordCount 解決方案下的 WordCount 專案中找到)。  更新 **ServiceManifestRef** 要使用 2.0.0.0 版的項目 **WordCountServicePkg** 專案。 也可以更新 **ApplicationTypeVersion** 為 2.0.0.0 從 1.0.0.0。 ApplicationManifest.xml 中的對應程式碼行應該會如下所示：

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="WordCount" ApplicationTypeVersion="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
...
<ServiceManifestRef ServiceManifestName="WordCountServicePkg" ServiceManifestVersion="2.0.0" />
```

進行這些變更之後，儲存檔案並重建 WordCount 專案。 更新應用程式現在封裝 WordCount 專案上按一下滑鼠右鍵，然後選擇 **封裝**。 這應該會建立可部署的應用程式封裝。 更新的應用程式已準備好進行部署。

### 步驟 2：複製和註冊已更新的應用程式封裝

現在應用程式已建置，並且準備好進行升級。 如果您開啟 PowerShell 視窗，系統管理員，並輸入 [**Get-servicefabricapplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), ，您應該會看到部署 1.0.0 版的 WordCount 應用程式類型。  如 WordCount 範例，在中找到的應用程式封裝: *C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug*。

現在將更新的應用程式封裝複製到 Service Fabric 映像存放區 (Service Fabric 在其中儲存應用程式封裝)。 參數 **ApplicationPackagePathInImageStore** 會通知 Service Fabric 可以在哪裡找到應用程式封裝。 下列命令將應用程式套件複製到 **WordCountV2** 映像存放區中:  

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2
```

下一步是新版的應用程式向服務網狀架構，可以使用執行 [**Register-servicefabricapplicationtype**](https://msdn.microsoft.com/library/azure/mt125958.aspx) 指令程式:

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

如果此命令不成功，您可能需要重建服務，如步驟 1 中所述。

### 步驟 3：開始升級
各種升級參數、逾時和健全狀況準則可以套用至應用程式升級。 閱讀 [應用程式升級參數](service-fabric-application-upgrade-parameters.md) 和 [升級程序](service-fabric-application-upgrade.md) 深入的文件。 對於此逐步解說，將服務健全狀況評估準則設定為預設值 (及建議值)。 所有服務和執行個體應該都是 _良好_ 在升級之後。  不過，您應增加 **HealthCheckStableDuration** 為 60 秒 (如此服務至少 20 秒之前升級繼續至下一個升級網域是狀況良好)。  也設定 **UpgradeDomainTimeout** 為 1200 秒， **UpgradeTimeout** 為 3000 秒。 最後，設定 **UpgradeFailureAction** 至 **回復**, ，這樣可要求，Service Fabric 會回復為舊版應用程式如果升級期間發生失敗。

您現在可以使用啟動應用程式升級 [**Start-servicefabricapplicationupgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) 指令程式:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

請注意，應用程式名稱與先前部署的 v1.0.0 應用程式名稱相同 (fabric:/WordCount)。 Service Fabric 會使用這個名稱來識別要升級哪一個應用程式。 如果您設定的逾時太短，您可能會遇到逾時失敗訊息，指出此問題。 請參閱 [疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md), ，或增加逾時。

您可以使用，以監視應用程式升級進度 [Service Fabric 總管](service-fabric-visualizing-your-cluster.md), ，或使用 [**Get-servicefabricapplicationupgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) 指令程式:

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

在幾分鐘的時間， [Get-servicefabricapplicationupgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) 指令程式應該會顯示已升級所有升級網域 (完成)。

## 工作：測試 Service Fabric 應用程式

為了撰寫高品質的服務，開發人員必須能夠產生這類不可靠的基礎結構錯誤，才能測試其服務的穩定性。 Service Fabric 讓開發人員可以引發錯誤動作，並且藉由使用混亂和容錯移轉的測試案例以測試失敗情況下的服務。  閱讀 [可測試性概觀](service-fabric-testability-overview.md) 如需詳細資訊。

### 步驟 1：執行混亂測試案例
混亂測試案例會在整個 Service Fabric 叢集中產生錯誤。 此案例會壓縮錯誤，通常是將幾個月或幾年壓縮到幾小時。 交錯錯誤和高錯誤率的組合，可以找到會在其他情形下被遺漏的極端狀況。 下列範例會執行 60 分鐘的混亂測試案例。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### 步驟 2：執行容錯移轉測試案例
容錯移轉測試案例的目標是特定服務資料分割，而不是整個叢集，並且讓其他服務不會受到影響。 案例會在您的商務邏輯執行時，在服務驗證中反覆一連串模擬的錯誤。 服務驗證中若有失敗，表示有需要進一步調查的問題。 容錯移轉測試一次只會引發一個錯誤，不像混亂測試案例，會引發多個錯誤。 下列範例會對下列網狀架構執行容錯移轉測試 60 分鐘：/WordCount/WordCountService service。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Connect-ServiceFabricCluster

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## 工作：移除 Service Fabric 應用程式
您可以刪除已部署應用程式的執行個體，從叢集中移除已佈建的應用程式類型，以及從 ImageStore 移除應用程式封裝。

### 步驟 1：移除應用程式執行個體
當不再需要應用程式執行個體時，您可以永久移除它使用 [**Remove-servicefabricapplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) 指令程式。 這也會自動移除屬於應用程式的所有服務，同時永久移除所有服務狀態。 此作業無法回復，且應用程式狀態無法復原。

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### 步驟 2：取消註冊應用程式類型
當您不再需要特定版本的應用程式類型時，取消使用 [**Unregister-servicefabricapplicationtype**](https://msdn.microsoft.com/library/azure/mt125885.aspx) 指令程式。 正在取消註冊的未使用類型，會釋放映像存放區上應用程式封裝所使用的儲存空間。 只要沒有對應的應用程式進行具現化，或擱置中的應用程式升級進行參考，則應用程式類型即可取消註冊。

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### 步驟 3：移除應用程式封裝
應用程式類型的註冊之後，應用程式封裝可以從映像存放區使用移除 [**Remove-servicefabricapplicationpackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx) 指令程式。

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## 後續步驟
[Service Fabric 應用程式生命週期](service-fabric-application-lifecycle.md)

[部署應用程式](service-fabric-deploy-remove-applications.md)

[應用程式升級](service-fabric-application-upgrade.md)

[Azure Service Fabric Cmdlet](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Azure Service Fabric 可測試性 Cmdlet](https://msdn.microsoft.com/library/azure/mt125844.aspx)

