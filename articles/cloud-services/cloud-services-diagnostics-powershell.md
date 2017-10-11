---
title: "A PowerShell használata Azure Cloud Services diagnosztika engedélyezése |} Microsoft Docs"
description: "Útmutató: a PowerShell használatával felhőszolgáltatások diagnosztika engedélyezése"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 66e08754-8639-4022-ae18-4237749ba17d
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/06/2016
ms.author: adegeo
ms.openlocfilehash: 8dd9724981860c9cd4ccc443cc2bfdc465811e7c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>A PowerShell használata Azure Cloud Services diagnosztika engedélyezése
Diagnosztikai adatok alkalmazásnaplók, például begyűjtheti a teljesítményszámlálók stb az egy Felhőszolgáltatás, az Azure Diagnostics kiterjesztés használatával. Ez a cikk ismerteti az Azure Diagnostics-bővítmény engedélyezése egy felhőalapú szolgáltatás PowerShell használatával.  Lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) esetében ez a cikk szükséges előfeltételeket.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy felhőszolgáltatás telepítésének részeként
Ezt a módszert olyan forgatókönyvekben, ahol a diagnosztika bővítmény telepítése a felhőalapú szolgáltatás részeként engedélyezhető folyamatos integrációt típusú alkalmazható. Új felhőalapú szolgáltatás központi telepítés létrehozásakor a sikeres a diagnosztika bővítmény engedélyezéséhez a *ExtensionConfiguration* paramétert a [New-AzureDeployment](/powershell/module/azure/new-azuredeployment?view=azuresmps-3.7.0) parancsmag. A *ExtensionConfiguration* paraméter a tömb diagnosztika konfigurációk használatával hozható létre a [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/azure/new-azureservicediagnosticsextensionconfig?view=azuresmps-3.7.0) parancsmag.

A következő példa bemutatja, hogyan engedélyezheti a diagnosztika a webrole típusról és WorkerRole, egy másik diagnosztikai konfigurációja rendelkezik egy felhőalapú szolgáltatás.

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

Ha a diagnosztika konfigurációs fájl határozza meg a `StorageAccount` elemet a tárfiók neve, majd a `New-AzureServiceDiagnosticsExtensionConfig` parancsmag automatikusan használja a tárfiók. Ennek működéséhez a tárfiók eltérőnek kell lennie a felhőalapú szolgáltatásként telepített ugyanahhoz az előfizetéshez.

Azure SDK 2.6 meghajtóbetűjeltől közzé az MSBuild generált bővítmény konfigurációs fájlokban kimenet tartalmazza a tárfiók nevét a szolgáltatás konfigurációs (.cscfg) fájljában megadott diagnosztika konfigurációs karakterlánc alapján. Az alábbi parancsfájl bemutatja, hogyan a közzététel kimenet bővítmény konfigurációs fájl elemzése és diagnosztika bővítményének beállítását a minden egyes szerepkör a felhőalapú szolgáltatás telepítésekor.

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

A Visual Studio Online használja a hasonló megközelítés diagnosztika kiterjesztésű a Felhőszolgáltatások automatikus központi telepítéséhez. Lásd: [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) teljes például.

Ha nincs `StorageAccount` adott a diagnosztika konfigurációban, akkor kell átadni a *StorageAccountName* paramétert a parancsmaghoz. Ha a *StorageAccountName* paraméter meg van adva, akkor a parancsmag mindig használja a tárfiók a paraméter, a nem a másik a diagnosztika konfigurációs fájlban megadott.

Ha a diagnosztikai tárfiók egy másik előfizetésben található felhőalapú szolgáltatásából, akkor meg kell átadni explicit módon a *StorageAccountName* és *StorageAccountKey* a parancsmag paramétereit. A *StorageAccountKey* paraméter nincs szükség esetén a diagnosztikai tárfiók ugyanahhoz az előfizetéshez, a parancsmag automatikusan lekérdezési és állítható be a kulcs értékét, ha a diagnosztika bővítmény engedélyezése. Azonban ha a diagnosztikai tárfiók egy másik előfizetésben található, akkor a parancsmag nem feltétlenül tudja automatikusan a kulcs eléréséhez, és kell explicit módon adja meg a kulcsot keresztül a *StorageAccountKey* paraméter.

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>A diagnosztikai bővítmény engedélyezése egy meglévő felhőszolgáltatáson
Használhatja a [Set-AzureServiceDiagnosticsExtension](/powershell/module/azure/set-azureservicediagnosticsextension?view=azuresmps-3.7.0) parancsmag engedélyezéséhez, vagy egy Felhőszolgáltatás, amely már fut a diagnosztikai konfiguráció frissítése.

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
Használja a [Get-AzureServiceDiagnosticsExtension](/powershell/module/azure/get-azureservicediagnosticsextension?view=azuresmps-3.7.0) parancsmagot, hogy megkapja az aktuális diagnosztikai konfiguráció egy felhőalapú szolgáltatás.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>A diagnosztikai bővítmény eltávolítása
Egy felhőszolgáltatás, használhatja a diagnosztika kikapcsolása a [Remove-AzureServiceDiagnosticsExtension](/powershell/module/azure/remove-azureservicediagnosticsextension?view=azuresmps-3.7.0) parancsmag.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Ha engedélyezte a diagnosztikai bővítmény használatával *Set-AzureServiceDiagnosticsExtension* vagy a *New-AzureServiceDiagnosticsExtensionConfig* nélkül a *szerepkör* paraméter, akkor eltávolíthatja a bővítmény használatával *Remove-AzureServiceDiagnosticsExtension* nélkül a *szerepkör* paraméter. Ha a *szerepkör* paraméter lett megadva, ha engedélyezi a bővítményt, akkor azt is használható a bővítmény eltávolításakor.

A diagnosztika bővítmény egyes szerepkörökből való eltávolítása:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Következő lépések
* Hibaelhárítás az Azure diagnostics és az egyéb technikák segítségével további útmutatást lásd: [diagnosztika engedélyezésével az Azure Cloud Services és a virtuális gépek](cloud-services-dotnet-diagnostics.md).
* A [diagnosztika konfigurációs séma](https://msdn.microsoft.com/library/azure/dn782207.aspx) a diagnosztika bővítmény különböző xml konfigurációk lehetőségeit ismerteti.
* Engedélyezi a diagnosztika bővítményt a virtuális gépek, lásd: [Windows virtuális gép létrehozása a figyelési és -diagnosztika Azure Resource Manager-sablon](../virtual-machines/windows/extensions-diagnostics-template.md)
