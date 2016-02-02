<properties 
    pageTitle="使用 PowerShell 在 Azure 雲端服務中啟用診斷 | Microsoft Azure" 
    description="了解如何使用 PowerShell 啟用雲端服務的診斷" 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="sbtron" 
    manager="" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="saurabh"/>



# 使用 PowerShell 在 Azure 雲端服務中啟用診斷

您可以使用 Azure 診斷延伸模組，從雲端服務收集診斷資料 (例如應用程式記錄檔、效能計數器等)。 本文描述如何使用 PowerShell 啟用雲端服務的 Azure 診斷延伸模組。 請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md) 的這篇文章所需的必要條件。

## 啟用診斷延伸模組做為部署雲端服務的一部分

這種適合連續整合案例啟用診斷延伸模組的位置類型。您可以啟用診斷延伸模組傳遞來部署雲端服務的一部分 *ExtensionConfiguration* 參數 [新增 AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) 指令程式。  *ExtensionConfiguration* 參數會使用陣列的建立，可使用的診斷組態 [新增 AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) 指令程式。

下列範例示範如何為某個雲端服務 (其中的 WebRole 和 WorkerRole 各自擁有不同的診斷組態) 啟用診斷。

    $service_name = "MyService"
    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    
    $primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey
    
    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -Storage_context $storageContext -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath
    
    
    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig) 

## 在現有的雲端服務上啟用診斷延伸模組

您可以使用 [Set-azureservicediagnosticsextension](https://msdn.microsoft.com/library/azure/mt589140.aspx) 指令程式來啟用診斷功能已在執行中的雲端服務上。


    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $webrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WorkerRole"

## 取得目前的診斷延伸模組組態

使用 [Set-azureservicediagnosticsextension](https://msdn.microsoft.com/library/azure/mt589204.aspx) cmdlet 來取得雲端服務的目前診斷組態。

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## 移除診斷延伸模組

若要關閉雲端服務，您可以使用診斷 [Remove-azureservicediagnosticsextension](https://msdn.microsoft.com/library/azure/mt589183.aspx) 指令程式。

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

如果您在未使用 *Role* 參數的情況下使用 *Set-AzureServiceDiagnosticsExtension* 或 *New-AzureServiceDiagnosticsExtensionConfig* 啟用診斷延伸模組，則您可以在未使用 *Role* 參數的情況下使用 *Remove-AzureServiceDiagnosticsExtension* 移除延伸模組。 如果啟用延伸模組時使用了 *Role* 參數，則移除延伸模組時也必須使用該參數。

若要從每個個別的角色移除診斷延伸模組：

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"

## 後續步驟

- 如需使用 Azure 診斷和其他技術來疑難排解問題的其他指引，請參閱 [在 Azure 雲端服務和虛擬機器中啟用診斷](cloud-services-dotnet-diagnostics.md)。
- [診斷組態結構描述](https://msdn.microsoft.com/library/azure/dn782207.aspx) 說明診斷擴充功能的各種 xml 組態選項。
- 若要了解如何啟用診斷擴充功能的虛擬機器，請參閱 [地監視與診斷使用 Azure 資源管理員範本建立 Windows 虛擬機器](virtual-machines-extensions-diagnostics-windows-template.md)







