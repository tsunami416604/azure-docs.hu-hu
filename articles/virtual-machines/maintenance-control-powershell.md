---
title: A PowerShellt használó azure-beli virtuális gépek karbantartásának vezérlése
description: Megtudhatja, hogyan szabályozhatja, hogy mikor alkalmazza a karbantartást az Azure-beli virtuális gépeken a Maintenance Control és a PowerShell használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: dc47afe9cb6eca1b10f8caca7b85087023c5eadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060130"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Előzetes verzió: Frissítések vezérlése a Maintenance Control és az Azure PowerShell segítségével

A karbantartási vezérlés használatával nem igényel újraindítást igénylő platformfrissítések kezelése. Az Azure gyakran frissíti infrastruktúráját a megbízhatóság, a teljesítmény, a biztonság és az új funkciók elindítása érdekében. A legtöbb frissítés a felhasználók számára átlátható. Egyes bizalmas számítási feladatok, például a játék, a médiaadatfolyam-továbbítás és a pénzügyi tranzakciók, még néhány másodpercig sem tolerálhatják a virtuális gépek karbantartás vagy leválasztását. A karbantartás-vezérlés lehetővé teszi, hogy megvárja a platformfrissítéseit, és alkalmazza azokat egy 35 napos gördülő ablakon belül. 

A karbantartás-vezérlés lehetővé teszi annak eldöntését, hogy mikor alkalmazza a frissítéseket az elkülönített virtuális gépekre.

A karbantartás vezérlésével a következőkre van lehetőség:
- A kötegfrissítések egyetlen frissítési csomagba kerülnek.
- Várjon akár 35 napot a frissítések alkalmazásával. 
- Az Azure Functions használatával automatizálhatja a karbantartási időszak platformfrissítéseit.
- A karbantartási konfigurációk előfizetések és erőforráscsoportok között működnek. 

> [!IMPORTANT]
> A Karbantartás vezérlés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
> 

## <a name="limitations"></a>Korlátozások

- A virtuális gépeknek [dedikált gazdagépen](./linux/dedicated-hosts.md)kell lenniük, vagy [elkülönített virtuálisgép-mérethasználatával](./linux/isolation.md)kell létrehozniuk.
- 35 nap elteltével a rendszer automatikusan alkalmazza a frissítést.
- A felhasználónak rendelkeznie kell **erőforrás-közreműködői** hozzáféréssel.


## <a name="enable-the-powershell-module"></a>A PowerShell-modul engedélyezése

Győződjön `PowerShellGet` meg róla, hogy naprakész.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Az Az.Maintenance PowerShell-parancsmagok előzetes verzióban vannak, ezért `AllowPrerelease` telepítenie kell a modult a paraméterrel a Cloud Shellben vagy a helyi PowerShell-telepítésben.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Ha helyileg telepíti, győződjön meg arról, hogy rendszergazdaként nyissa meg a PowerShell-kérdést.

Előfordulhat, hogy meg kell erősítenie, hogy *nem megbízható tárházból*kíván telepíteni. Írja `Y` be vagy válassza **az Igen az Összes gombra** a modul telepítéséhez.



## <a name="create-a-maintenance-configuration"></a>Karbantartási konfiguráció létrehozása

Hozzon létre egy erőforráscsoportot a konfiguráció tárolójaként. Ebben a példában egy *myMaintenanceRG* nevű erőforráscsoport jön létre *az eastus alkalmazásban.* Ha már rendelkezik használni kívánt erőforráscsoporttal, kihagyhatja ezt a részt, és a többi példában lecserélheti az erőforráscsoport nevét a saját nevére.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

A [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) használatával karbantartási konfigurációt hozhat létre. Ebben a példában létrehoz egy karbantartási konfiguráció nevű *myConfig* hatókörrel a gazdagép. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

A `-MaintenanceScope host` használata biztosítja, hogy a karbantartási konfiguráció t használja a gazdagép frissítéseinek szabályozásához.

Ha azonos nevű, de más helyen lévő konfigurációt próbál létrehozni, hibaüzenetet kap. A konfigurációneveknek egyedinek kell lenniük az előfizetésben.

Az elérhető karbantartási konfigurációkat a [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration)használatával kérdezheti le.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>A konfiguráció hozzárendelése

A [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) használatával rendelje hozzá a konfigurációt az elkülönített virtuális géphez vagy az Azure dedikált gazdagéphez.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Alkalmazza a konfigurációt egy virtuális gépre a konfiguráció azonosítójának használatával. Adja `-ResourceType VirtualMachines` meg és adja meg `-ResourceName`a virtuális gép nevét és `-ResourceGroupName`a virtuális gép erőforráscsoportját. 

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

Ha egy dedikált állomásra szeretne konfigurációt `-ResourceType hosts`alkalmazni, a nevét is meg kell adnia a gazdagépcsoport `-ResourceParentName` nevével, és `-ResourceParentType hostGroups`. 


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

A [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) segítségével ellenőrizheti, hogy vannak-e függőben lévő frissítések. A `-subscription` virtuális gép Azure-előfizetésének megadásához, ha az eltér a bejelentkezetttől.

Ha nincsenek megjelenetésre kerülő frissítések, ez a parancs nem ad vissza semmit. Ellenkező esetben egy PSApplyUpdate objektumot ad vissza:

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

Ellenőrizze a függőben lévő frissítések egy elkülönített virtuális gép. Ebben a példában a kimenet az olvashatóság érdekében táblázatként van formázva.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Dedikált gazdagép

Egy dedikált állomás függőben lévő frissítéseinek keresése. Ebben a példában a kimenet az olvashatóság érdekében táblázatként van formázva. Cserélje le az erőforrások értékeit a sajátjára.

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

A [New-AzApplyUpdate segítségével](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) alkalmazza a függőben lévő frissítéseket.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Hozzon létre egy kérelmet egy elkülönített virtuális gép frissítések alkalmazására.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

A sikeres, ez a `PSApplyUpdate` parancs visszaad egy objektumot. A `Get-AzApplyUpdate` parancs Ban található Name attribútummal ellenőrizheti a frissítés állapotát. Lásd: [Frissítés állapotának ellenőrzése](#check-update-status).

### <a name="dedicated-host"></a>Dedikált gazdagép

Frissítések alkalmazása dedikált állomásra.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Frissítés állapotának ellenőrzése
A Frissítések állapotának ellenőrzéséhez használja a [Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) szolgáltatást. Az alábbi parancsok a legutóbbi frissítés állapotát `default` mutatják a `-ApplyUpdateName` paraméter használatával. A frissítés (a [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) parancs által visszaadott) nevének beírásával lekérheti egy adott frissítés állapotát.

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
LastUpdateTime lesz az az idő, amikor a frissítés befejeződött, akár Ön által kezdeményezett, vagy a platform, ha önfenntartó ablak nem használták. Ha a karbantartás-ellenőrzésen keresztül még soha nem alkalmazták a frissítést, akkor az alapértelmezett értéket jeleníti meg.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Egy adott virtuális gép frissítései keresése.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Dedikált gazdagép

Ellenőrizze, hogy vannak-e frissítések egy dedikált állomáshoz.

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

Karbantartási konfiguráció törléséhez használja [az Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) parancsot.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>További lépések
További információ: [Karbantartás és frissítések](maintenance-and-updates.md).
