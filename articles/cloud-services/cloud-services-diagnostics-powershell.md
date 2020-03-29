---
title: Diagnosztika engedélyezése az Azure Cloud Servicesben a PowerShell használatával | Microsoft dokumentumok
description: Megtudhatja, hogy miként engedélyezheti a felhőszolgáltatások diagnosztikát a PowerShell használatával
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: tagore
ms.openlocfilehash: 76cdffed813fd182980b36f848e0ae42f3226539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386544"
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Diagnosztika engedélyezése az Azure Cloud Servicesben a PowerShell használatával
Diagnosztikai adatokat, például alkalmazásnaplókat, teljesítményszámlálókat stb. Ez a cikk ismerteti, hogyan engedélyezheti az Azure Diagnostics bővítményt egy felhőalapú szolgáltatás powershell használatával.  Olvassa [el az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview) a cikkhez szükséges előfeltételekhez.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy felhőszolgáltatás telepítésének részeként
Ez a megközelítés a forgatókönyvek folyamatos integrációs típusára alkalmazható, ahol a diagnosztikai bővítmény engedélyezhető a felhőszolgáltatás üzembe helyezésének részeként. Új felhőszolgáltatás-telepítés létrehozásakor engedélyezheti a diagnosztikai bővítményt az *ExtensionConfiguration* paraméter nek az [Új-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-3.7.0) parancsmagnak való átadásával. Az *ExtensionConfiguration* paraméter a [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) parancsmag használatával létrehozható diagnosztikai konfigurációk tömbjét veszi igénybe.

A következő példa bemutatja, hogyan engedélyezheti a webrole és workerrole felhőalapú szolgáltatások diagnosztikát, amelyek mindegyike más-más diagnosztikai konfigurációval rendelkezik.

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

Ha a diagnosztikai konfigurációs `StorageAccount` fájl egy tárfiók névvel `New-AzureServiceDiagnosticsExtensionConfig` rendelkező elemet ad meg, akkor a parancsmag automatikusan ezt a tárfiókot fogja használni. Ahhoz, hogy ez működjön, a tárfióknak ugyanabban az előfizetésben kell lennie, mint a telepített felhőszolgáltatásnak.

Az Azure SDK 2.6-tól kezdve az MSBuild közzétételi célkimenet által létrehozott bővítmény konfigurációs fájlok tartalmazzák a tárfiók nevét a szolgáltatás konfigurációs fájljában (.cscfg) megadott diagnosztikai konfigurációs karakterlánc alapján. Az alábbi parancsfájl bemutatja, hogyan elemezheti a bővítmény konfigurációs fájljait a közzétételi célkimenetből, és konfigurálhatja a diagnosztikai bővítményt az egyes szerepkörökhöz a felhőszolgáltatás üzembe helyezésekor.

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

A Visual Studio Online hasonló megközelítést alkalmaz a felhőszolgáltatások diagnosztikai bővítményt tartalmazó automatikus üzembe helyezéséhez. A teljes példát a [Publish-AzureCloudDeployment.ps1 című](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) témakörben talál.

Ha `StorageAccount` a diagnosztikai konfigurációban nem volt megadva, akkor a *StorageAccountName* paraméterben át kell adnia a parancsmagnak. Ha a *StorageAccountName* paraméter meg van adva, akkor a parancsmag mindig a paraméterben megadott tárfiókot fogja használni, nem pedig a diagnosztikai konfigurációs fájlban megadottat.

Ha a diagnosztikai tárfiók a felhőszolgáltatástól eltérő előfizetésben van, akkor explicit módon át kell adnia a *StorageAccountName* és *storageAccountKey* paramétereket a parancsmagnak. A *StorageAccountKey* paraméter nem szükséges, ha a diagnosztikai tárfiók ugyanabban az előfizetésben van, mivel a parancsmag automatikusan lekérdezheti és beállíthatja a kulcs értékét a diagnosztikai bővítmény engedélyezésekor. Ha azonban a diagnosztikai tárfiók egy másik előfizetésben van, akkor előfordulhat, hogy a parancsmag nem tudja automatikusan beszerezni a kulcsot, és explicit módon meg kell adnia a kulcsot a *StorageAccountKey* paraméteren keresztül.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy meglévő felhőszolgáltatáson
A [Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) parancsmag használatával engedélyezheti vagy frissítheti a diagnosztikai konfigurációt egy már futó felhőszolgáltatáson.

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
A [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) parancsmag használatával lekéri a felhőszolgáltatás aktuális diagnosztikai konfigurációját.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>A diagnosztikai bővítmény eltávolítása
A felhőalapú szolgáltatás diagnosztika kikapcsolására használhatja az [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) parancsmag.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Ha engedélyezte a diagnosztikai bővítményt a *Set-AzureServiceDiagnosticsExtension* vagy a *New-AzureServiceDiagnosticsExtensionconfig* használatával a *szerepkör* paraméter nélkül, akkor eltávolíthatja a bővítményt az *Remove-AzureServiceDiagnosticsExtension* használatával a *Szerepkör* paraméter nélkül. Ha a *szerepkör* paramétert használták a bővítmény engedélyezésekor, akkor azt is használni kell a bővítmény eltávolításakor.

A diagnosztika bővítmény egyes szerepkörökből való eltávolítása:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Következő lépések
* Az Azure-diagnosztika és a problémák elhárításához szükséges egyéb technikák használatával kapcsolatos további útmutatásért olvassa el [a Diagnosztika engedélyezése az Azure Felhőszolgáltatásokban és a virtuális gépekben című témakört.](cloud-services-dotnet-diagnostics.md)
* A [diagnosztikai konfigurációs séma](/azure/azure-monitor/platform/diagnostics-extension-schema-1dot3) ismerteti a diagnosztikai bővítmény különböző xml-konfigurációs beállításait.
* A virtuális gépek diagnosztikai bővítményének engedélyezéséről a [Windows virtuális gép létrehozása figyeléssel és diagnosztikával az Azure Resource Manager-sablon használatával című](../virtual-machines/windows/extensions-diagnostics-template.md) témakörben olvashat.



