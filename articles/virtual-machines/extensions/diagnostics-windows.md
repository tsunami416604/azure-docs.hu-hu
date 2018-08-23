---
title: Az Azure PowerShell használatával engedélyezze a diagnosztikát a virtuális gép Windows |} A Microsoft Docs
services: virtual-machines-windows
documentationcenter: ''
description: Ismerje meg a Windows rendszerű virtuális gép Azure-diagnosztika engedélyezése a PowerShell használatával
author: sbtron
manager: jeconnoc
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 2a4f55ea15c933094befb8855185c4b7e353dee3
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054068"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Az Azure Diagnostics használatának engedélyezése a PowerShell-lel virtual windowsos virtuális gépen

Az Azure Diagnostics a funkció, amely lehetővé teszi az üzembe helyezett alkalmazás diagnosztikai adatgyűjtés Azure-on belül. A diagnosztikai bővítmény segítségével egy Azure virtuális gép (VM), amelyen fut a Windows mint alkalmazásnaplókat vagy a teljesítményszámlálók diagnosztikai adatainak összegyűjtése. Ez a cikk ismerteti a diagnosztikai bővítmény engedélyezése egy virtuális géphez a Windows PowerShell használatával. Lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview) esetében ez a cikk szükséges előfeltételeket.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>A diagnosztikai bővítmény engedélyezése, ha a Resource Manager üzemi modell
Az Azure Resource Manager-alapú üzemi modellel Windows virtuális gép létrehozásakor a bővítmény konfigurációja a Resource Manager-sablon hozzáadásával engedélyezheti a diagnosztikai bővítményt. Lásd: [Windows virtuális gép létrehozása figyelési és diagnosztikai funkciókkal az Azure Resource Manager-sablon használatával](diagnostics-template.md).

Ahhoz, hogy a diagnosztikai bővítmény egy meglévő virtuális Gépet, amely a Resource Manager-alapú üzemi modellel lett létrehozva, használhatja a [Set-AzureRMVMDiagnosticsExtension](/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension) ahogy az alábbi PowerShell-parancsmagot.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* XML diagnosztikai konfigurációja tartalmazó fájl elérési útját a leírtak szerint a [minta](#sample-diagnostics-configuration) alatt.  

Ha a diagnosztikai konfigurációs fájl határozza meg, egy **StorageAccount** elemet a tárfiók nevének, akkor a *Set-AzureRMVMDiagnosticsExtension* parancsfájl automatikusan beállítja a diagnosztika diagnosztikai adatok küldése az adott storage-fiók bővítményt. Ennek működéséhez a storage-fiókot kell lennie a virtuális géppel azonos előfizetésben található.

Ha nincs **StorageAccount** volt megadva a diagnosztikai konfiguráció, akkor kell megadni a *StorageAccountName* paramétert a parancsmaghoz. Ha a *StorageAccountName* paraméter meg van adva, akkor a parancsmag mindig a paramétert, és nem az egyik a diagnosztikai konfigurációs fájlban megadott megadott storage-fiókot fogja használni.

Ha a diagnosztikai tárfiók a virtuális gépről egy másik előfizetésben található, akkor az explicit módon továbbíthat kell a *StorageAccountName* és *StorageAccountKey* a parancsmag paramétereit. A *StorageAccountKey* paraméter nincs szükség esetén a diagnosztikai tárfiók ugyanahhoz az előfizetéshez tartozik, a parancsmag automatikusan lekérdezése és a kulcs értékét állítsa a diagnosztikai bővítmény engedélyezésekor. Azonban, ha a diagnosztikai tárfiók van egy másik előfizetésben található, akkor a parancsmag nem feltétlenül tudja automatikusan lekérni a kulcsot, és kell explicit módon adja meg a kulcsot keresztül a *StorageAccountKey* paraméter.  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Miután a diagnosztikai bővítményt a virtuális gépen engedélyezve van, az aktuális beállítások használatával megtekintheti a [Get-AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/get-azurermvmdiagnosticsextension) parancsmagot.

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

A parancsmag visszaadja *PublicSettings*, amely tartalmazza a diagnosztikai konfigurációja. Két fajtája támogatott konfiguráció, WadCfg és xmlCfg. WadCfg JSON konfigurációs, és xmlCfg Base64-kódolású formátumú XML-konfiguráció. Olvassa el az XML-fájl, dekódolni kell.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

A [Remove-AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/remove-azurermvmdiagnosticsextension) parancsmag segítségével távolítsa el a diagnosztikai bővítményt a virtuális gépről.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>A diagnosztikai bővítmény engedélyezése, ha a klasszikus üzemi modellben
Használhatja a [Set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) parancsmag használatával a klasszikus üzemi modellel létrehozott virtuális gép egy diagnosztikai bővítmény engedélyezése. Az alábbi példa bemutatja, hogyan hozhat létre egy új virtuális Gépet a klasszikus üzembehelyezési modellel a diagnosztikai bővítmény engedélyezve van.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

A meglévő virtuális gép, amely a klasszikus üzemi modellel lett létrehozva a diagnosztikai bővítmény engedélyezéséhez először használja a [Get-AzureVM](/powershell/module/servicemanagement/azure/get-azurevm) parancsmagot, hogy a virtuális gép konfigurációjának beolvasása. Frissítse a virtuális gép konfigurációjához, hogy tartalmazzák a diagnosztikai bővítményt a [Set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) parancsmagot. Végül alkalmazása a frissített konfigurációt a virtuális gép használatával [Update-AzureVM](/powershell/module/servicemanagement/azure/update-azurevm).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Minta diagnosztikai konfigurációja
A fenti szkriptekkel diagnosztikai nyilvános konfigurációja a következő XML formátumú is használható. Konfigurációs különböző teljesítményszámlálókat átvitele a diagnosztikai tárfiók együtt az alkalmazás, biztonsági és rendszer-csatornák a Windows eseménynaplóiban hibák és a diagnosztikai infrastruktúra naplói hibáit.

A konfigurációs van szüksége, frissíteni kell a következők:

* A *resourceID* attribútuma a **metrikák** elemet hozzá kell a virtuális gép frissíthető az erőforrás-azonosító.
  
  * Az erőforrás-azonosítója a következő minta használatával lehet létrehozni: "/ subscriptions / {*a virtuális géppel az előfizetéshez tartozó előfizetés-azonosító*} /resourceGroups/ {*az erőforráscsoport nevét a virtuális gép*} / providers/Microsoft.Compute/virtualMachines/ {*a virtuális gép neve*} ".
  * Ha például az előfizetés AZONOSÍTÓJÁT az előfizetést, ahol a virtuális gép fut-e **11111111-1111-1111-1111-111111111111**, az erőforráscsoport az erőforráscsoport neve **MyResourceGroup**, és a Virtuális gép neve **MyWindowsVM**, majd az értékét *resourceID* lenne:
    
      ```
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * További információt a metrikák jönnek létre teljesítmény számlálókat és metrikák konfigurációja alapján, lásd: [Azure Diagnostics metrikák table storage-ban](diagnostics-template.md#wadmetrics-tables-in-storage).
* A **StorageAccount** elemet hozzá kell frissíteni a diagnosztikai tárfiók nevére.
  
    ```
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
* További útmutató az Azure Diagnostics funkció és egyéb módszerek használatával kapcsolatos hibák elhárításához, lásd: [Diagnosztikának az Azure Cloud Services és Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Diagnostics-séma konfigurációk](https://msdn.microsoft.com/library/azure/mt634524.aspx) a diagnosztikai bővítményt a különböző XML konfigurációk lehetőségeit ismerteti.

