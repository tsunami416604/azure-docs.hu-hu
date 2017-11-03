---
title: "A virtuális gép Windows diagnosztika engedélyezése az Azure PowerShell használatával |} Microsoft Docs"
services: virtual-machines-windows
documentationcenter: 
description: "Útmutató: Azure Diagnostics engedélyezése egy virtuális gépen futó Windows PowerShell használatával"
author: sbtron
manager: timlt
editor: 
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: d0be4a712657edfc516c5f32e66519f5d9486728
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Az Azure Diagnostics használatának engedélyezése a PowerShell-lel virtual windowsos virtuális gépen
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Az Azure Diagnostics a funkció, amely lehetővé teszi a telepített alkalmazás diagnosztikai adatok gyűjtésére Azure belül. A diagnosztika bővítmény segítségével például alkalmazás naplóit vagy teljesítményszámlálóit diagnosztikai adatainak összegyűjtése egy Azure virtuális gép (VM) Windows rendszerű. Ez a cikk ismerteti a Windows PowerShell segítségével engedélyezi a diagnosztika bővítményt a virtuális gépek. Lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) esetében ez a cikk szükséges előfeltételeket.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>A diagnosztika bővítmény engedélyezése a Resource Manager üzembe helyezési modellel használatakor
A diagnosztika bővítmény engedélyezheti a Windows virtuális gépek az Azure Resource Manager deployment használatával a bővítmény konfigurációja ad hozzá a Resource Manager-sablon létrehozása során. Lásd: [figyelése és diagnosztika a Windows virtuális gép létrehozása az Azure Resource Manager-sablon használatával](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ahhoz, hogy a diagnosztika bővítmény egy meglévő virtuális gépen keresztül a Resource Manager üzembe helyezési modellel létrehozott, használhatja a [Set-AzureRMVMDiagnosticsExtension](/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension) PowerShell-parancsmag alább látható módon.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* van, amely tartalmazza a diagnosztikai beállításokat az XML-ben, a fájl elérési útját, lásd: a [minta](#sample-diagnostics-configuration) alatt.  

Ha a diagnosztika konfigurációs fájl határozza meg a **StorageAccount** elemet a tárfiók neve, majd a *Set-AzureRMVMDiagnosticsExtension* parancsfájl automatikusan beállítja a diagnosztika bővítmény diagnosztikai adatokat küldeni a tárolási fiók. Ennek működéséhez a tárfiók eltérőnek kell lennie a virtuális géppel ugyanahhoz az előfizetéshez.

Ha nincs **StorageAccount** adott a diagnosztika konfigurációban, akkor kell átadni a *StorageAccountName* paramétert a parancsmaghoz. Ha a *StorageAccountName* paraméter meg van adva, akkor a parancsmag mindig használja a tárfiók a paraméter, a nem a másik a diagnosztika konfigurációs fájlban megadott.

Ha a diagnosztikai tárfiók egy másik előfizetésben található a virtuális gépről, akkor meg kell átadni explicit módon a *StorageAccountName* és *StorageAccountKey* a parancsmag paramétereit. A *StorageAccountKey* paraméter nincs szükség esetén a diagnosztikai tárfiók ugyanahhoz az előfizetéshez, a parancsmag automatikusan lekérdezési és állítható be a kulcs értékét, ha a diagnosztika bővítmény engedélyezése. Azonban ha a diagnosztikai tárfiók egy másik előfizetésben található, akkor a parancsmag nem feltétlenül tudja automatikusan a kulcs eléréséhez, és kell explicit módon adja meg a kulcsot keresztül a *StorageAccountKey* paraméter.  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

A diagnosztika bővítményt a virtuális gép engedélyezése után az aktuális beállítások használatával kaphat a [Get-AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/get-azurermvmdiagnosticsextension) parancsmag.

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

A parancsmag visszaadja *PublicSettings*, amely tartalmazza a diagnosztikai konfigurációja. Támogatott konfiguráció, WadCfg és xmlCfg két típusú léteznek. WadCfg JSON-konfigurációt, és xmlCfg Base64-kódolású formátumú XML-konfigurációját. Az XML-fájl elolvasásához dekódolni a kell.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

A [Remove-AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/remove-azurermvmdiagnosticsextension) parancsmag segítségével távolítsa el a virtuális gép diagnosztikai bővítményét.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>A diagnosztika bővítmény engedélyezése, ha a klasszikus üzembe helyezési modellt használja
Használhatja a [Set-AzureVMDiagnosticsExtension](/powershell/module/azure/set-azurevmdiagnosticsextension) parancsmag engedélyezi a diagnosztika bővítményt a virtuális gép, amely a klasszikus telepítési modell segítségével hozhatók létre. A következő példa bemutatja, hogyan hozzon létre egy új virtuális Gépet, a klasszikus üzembe helyezési modell használatával a diagnosztika bővítmény engedélyezve van.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

A diagnosztika bővítmény egy meglévő virtuális gépen, a klasszikus üzembe helyezési modell használatával létrehozott engedélyezéséhez először használja a [Get-AzureVM](/powershell/module/azure/get-azurevm) parancsmagot, hogy megkapja a Virtuálisgép-konfiguráció. Ezután frissítse a virtuális gép konfigurációját, és tartalmazzák a diagnosztika bővítmény a [Set-AzureVMDiagnosticsExtension](/powershell/module/azure/set-azurevmdiagnosticsextension) parancsmag. Végül a frissített konfiguráció alkalmazása a virtuális gép használatával [frissítés-AzureVM](/powershell/module/azure/update-azurevm).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Mintakonfiguráció diagnosztika
A következő XML a diagnosztika a fenti parancsfájlok nyilvános konfigurációjához használható. Ez a minta-konfiguráció különböző teljesítményszámlálók átkerül a diagnosztikai tárfiók, valamint hibák az alkalmazás biztonsági és a Windows eseménynaplóiban keresse meg a rendszer csatornák és a diagnosztika infrastruktúra naplókból hibáit.

A beállításokat kell frissíteni, hogy a következők:

* A *resourceID* attribútuma a **metrikák** elem az erőforrás-azonosító a virtuális gép frissíteni kell.
  
  * Az erőforrás-azonosítója a következő mintát használatával lehet létrehozni: "/Subscriptions/ {*Előfizetésazonosító az előfizetés a virtuális gép*} /resourceGroups/ {*az erőforráscsoport-névre, a virtuális gép*} / providers/Microsoft.Compute/virtualMachines/ {*a virtuális gép nevét*}".
  * Például, ha az előfizetés-azonosító az előfizetés, a virtuális gép fusson, ahol van **11111111-1111-1111-1111-111111111111**, az erőforráscsoport nevét ahhoz az erőforráscsoporthoz **MyResourceGroup**, és a virtuális gép neve **MyWindowsVM**, majd az értékét *resourceID* lenne:
    
      ```
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * További információt a metrikák akkor jönnek létre, a teljesítmény-számlálókból és a metrikák konfigurációján alapul, lásd: [Azure Diagnostics metrikák table Storage](extensions-diagnostics-template.md#wadmetrics-tables-in-storage).
* A **StorageAccount** elem a diagnosztikai tárfiók neve frissíteni kell.
  
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

## <a name="next-steps"></a>Következő lépések
* Hibaelhárítás az Azure Diagnostics funkció és egyéb technikák segítségével további útmutatást lásd: [diagnosztika engedélyezésével az Azure Cloud Services és a virtuális gépek](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Diagnosztikai konfiguráció séma](https://msdn.microsoft.com/library/azure/mt634524.aspx) a diagnosztika bővítmény különböző XML konfigurációk lehetőségeit ismerteti.

