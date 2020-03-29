---
title: Karbantartási értesítések beszereznie az Azure virtuális gépeiről a PowerShell használatával
description: Tekintse meg az Azure-ban futó virtuális gépek karbantartási értesítéseit, és indítsa el az önkiszolgáló karbantartást a PowerShell használatával.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: b23c210d7c8a9f1d42e6e1b46e0f7f81bda857b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916082"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Tervezett karbantartás kezelése a PowerShell használatával

**Ez a cikk linuxos és Windows-os virtuális gépekre vonatkozik.**

Az Azure PowerShell segítségével megtekintheti, hogy a virtuális gépek [karbantartásra](maintenance-notifications.md)vannak ütemezve. A tervezett karbantartási információk a [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) parancsmagból `-status` érhetők el a paraméter használatakor.
  
A karbantartási adatokat csak akkor adja vissza, ha karbantartást terveznek. Ha nincs olyan karbantartás ütemezve, amely hatással van a virtuális gépre, a parancsmag nem ad vissza karbantartási információkat. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

A MaintenanceRedeployStatus alatt a következő tulajdonságok kerülnek visszaadásra: 

| Érték | Leírás   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Azt jelzi, hogy elindíthatja-e a karbantartást a virtuális gépen jelenleg |
| Karbantartás előttiablak-kezdetideje         | A karbantartási önkiszolgáló ablak kezdete, amikor karbantartást kezdeményezhet a virtuális gépen |
| Karbantartás előttiablakbefejezési idő           | A karbantartási önkiszolgáló ablak vége, amikor karbantartást kezdeményezhet a virtuális gépen |
| MaintenanceWindowStarttime karbantartás            | Az ütemezett karbantartás kezdete, amelyben az Azure karbantartást kezdeményez a virtuális gépen |
| MaintenanceWindowEndTime              | A karbantartási ütemezett ablak vége, amelyben az Azure karbantartást kezdeményez a virtuális gépen |
| LastOperationResultKód               | A virtuális gép karbantartásának kezdeményezésére tett utolsó kísérlet eredménye |



Az erőforráscsoport összes virtuális gépének karbantartási állapotát is lekaphatja a [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) használatával, és nem adja meg a virtuális gépet.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

A következő PowerShell-példa veszi az előfizetés-azonosítót, és visszaadja a karbantartásra ütemezett virtuális gépek listáját.

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

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Karbantartás indítása a virtuális gépen a PowerShell használatával

Az előző szakaszban a függvényből származó információk használatával a következő elindítja a karbantartást egy virtuális gépen, ha **az IsCustomerInitiatedMaintenanceAllowed** értéke true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klasszikus üzembe helyezések

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Ha továbbra is rendelkezik örökölt virtuális gépek, amelyek a klasszikus üzembe helyezési modell használatával telepített, a PowerShell segítségével lekérdezheti a virtuális gépek, és karbantartást kezdeményezhet.

A virtuális gép karbantartási állapotának leigazolásához írja be a következőt:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

A karbantartás elindításához a klasszikus virtuális gép, írja be:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>További lépések

A tervezett karbantartást az [Azure CLI](maintenance-notifications-cli.md) vagy a [portál](maintenance-notifications-portal.md)használatával is kezelheti.
