---
title: Az Azure-beli virtuális gépen és az Azure-beli virtuálisgép-méretezési csoportokban tárolt alkalmazások teljesítményének figyelése | Microsoft Docs
description: Alkalmazások teljesítményének figyelése Azure-beli virtuális gépekhez és Azure-beli virtuálisgép-méretezési csoportokhoz. A diagram betöltésének és a válaszidő, a függőségi adatok és a riasztások beállítása a teljesítményre.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: mbullwin
ms.openlocfilehash: f2c6b98fd0be2061e9d8cab5c063cafadf71476a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597451"
---
# <a name="monitor-application-performance-hosted-on-azure-vm-and-azure-virtual-machine-scale-sets"></a>Az Azure-beli virtuális gépen és az Azure-beli virtuálisgép-méretezési csoportokban tárolt alkalmazások teljesítményének figyelése

Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) -on és az [Azure Virtual Machine Scale sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) -on futó .NET-alapú webalkalmazások figyelésének engedélyezése mostantól minden eddiginél egyszerűbb. A kód módosítása nélkül érheti el a Application Insights használatának összes előnyét.

Ebből a cikkből megtudhatja, hogyan engedélyezheti Application Insights figyelését az ApplicationMonitoringWindows bővítménnyel, és hogyan adhat meg előzetes útmutatót a nagyméretű központi telepítések folyamatának automatizálásához.

> [!IMPORTANT]
> Az Azure ApplicationMonitoringWindows bővítmény jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem ajánlott éles környezetben üzemelő számítási feladatokhoz. Előfordulhat, hogy egyes funkciók nem támogatottak, és egyes szolgáltatások korlátozott képességekkel rendelkeznek.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az alkalmazások figyelését kétféleképpen engedélyezheti az Azure-beli virtuális gépek és az Azure virtuálisgép-méretezési csoport üzemeltetett alkalmazásai számára:

* **Ügynök-alapú alkalmazás figyelése** (ApplicationMonitoringWindows-bővítmény).
    * Ez a módszer a legkönnyebben engedélyezhető, és nincs szükség speciális konfigurációra. Ezt gyakran "futtatókörnyezet"-figyelőnek nevezzük. Az Azure-beli virtuális gépek és az Azure-beli virtuálisgép-méretezési csoportok esetében ajánlott minimálisan engedélyezni ezt a monitorozási szintet. Ezt követően az adott forgatókönyv alapján kiértékelheti, hogy szükség van-e a manuális rendszerállapotra.
    * Jelenleg csak a .NET IIS által üzemeltetett alkalmazások támogatottak.

* Az **alkalmazást a programkódon keresztül manuálisan** , a Application Insights SDK telepítésével végezheti el.

    * Ez a megközelítés sokkal testreszabható, de a [Application INSIGHTS SDK NuGet-csomagokhoz való függőség hozzáadását](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)igényli. Ez a módszer azt is jelenti, hogy a frissítéseket a csomagok legújabb verziójára kell kezelnie.

    * Ha egyéni API-hívásokat kell megadnia az ügynök-alapú figyeléssel alapértelmezés szerint nem rögzített események/függőségek nyomon követéséhez, ezt a metódust kell használnia. További információért tekintse meg az [Egyéni események és mérőszámok API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) -ját ismertető cikket.

> [!NOTE]
> Ha mind az ügynök-alapú figyelés, mind a manuális SDK-alapú rendszerállapotot észleli, a rendszer csak a manuális rendszerállapot-beállításokat veszi figyelembe. Ez megakadályozza az ismétlődő adatok küldését. Ha többet szeretne megtudni erről, tekintse meg az alábbi [hibaelhárítási szakaszt](#troubleshooting) .

## <a name="manage-agent-based-monitoring-for-net-applications-on-vm-using-powershell"></a>A virtuális gépen futó .NET-alkalmazások ügynök-alapú figyelésének kezelése a PowerShell használatával

Telepítse vagy frissítse a virtuális gép alkalmazás-figyelési bővítményét
```powershell
$publicCfgJsonString = '
{
  "RedfieldConfiguration": {
    "InstrumentationKeyMap": {
      "Filters": [
        {
          "AppFilter": ".*",
          "MachineFilter": ".*",
          "InstrumentationSettings" : {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "South Central US" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

Alkalmazás-figyelési bővítmény eltávolítása a virtuális gépről
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Alkalmazás-figyelési bővítmény állapotának lekérdezése virtuális gépen
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Telepített bővítmények listájának beolvasása a virtuális géphez
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```

## <a name="manage-agent-based-monitoring-for-net-applications-on-azure-virtual-machine-scale-set-using-powershell"></a>A .NET-alkalmazások ügynök-alapú figyelésének kezelése Azure-beli virtuálisgép-méretezési csoporton a PowerShell használatával

Az Azure virtuálisgép-méretezési csoport alkalmazás-figyelési bővítményének telepítése vagy frissítése
```powershell
$publicCfgHashtable =
@{
  "RedfieldConfiguration"= @{
    "InstrumentationKeyMap"= @{
      "Filters"= @(
        @{
          "AppFilter"= ".*";
          "MachineFilter"= ".*";
          "InstrumentationSettings"= @{
            "InstrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Alkalmazás-figyelési bővítmény eltávolítása az Azure virtuálisgép-méretezési csoportból
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Alkalmazás-figyelési bővítmény állapotának lekérdezése Azure-beli virtuálisgép-méretezési csoport számára
```powershell
# Not supported by extensions framework
```

Az Azure virtuálisgép-méretezési csoport telepített bővítményeinek listájának beolvasása
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbiakban részletes hibaelhárítási útmutatót talál az Azure-beli virtuális gépen és az Azure-beli virtuálisgép-méretezési csoportokon futó .NET-alkalmazások bővítmény-alapú figyeléséhez.

> [!NOTE]
> A .NET Core, a Java és a Node. js alkalmazások csak az Azure-beli virtuális gépek és az Azure virtuálisgép-méretezési csoportok esetében támogatottak a manuális SDK-alapú eszközökön keresztül, ezért az alábbi lépések nem vonatkoznak ezekre a forgatókönyvekre.

A bővítmény-végrehajtás kimenete a következő címtárakban található fájlokra van naplózva:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [helyezhet üzembe egy alkalmazást egy Azure-beli virtuálisgép-méretezési csoporton](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Állítsa be a rendelkezésre állási webes tesztek](monitor-web-app-availability.md) riasztását, ha a végpont le van állítva.
