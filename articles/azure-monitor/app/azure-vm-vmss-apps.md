---
title: Teljesítmény figyelése az Azure virtuális gépeken – Azure Application Insights
description: Alkalmazásteljesítmény-figyelés az Azure Virtuálisgép és az Azure virtuálisgép-méretezési készletek. A diagram terhelési és válaszideje, a függőségi információk és a teljesítményre vonatkozó riasztások beállítása.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661328"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Az Azure Monitor Application Insights Agent üzembe helyezése Azure-beli virtuális gépeken és Azure virtuálisgép-méretezési csoportokban

Az [Azure virtuális gépeken](https://azure.microsoft.com/services/virtual-machines/) és [az Azure virtuálisgép-méretezési csoportjain](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) futó .NET alapú webalkalmazások figyelésének engedélyezése most minden eddiginél egyszerűbb. Az Application Insights használatának minden előnyét a kód módosítása nélkül is élvezheti.

Ez a cikk bemutatja az Application Insights-figyelő kontise az Application Insights-ügynök használatával, és előzetes útmutatást nyújt a folyamat gyorsan a nagy méretű központi telepítések.

> [!IMPORTANT]
> Az Azure Application Insights Agent for .
> Ez az előzetes verzió szolgáltatásszintű szerződés nélkül érhető el, és nem javasoljuk éles számítási feladatokhoz. Előfordulhat, hogy egyes szolgáltatások nem támogatottak, és néhány korlátozott képességgel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Kétféleképpen engedélyezheti az alkalmazások figyelését az Azure virtuális gépeihez és az Azure virtuálisgép-méretezési készleteihez:

* **Kódnélküli** az Application Insights-ügynökön keresztül
    * Ez a módszer a legkönnyebben engedélyezve, és nincs szükség speciális konfigurációra. Gyakran nevezik "futásidejű" figyelés.

    * Az Azure virtuális gépek és az Azure virtuálisgép-méretezési készletek azt javasoljuk, hogy legalább ezt a felügyeleti szintet. Ezt követően, az adott forgatókönyv alapján, kiértékelheti, hogy manuális műszerezés re van szükség.

    * Az Application Insights-ügynök automatikusan ugyanazokat a függőségi jeleket gyűjti a .NET SDK-val. További [információ: Függőség automatikus gyűjtése.](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net)
        > [!NOTE]
        > Jelenleg csak a .Net IIS által üzemeltetett alkalmazások támogatottak. SDK használatával ASP.NET Core, Java és Node.js alkalmazások at egy Azure virtuális gépek és a virtuális gép méretezési csoportok.

* **Kódalapú SDK-n** keresztül

    * Ez a megközelítés sokkal testreszabhatóbb, de az [Application Insights SDK NuGet-csomagoktól való függőség et](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)igényel. Ez a módszer azt is jelenti, hogy saját maga kell kezelnie a csomagok legújabb verziójának frissítéseit.

    * Ha egyéni API-hívásokat kell kezdeményeznie az alapértelmezetten nem rögzített események/függőségek nyomon követéséhez az ügynökalapú figyeléssel, akkor ezt a módszert kell használnia. További információért tekintse meg az [EGYÉNI események és metrikák API-ját.](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)

> [!NOTE]
> Ha mind az ügynök alapú figyelés, mind a manuális SDK-alapú műszerezés csak a manuális műszerezési beállítások at észleli. Ezzel megakadályozhatja az ismétlődő adatok küldését. Ha többet szeretne megtudni erről, olvassa el az alábbi [hibaelhárítási szakaszt.](#troubleshooting)

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Application Insights-ügynök kezelése .NET-alkalmazásokhoz Azure-beli virtuális gépeken a PowerShell használatával

> [!NOTE]
> Az Application Insights-ügynök telepítése előtt szüksége lesz egy kapcsolati karakterláncra. [Hozzon létre egy új Application Insights-erőforrást,](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) vagy másolja a kapcsolati karakterláncot egy meglévő application insights-erőforrásból.

> [!NOTE]
> Még nem vagy itt? Tekintse meg az [Első lépések útmutatót.](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0)

Az Application Insights-ügynök telepítése vagy frissítése az Azure virtuális gépek bővítményeként
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Az Application Insights-ügynök egy bővítmény ként több virtuális gépek egy Powershell-ciklus használatával telepítheti vagy frissítheti az Application Insights Agent bővítményként.

Az Application Insights Ügynök bővítmény eltávolítása az Azure virtuális gépről
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

A Query Application Insights Ügynök bővítményének állapota az Azure virtuális géphez
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Az Azure virtuális gép telepített bővítményeinek listájának beszereznie
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
A telepített bővítményeket az [Azure virtuálisgép-panelen](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) is megtekintheti a portálon.

> [!NOTE]
> A telepítés takarásban való ellenőrzésével ellenőrizze az Alkalmazáselemzési ügynökbővítmény üzembe helyezéséhez használt kapcsolati karakterlánchoz társított Application Insights Erőforrás-erőforrás élő metrikák streamre való kattintással. Ha több virtuális gépről küld adatokat, válassza ki a cél Azure virtuális gépek a Kiszolgáló neve csoportban. Az adatok áramlásának megkezdése akár egy percet is igénybe vehet.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Application Insights-ügynök kezelése .NET-alkalmazásokhoz az Azure virtuálisgép-méretezési csoportjain a PowerShell használatával

Az Application Insights-ügynök telepítése vagy frissítése az Azure virtuálisgép-méretezési készletbővítményeként
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
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

Alkalmazásfigyelési bővítmény eltávolítása az Azure virtuálisgép-méretezési készleteiből
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Lekérdezési alkalmazásfigyelési bővítmény állapota az Azure virtuálisgép-méretezési készleteihez
```powershell
# Not supported by extensions framework
```

Az Azure virtuálisgép-méretezési készleteinek telepített bővítményeinek listájának beszereznie
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Hibaelhárítás

Hibaelhárítási tippek az Application Insights Monitoring Agent Extension for . NET-alkalmazások hoz, amelyek Azure virtuális gépeken és virtuálisgép-méretezési csoportokon futnak.

> [!NOTE]
> A .NET Core, a Java és a Node.js alkalmazások csak az Azure virtuális gépeken és az Azure virtuálisgép-méretezési csoportokon keresztül támogatottak manuális SDK-alapú instrumentation-en keresztül, ezért az alábbi lépések nem vonatkoznak ezekre a forgatókönyvekre.

A bővítmény-végrehajtási kimenet a következő könyvtárakban található fájlokba kerül:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [helyezhet üzembe egy alkalmazást egy Azure virtuálisgép-méretezési csoportba.](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)
* [Állítsa be a rendelkezésre állási webes teszteket,](monitor-web-app-availability.md) hogy riasztást kapjon, ha a végpont nem érhető el.
