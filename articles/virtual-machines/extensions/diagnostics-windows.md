---
title: Az Azure PowerShell használata a windowsos virtuális gépek diagnosztikának engedélyezéséhez
services: virtual-machines-windows
documentationcenter: ''
description: Megtudhatja, hogy miként engedélyezheti az Azure Diagnosztika használatát a Windows rendszert futtató virtuális gépeken a PowerShell használatával
author: mimckitt
manager: gwallace
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2015
ms.author: mimckitt
ms.openlocfilehash: 16e1dba8c430a5c1e1d1d69910b8ed2c8d0b8138
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262842"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Az Azure Diagnosztika engedélyezése a PowerShell használatával Windows rendszert futtató virtuális gépen

Az Azure Diagnostics az Azure-on belüli képesség, amely lehetővé teszi a diagnosztikai adatok gyűjtését egy telepített alkalmazáson. A diagnosztikai bővítmény segítségével diagnosztikai adatokat, például alkalmazásnaplókat vagy teljesítményszámlálókat gyűjthet egy Windows rendszert futtató Azure virtuális gépről (VM). 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>A diagnosztikai bővítmény engedélyezése, ha az Erőforrás-kezelő telepítési modelljét használja
Engedélyezheti a diagnosztikai bővítményt, miközben létrehoz egy Windows virtuális gép az Azure Resource Manager telepítési modellen keresztül a bővítmény konfigurációját az Erőforrás-kezelő sablonhoz. Lásd: Windows virtuális gép létrehozása [figyeléssel és diagnosztikával az Azure Resource Manager sablon használatával.](diagnostics-template.md)

A diagnosztikai bővítmény engedélyezéséhez egy meglévő virtuális gép, amely az Erőforrás-kezelő telepítési modellen keresztül létrehozott, használhatja a [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiagnosticsextension) PowerShell-parancsmag, az alábbiak szerint.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* az XML-ben a diagnosztikai konfigurációt tartalmazó fájl elérési útja, az alábbi [mintában leírtak](#sample-diagnostics-configuration) szerint.  

Ha a diagnosztikai konfigurációs fájl egy **storageAccount-elemet** ad meg tárfióknévvel, akkor a *Set-AzVMDiagnosticsExtension* parancsfájl automatikusan beállítja a diagnosztikai bővítményt, hogy diagnosztikai adatokat küldjön az adott tárfiókba. Ahhoz, hogy ez működjön, a tárfióknak ugyanabban az előfizetésben kell lennie, mint a virtuális gépnek.

Ha a diagnosztikai konfigurációban nincs **megadva StorageAccount,** akkor a *StorageAccountName* paraméterben át kell adnia a parancsmagnak. Ha a *StorageAccountName* paraméter meg van adva, akkor a parancsmag mindig a paraméterben megadott tárfiókot fogja használni, nem pedig a diagnosztikai konfigurációs fájlban megadottat.

Ha a diagnosztikai tárfiók a virtuális géptől eltérő előfizetésben van, akkor explicit módon át kell adnia a *StorageAccountName* és *storageAccountKey* paramétereket a parancsmagnak. A *StorageAccountKey* paraméter nem szükséges, ha a diagnosztikai tárfiók ugyanabban az előfizetésben van, mivel a parancsmag automatikusan lekérdezheti és beállíthatja a kulcs értékét a diagnosztikai bővítmény engedélyezésekor. Ha azonban a diagnosztikai tárfiók egy másik előfizetésben van, akkor előfordulhat, hogy a parancsmag nem tudja automatikusan beszerezni a kulcsot, és explicit módon meg kell adnia a kulcsot a *StorageAccountKey* paraméteren keresztül.  

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Miután a diagnosztikai bővítmény engedélyezve van a virtuális gépen, a [Get-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiagnosticsextension) parancsmag használatával lejuthat az aktuális beállításokkal.

    Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

A parancsmag visszaadja a *PublicSettings*-t, amely a diagnosztikai konfigurációt tartalmazza. Kétféle konfiguráció támogatott, WadCfg és xmlCfg. A WadCfg JSON-konfiguráció, az xmlCfg pedig Base64-kódolású XML-konfiguráció. Az XML olvasásához dekódolnia kell azt.

    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

Az [Remove-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdiagnosticsextension) parancsmag használható a diagnosztikai bővítmény eltávolításához a virtuális gépről.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>A diagnosztikai bővítmény engedélyezése, ha a klasszikus telepítési modellt használja

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A [Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) parancsmag használatával engedélyezheti a diagnosztikai bővítményt egy virtuális gépen, amelyet a klasszikus üzembe helyezési modellen keresztül hoz létre. A következő példa bemutatja, hogyan hozhat létre egy új virtuális gép a klasszikus üzembe helyezési modell a diagnosztikai bővítmény engedélyezve van.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM

A diagnosztikai bővítmény engedélyezéséhez egy meglévő virtuális gép, amely a klasszikus üzembe helyezési modell en keresztül jött létre, először használja a [Get-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurevm) parancsmag a virtuális gép konfigurációjának lehívásához. Ezután frissítse a virtuális gép konfigurációját, hogy tartalmazza a diagnosztikai bővítményt a [Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) parancsmag használatával. Végül alkalmazza a frissített konfigurációt a virtuális gépre az [Update-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/update-azurevm)használatával.

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Mintadiagnosztika konfigurációja
A következő XML használható a diagnosztika nyilvános konfiguráció a fenti parancsfájlok. Ez a mintakonfiguráció különböző teljesítményszámlálókat továbbít a diagnosztikai tárfiókba, valamint a Windows eseménynaplóiban az alkalmazásból, a biztonságból és a rendszercsatornákból származó hibákat, valamint a diagnosztikai infrastruktúra naplóiból származó hibákat.

A konfigurációt frissíteni kell, hogy tartalmazza a következőket:

* A **metrikaelem** *erőforrás-azonosító* attribútuma frissíteni kell a virtuális gép erőforrás-azonosítójával.
  
  * Az erőforrás-azonosító a következő minta használatával alakítható ki: "/subscriptions/{*subscription id az előfizetéshez a VM*}/resourceGroups/{ A vm }/providers}/Microsoft.Compute/virtualMachines/{*A virtuális gép neve*}"*erőforráscsoport neve.*
  * Ha például a virtuális gép futtatásával működő előfizetés előfizetési azonosítója **11111111-1111-1111-11111111111111111,** az erőforráscsoport erőforráscsoport neve **MyResourceGroup**, a virtuálisgép neve **pedig MyWindowsVM,** akkor az *erőforrásazonosító* értéke a következő lesz:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * A teljesítményszámlálók és a metrikák konfigurációja alapján létrehozott metrikák létrehozásáról az [Azure Diagnostics metrics táblázata a storage-ban](diagnostics-template.md#wadmetrics-tables-in-storage)című témakörben talál további információt.
* A **StorageAccount** elemet frissíteni kell a diagnosztikai tárfiók nevével.
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>További lépések
* Az Azure Diagnostics funkció és a problémák elhárításához szükséges egyéb technikák használatával kapcsolatban a [Diagnosztika engedélyezése az Azure Felhőszolgáltatásokban és a virtuális gépekben](../../cloud-services/cloud-services-dotnet-diagnostics.md)című témakörben talál további útmutatást.
* [A diagnosztikai konfigurációk sémája](https://msdn.microsoft.com/library/azure/mt634524.aspx) ismerteti a diagnosztikai bővítmény különböző XML-konfigurációs beállításait.

