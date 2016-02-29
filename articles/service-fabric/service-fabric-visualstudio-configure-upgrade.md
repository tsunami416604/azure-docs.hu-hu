<properties
   pageTitle="設定 Service Fabric 應用程式的升級 | Microsoft Azure"
   description="了解如何使用 Microsoft Visual Studio 來設定升級 Service Fabric 應用程式的設定。"
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/08/2015"
   ms.author="cawaMS" />

# 在 Visual Studio 中設定 Service Fabric 應用程式的升級

「Visual Studio Service Fabric 工具」提供發行至本機或遠端叢集的升級支援。 在進行測試和偵錯的期間，將應用程式升級至較新版本而不取代應用程式有兩個優點：首先，應用程式資料在升級期間不會遺失；其次，可達到高可用性，因為如果已在所有升級網域散佈足夠的服務執行個體，在升級期間就不會發生任何服務中斷。 在應用程式進行升級時，可以對該應用程式進行測試。 

## 升級所需的參數

您可以選擇的部署有兩種： 一般或升級。 一般部署會將叢集上所有先前的部署資訊和資料都清除，而升級部署則會將其保留。 當您在 Visual Studio 中升級 Service Fabric 應用程式時，您需要提供應用程式升級參數和健康情況檢查原則。 應用程式升級參數可協助控制升級，而健康情況檢查原則則可判斷升級是否成功。 請參閱 [Service Fabric 應用程式升級: 升級參數](service-fabric-application-upgrade-parameters.md) 如需詳細資訊。

有三種升級模式: *監視*, ，*UnmonitoredAuto*, ，和 *手動*。

  - A *監視* 升級會自動升級和應用程式健全狀況檢查。

  - * UnmonitoredAuto* 升級會自動進行升級，但會略過應用程式健康情況檢查。

  - 當您執行 *手動* 升級，您必須先手動升級每個升級網域。

每一種升級模式都需要一組不同的參數。 請參閱 [應用程式升級參數](service-fabric-application-upgrade-parameters.md) 若要深入了解可用的升級選項。

## 在 Visual Studio 中升級 Service Fabric 應用程式

如果您使用 Visual Studio Service Fabric 的工具來升級 Service Fabric 應用程式，您可以指定要升級，而不是標準的部署方式是檢查發行程序 **升級應用程式** 核取方塊。

### 設定升級參數

1. 按一下 [ **設定** 按鈕旁邊的核取方塊。  **編輯升級參數** ] 對話方塊隨即出現。  **編輯升級參數** 對話方塊支援 *監視*, ，*UnmonitoredAuto* 和 *UnmonitoredManual* 升級模式。

2. 選取您想要使用的升級模式，然後填寫參數方格。

    每個參數都有預設值。 選擇性參數 *DefaultServiceTypeHealthPolicy* 參數會使用雜湊資料表輸入。 以下是範例的雜湊資料表輸入格式 *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```
    
    *ServiceTypeHealthPolicyMap* 是採用下列格式的雜湊資料表輸入另一個選擇性參數:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```


    Here's a real-life example:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```

3. 如果您選取 *UnmonitoredManual* 升級模式，您必須手動啟動 PowerShell 主控台中，以繼續並完成升級程序。 請參閱 [Service Fabric 應用程式升級: 進階主題](service-fabric-application-upgrade-advanced.md) 若要了解如何手動升級的運作方式

## 使用 PowerShell 升級應用程式

您可以使用 PowerShell Cmdlet 來升級 Service Fabric 應用程式。 請參閱 [服務網狀架構應用程式升級教學課程](service-fabric-application-upgrade-tutorial.md) 和 [Start-servicefabricapplicationupgrade](https://msdn.microsoft.com/library/mt125975.aspx) 如需詳細資訊。

## 在應用程式資訊清單檔案中指定健康情況檢查原則

Service Fabric 應用程式中的每個服務都可以有自己的健康情況原則參數來覆寫預設值。 您可以在「應用程式資訊清單」檔案中提供這些參數值。

下列範例示範如何在應用程式清單中套用每個服務的唯一健康情況檢查原則。

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## 後續步驟
如需有關部署應用程式的詳細資訊，請參閱 [部署現有的應用程式在 Azure Service Fabric](service-fabric-deploy-existing-app.md)。


