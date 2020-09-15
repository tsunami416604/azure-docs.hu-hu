---
title: Azure-beli virtuális gépek karbantartási vezérlése a PowerShell-lel
description: Útmutató az Azure-beli virtuális gépek karbantartásának szabályozásához a karbantartás és a PowerShell használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 5cb504e10c9a1b10c5bad201f4f599a3c00992fe
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530760"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>Frissítések vezérlése karbantartási vezérléssel és Azure PowerShell

A karbantartási ellenőrzéssel eldöntheti, hogy mikor alkalmazza a frissítéseket az elkülönített virtuális gépekre és az Azure dedikált gazdagépekre. Ez a témakör a karbantartás-vezérlés Azure PowerShell beállításait ismerteti. További információ a karbantartási ellenőrzés, a hozzá tartozó korlátozások és egyéb felügyeleti lehetőségek használatáról: a [platform frissítéseinek kezelése a karbantartási ellenőrzéssel](maintenance-control.md).
 
## <a name="enable-the-powershell-module"></a>A PowerShell-modul engedélyezése

Győződjön `PowerShellGet` meg róla, hogy naprakész.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

Telepítse a `Az.Maintenance` PowerShell-modult.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Ha helyileg telepíti a rendszert, győződjön meg róla, hogy rendszergazdaként megnyitja a PowerShell-parancssort.

Azt is megteheti, hogy meg kell erősítenie, hogy nem *megbízható adattárból*kíván telepíteni. Írja be, `Y` vagy válassza az **Igen** lehetőséget az összes elemre a modul telepítéséhez.


## <a name="create-a-maintenance-configuration"></a>Karbantartási konfiguráció létrehozása

Hozzon létre egy erőforráscsoportot tárolóként a konfigurációhoz. Ebben a példában egy *myMaintenanceRG* nevű erőforráscsoportot hoznak létre a *eastus*-ben. Ha már van olyan erőforráscsoport, amelyet használni szeretne, kihagyhatja ezt a részt, és az erőforráscsoport nevét lecserélheti a többi példán belülre.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

A [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) használatával hozzon létre egy karbantartási konfigurációt. Ez a példa létrehoz egy *konfig* nevű karbantartási konfigurációt a gazdagépre. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

A használata `-MaintenanceScope host` biztosítja, hogy a rendszer a karbantartási konfigurációt használja a gazdagép frissítéseinek vezérlésére.

Ha azonos nevű konfigurációt próbál létrehozni, de egy másik helyen, hibaüzenetet fog kapni. A konfigurációs névnek egyedinek kell lennie az erőforráscsoport számára.

A [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)használatával lekérdezheti a rendelkezésre álló karbantartási konfigurációkat.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>A konfiguráció kiosztása

A [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) használatával rendelje hozzá a konfigurációt az elkülönített virtuális géphez vagy az Azure dedikált gazdagéphez.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Alkalmazza a konfigurációt egy virtuális gépre a konfiguráció AZONOSÍTÓjának használatával. Adja meg és adja meg a virtuális gép `-ResourceType VirtualMachines` nevét `-ResourceName` , valamint a virtuális gép erőforrás-csoportját `-ResourceGroupName` . 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Dedikált gazdagép

Ha a konfigurációt dedikált gazdagépre szeretné alkalmazni, a nevet is tartalmaznia kell a `-ResourceType hosts` `-ResourceParentName` gazda csoport nevével és `-ResourceParentType hostGroups` . 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Függőben lévő frissítések keresése

A [Get-AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate) használatával ellenőrizze, hogy van-e függőben lévő frissítés. A használatával `-subscription` megadhatja a virtuális gép Azure-előfizetését, ha az eltér a bejelentkezett fióktól.

Ha nincsenek megjeleníthető frissítések, a parancs nem ad vissza semmit. Ellenkező esetben egy PSApplyUpdate objektumot ad vissza:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>Elkülönített virtuális gép

A függőben lévő frissítések keresése egy elkülönített virtuális géphez. Ebben a példában a kimenet táblázatként van formázva az olvashatóság érdekében.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Dedikált gazdagép

A függőben lévő frissítések keresése egy dedikált gazdagépen. Ebben a példában a kimenet táblázatként van formázva az olvashatóság érdekében. Cserélje le az erőforrások értékeit a saját adataira.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Frissítések alkalmazása

A függőben lévő frissítések alkalmazásához használja a [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) .

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Hozzon létre egy, az elkülönített virtuális gépre vonatkozó frissítések alkalmazására vonatkozó kérelmet.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Sikeres művelet esetén a parancs egy objektumot ad vissza `PSApplyUpdate` . A `Get-AzApplyUpdate` frissítés állapotának megtekintéséhez használja a parancs Name attribútumát. Lásd: [frissítési állapot keresése](#check-update-status).

### <a name="dedicated-host"></a>Dedikált gazdagép

Frissítések alkalmazása dedikált gazdagépre.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Frissítés állapotának keresése
A [Get-AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate) használatával megtekintheti a frissítések állapotát. Az alábbi parancsok megjelenítik a legújabb frissítés állapotát a `default` `-ApplyUpdateName` paraméterrel való használatával. Egy adott frissítés állapotának lekéréséhez helyettesítse be a frissítés nevét (a [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) parancs által visszaadottak szerint).

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
A LastUpdateTime az az idő, amikor a frissítés befejeződött, vagy Ön által kezdeményezett, vagy a platformon, ha az önkarbantartási időszakot nem használták. Ha még soha nem történt frissítés a karbantartási ellenőrzésen, akkor az alapértelmezett értéket fogja megjeleníteni.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Egy adott virtuális gép frissítéseinek keresése.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Dedikált gazdagép

A dedikált gazdagép frissítéseinek keresése.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Karbantartási konfiguráció eltávolítása

A [Remove-AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) használatával törölje a karbantartási konfigurációt.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Következő lépések
További információ: [karbantartás és frissítések](maintenance-and-updates.md).
