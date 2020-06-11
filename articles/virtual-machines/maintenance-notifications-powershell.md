---
title: Azure-beli virtuális gépek karbantartási értesítéseinek beolvasása a PowerShell használatával
description: Megtekintheti az Azure-ban futó virtuális gépek karbantartási értesítéseit, és megkezdheti az önkiszolgáló karbantartást a PowerShell használatával.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 79ef649502af8962253121ea18804966b1fa043e
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677933"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Tervezett karbantartás kezelés a PowerShell használatával

**Ez a cikk a Linux és Windows rendszerű virtuális gépekre is vonatkozik.**

A Azure PowerShell használatával megtekintheti a virtuális gépek [karbantartásának](maintenance-notifications.md)ütemezését. A tervezett karbantartási információk a [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) parancsmaggal érhetők el, ha a `-status` paramétert használja.
  
A karbantartási adatokat csak akkor adja vissza a rendszer, ha karbantartási terv van. Ha nincs ütemezve karbantartás, amely hatással van a virtuális gépre, a parancsmag nem ad vissza karbantartási információt. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

A MaintenanceRedeployStatus alatt a következő tulajdonságokat adja vissza: 

| Érték | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy most elindítható-e a virtuális gép karbantartása. |
| PreMaintenanceWindowStartTime         | A karbantartás önkiszolgáló ablakának kezdete, ha a virtuális gépen karbantartást kezdeményezhet |
| PreMaintenanceWindowEndTime           | A karbantartási önkiszolgáló ablak befejezése, ha a virtuális gép karbantartását indítja el |
| MaintenanceWindowStartTime            | Azon karbantartási időszak kezdete, amelyben az Azure a virtuális gép karbantartását kezdeményezi |
| MaintenanceWindowEndTime              | A karbantartási ütemezett időszak vége, amelyben az Azure karbantartást kezdeményez a virtuális gépen |
| LastOperationResultCode               | A virtuális gép karbantartásának megkezdésére tett legutóbbi kísérlet eredménye |



Az erőforráscsoporthoz tartozó összes virtuális gép karbantartási állapotát a [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) használatával és nem a virtuális gépek meghatározásával is lekérheti.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

A következő PowerShell-példa az előfizetés-azonosítót veszi át, és a karbantartásra ütemezett virtuális gépek listáját adja vissza.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>A virtuális gép karbantartásának elindítása a PowerShell használatával

Az előző szakaszban található függvényből származó információk használatával a következő elindítja a virtuális gép karbantartását, ha a **IsCustomerInitiatedMaintenanceAllowed** értéke TRUE (igaz).

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klasszikus üzembe helyezések

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Ha továbbra is a klasszikus üzemi modellel telepített örökölt virtuális gépekkel rendelkezik, akkor a PowerShell használatával lekérdezheti a virtuális gépeket, és megkezdheti a karbantartást.

Egy virtuális gép karbantartási állapotának lekéréséhez írja be a következőt:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

A klasszikus virtuális gép karbantartásának megkezdéséhez írja be a következőt:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Következő lépések

A tervezett karbantartást az [Azure CLI](maintenance-notifications-cli.md) vagy a [portál](maintenance-notifications-portal.md)használatával is kezelheti.
