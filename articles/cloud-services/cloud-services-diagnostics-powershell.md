---
title: Diagnosztika engedélyezése az Azure Cloud Services szolgáltatással a PowerShell használatával |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezze a diagnosztikát a PowerShell használatával a cloud services
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 66e08754-8639-4022-ae18-4237749ba17d
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: jeconnoc
ms.openlocfilehash: b20fa7a1f43369cde85c2535637eec7ceb1d3c29
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918331"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Diagnosztika engedélyezése az Azure Cloud Services szolgáltatással a PowerShell használatával
Alkalmazásnaplók, például a diagnosztikai adatokat gyűjthet a teljesítményszámlálók stb egy felhőalapú szolgáltatásából, az Azure Diagnostics bővítmény használatával. Ez a cikk ismerteti az Azure diagnosztikai bővítmény engedélyezése egy felhőszolgáltatás, PowerShell-lel.  Lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview) esetében ez a cikk szükséges előfeltételeket.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy felhőszolgáltatás telepítésének részeként
Ez a megközelítés olyan forgatókönyvek, ahol a diagnosztikai bővítményt a felhőszolgáltatás telepítésének részeként is engedélyezhető a folyamatos integráció típusú alkalmazható. Egy új felhőalapú szolgáltatás központi telepítés létrehozásakor a diagnosztikai bővítmény megadásával engedélyezheti az *ExtensionConfiguration* paramétert a [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) parancsmagot. A *ExtensionConfiguration* paraméter egy diagnosztikakonfigurációs tömböt foglal magába, amelyek segítségével hozható létre a [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) parancsmagot.

Az alábbi példa bemutatja, hogyan engedélyezheti a WebRole és WorkerRole, egy másik diagnosztikai konfigurációja kellene a felhőszolgáltatások diagnosztikai.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Ha a diagnosztikai konfigurációs fájl határozza meg, egy `StorageAccount` elemet a tárfiók nevének, akkor a `New-AzureServiceDiagnosticsExtensionConfig` parancsmag automatikusan használja a tárfiók. Ennek működéséhez a storage-fiókot kell lennie a felhőalapú szolgáltatásként telepített ugyanabban az előfizetésben.

Az Azure SDK 2.6-os kezdve a bővítmény konfigurációs fájlokat az MSBuild által generált közzétételi cél kimeneti tartalmazza a tárfiók nevét a szolgáltatás konfigurációs (.cscfg) fájljában megadott diagnosztikai konfigurációs karakterlánc alapján. Az alábbi parancsfájl bemutatja, hogyan elemezheti a közzétételi cél kimenetből származó bővítmény konfigurációs fájljait, és konfigurálja az egyes szerepkörökhöz a diagnosztikai bővítmény, a felhőszolgáltatás üzembe helyezésekor.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

A Visual Studio online-hoz hasonló megközelítést alkalmaz a diagnosztikai bővítményt a Felhőszolgáltatások automatikus központi telepítéséhez. Lásd: [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) kapcsolatos átfogó példát.

Ha nincs `StorageAccount` volt megadva a diagnosztikai konfiguráció, akkor kell megadni a *StorageAccountName* paramétert a parancsmaghoz. Ha a *StorageAccountName* paraméter meg van adva, akkor a parancsmag mindig a paramétert, és nem az egyik a diagnosztikai konfigurációs fájlban megadott megadott storage-fiókot fogja használni.

Ha a diagnosztikai tárfiók felhőalapú szolgáltatásából egy másik előfizetésben található, akkor az explicit módon továbbíthat kell a *StorageAccountName* és *StorageAccountKey* a parancsmag paramétereit. A *StorageAccountKey* paraméter nincs szükség esetén a diagnosztikai tárfiók ugyanahhoz az előfizetéshez tartozik, a parancsmag automatikusan lekérdezése és a kulcs értékét állítsa a diagnosztikai bővítmény engedélyezésekor. Azonban, ha a diagnosztikai tárfiók van egy másik előfizetésben található, akkor a parancsmag nem feltétlenül tudja automatikusan lekérni a kulcsot, és kell explicit módon adja meg a kulcsot keresztül a *StorageAccountKey* paraméter.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy meglévő felhőszolgáltatáson
Használhatja a [Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) parancsmagot, engedélyezéséhez, vagy egy Felhőszolgáltatás, amely már fut a diagnostics konfigurációjának frissítése.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Az aktuális diagnosztikai bővítmény konfigurációjának lekérése
Használja a [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) -parancsmaggal beolvasható az aktuális diagnosztikai konfiguráció a felhőszolgáltatás használatához.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>A diagnosztikai bővítmény eltávolítása
Egy felhőszolgáltatás, használhatja a diagnosztika kikapcsolása az [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) parancsmagot.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Ha engedélyezte a diagnosztikai bővítmény használatával *Set-AzureServiceDiagnosticsExtension* vagy a *New-AzureServiceDiagnosticsExtensionConfig* nélkül a *szerepkör*paramétert, akkor eltávolíthatja a bővítmény használatával *Remove-AzureServiceDiagnosticsExtension* nélkül a *szerepkör* paraméter. Ha a *szerepkör* paraméter lett megadva, amikor engedélyezi a bővítményt, akkor azt is használnia kell a bővítmény eltávolításakor.

A diagnosztika bővítmény egyes szerepkörökből való eltávolítása:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>További lépések
* További útmutató az Azure diagnostics és az egyéb módszerek használatával kapcsolatos hibák elhárításához, lásd: [Diagnosztikának az Azure Cloud Services és Virtual Machines](cloud-services-dotnet-diagnostics.md).
* A [diagnosztikai konfigurációs séma](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot2) a diagnosztikai bővítményt a különböző xml konfigurációk lehetőségeit ismerteti.
* A diagnosztikai bővítmény engedélyezése a virtuális gépek kezelésével kapcsolatos információkért lásd: [Windows virtuális gép létrehozása figyelési és diagnosztikai funkciókkal, az Azure Resource Manager-sablon használatával](../virtual-machines/windows/extensions-diagnostics-template.md)
