---
title: Az Application Insights beállítása a PowerShell segítségével az Azure-ban | Microsoft Docs
description: Az Azure Diagnostics konfigurálásának automatizálása az Application Insights felé való továbbításra.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 4ac803a8-f424-4c0c-b18f-4b9c189a64a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/17/2015
ms.author: mbullwin
ms.openlocfilehash: 3c0decaa89b4ecc503157a32fcb1e5b4d249ccfb
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317781"
---
# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Az Application Insights beállítása a PowerShell segítségével Azure-webalkalmazáshoz

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A [Microsoft Azure](https://azure.com) [konfigurálható úgy, hogy az Azure Diagnostics adatait elküldje](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) az [Azure Application Insights ](../../azure-monitor/app/app-insights-overview.md) szolgáltatásba. A diagnosztika az Azure Cloud Services szolgáltatáshoz és az Azure virtuális gépekhez kapcsolódik. Kiegészíti az alkalmazáson belülről az Application Insights SDK használatával küldött telemetriát. Az új erőforrások Azure-ban való létrehozási folyamatának részeként konfigurálhatja a diagnosztikát a PowerShell segítségével.

## <a name="azure-template"></a>Azure-sablon
Ha a webalkalmazás az Azure-ban található és Azure Resource Manager-sablonnal hozza létre az erőforrásait, az Application Insights konfigurálásához hozzáadhatja ezt az erőforrások csomóponthoz:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource`– az Application Insights-erőforrás neve
* `myWebAppName` – a webalkalmazás azonosítója

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy felhőszolgáltatás telepítésének részeként
A `New-AzureDeployment` parancsmag rendelkezik egy `ExtensionConfiguration` paraméterrel, amely egy diagnosztikakonfigurációs tömböt foglal magába. Ez a `New-AzureServiceDiagnosticsExtensionConfig` parancsmag segítségével hozható létre. Példa:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
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

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy meglévő felhőszolgáltatáson
Meglévő szolgáltatáson használja a következőt: `Set-AzureServiceDiagnosticsExtension`.

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzStorageContext `
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

## <a name="get-current-diagnostics-extension-configuration"></a>Az aktuális diagnosztikai bővítmény konfigurációjának lekérése
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>A diagnosztikai bővítmény eltávolítása
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Ha a Szerepkör paraméter nélkül engedélyezte a diagnosztikai bővítményt a `Set-AzureServiceDiagnosticsExtension` vagy a `New-AzureServiceDiagnosticsExtensionConfig` használatával, akkor a bővítményt a Szerepkör paraméter nélküli `Remove-AzureServiceDiagnosticsExtension` segítségével távolíthatja el. Ha használta a Szerepkör paramétert a bővítmény engedélyezésekor, akkor a bővítmény eltávolításakor is használnia kell.

A diagnosztika bővítmény egyes szerepkörökből való eltávolítása:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Lásd még
* [Azure Cloud Services alkalmazások figyelése az Application Insights segítségével](../../azure-monitor/app/cloudservices.md)
* [Az Azure Diagnostics küldése az Application Insights-ba](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Riasztások konfigurálásának automatizálása](powershell-alerts.md)

