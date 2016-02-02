<properties
    pageTitle="使用 PowerShell 將 Azure 診斷傳送至 Application Insights | Microsoft Azure"
    description="自動設定 Azure 診斷以透過管道傳送至 Application Insights。"
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>


# 使用 PowerShell 將 Azure 診斷傳送至 Application Insights

[Microsoft Azure](https://azure.com) 可以是 [設定為傳送 Azure 診斷](app-insights-azure-diagnostics.md) 至 [Visual Studio Application Insights](app-insights-overview.md)。 診斷與 Azure 雲端服務和 Azure VM 相關。 可輔助您從應用程式使用 Application Insights SDK 傳送的遙測資料。 在 Azure 中自動建立新資源的程序中，您可以使用 PowerShell 設定診斷。

## 啟用診斷延伸模組做為部署雲端服務的一部分

`新增 AzureDeployment` cmdlet 具有一個參數 `ExtensionConfiguration`, ，這會將診斷組態的陣列。 可以建立使用 `新增 AzureServiceDiagnosticsExtensionConfig` 指令程式。 例如：

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

## 在現有的雲端服務上啟用診斷延伸模組

在現有的服務，使用 `Set-azureservicediagnosticsextension`。

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## 取得目前的診斷延伸模組組態

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## 移除診斷延伸模組

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

如果您啟用診斷延伸模組使用 `Set-azureservicediagnosticsextension` 或 `新增 AzureServiceDiagnosticsExtensionConfig` 沒有角色參數中，您就可以移除延伸模組使用 `Remove-azureservicediagnosticsextension` 沒有角色參數。 如果啟用延伸模組時使用了 Role 參數，則移除延伸模組時也必須使用該參數。

若要從每個個別的角色移除診斷延伸模組：

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## 相關主題

* [監視 Azure 雲端服務使用 Application Insights 的應用程式](app-insights-cloudservices.md)
* [Azure 診斷傳送至 Application Insights](app-insights-azure-diagnostics.md)






