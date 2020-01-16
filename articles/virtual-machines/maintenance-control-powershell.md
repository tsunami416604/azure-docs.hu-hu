---
title: Azure-beli virtuális gépek karbantartási vezérlése a PowerShell-lel
description: Útmutató az Azure-beli virtuális gépek karbantartásának szabályozásához a karbantartás és a PowerShell használatával.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: 7ca98723511cc7297b462747d4e1e12ca9bd38c2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979020"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Előzetes verzió: frissítések vezérlése karbantartási vezérlővel és Azure PowerShell

A karbantartási vezérlő használatával olyan platform-frissítéseket kezelhet, amelyek nem igényelnek újraindítást. Az Azure gyakran frissíti az infrastruktúráját a megbízhatóság, a teljesítmény, a biztonság és az új funkciók elindításának javítása érdekében. A legtöbb frissítés átlátható a felhasználók számára. Bizonyos érzékeny munkaterhelések, például a játékok, a médiaadatfolyam-továbbítás és a pénzügyi tranzakciók nem tolerálják a virtuális gépek lefagyasztásának vagy leválasztásának néhány másodpercét. A karbantartási ellenőrzés lehetőséget biztosít a platform frissítéseinek megvárni, és egy 35 napos időszakon belül alkalmazza őket. 

A karbantartási ellenőrzéssel eldöntheti, hogy mikor alkalmazza a frissítéseket az elkülönített virtuális gépekre.

A karbantartási ellenőrzéssel a következőket teheti:
- A Batch frissítése egyetlen frissítési csomagba.
- Várjon akár 35 napra a frissítések alkalmazásához. 
- A karbantartási időszak platform-frissítéseinek automatizálása Azure Functions használatával.
- A karbantartási konfigurációk az előfizetések és az erőforráscsoportok között működnek. 

> [!IMPORTANT]
> A karbantartási vezérlő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Korlátozások

- A virtuális gépeknek [dedikált gazdagépen](./linux/dedicated-hosts.md)kell lenniük, vagy egy elkülönített virtuálisgép- [mérettel](./linux/isolation.md)kell létrehozni.
- 35 nap elteltével a rendszer automatikusan alkalmazza a frissítést.
- A felhasználónak **erőforrás-tulajdonosi** hozzáféréssel kell rendelkeznie.


## <a name="enable-the-powershell-module"></a>A PowerShell-modul engedélyezése

Győződjön meg arról, hogy a `PowerShellGet` naprakész.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Az az. Maintenance PowerShell-parancsmagok előzetes verzióban érhetők el, ezért telepítenie kell a modult a `AllowPrerelease` paraméterrel Cloud Shell vagy a helyi PowerShell-telepítésben.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Ha helyileg telepíti a rendszert, győződjön meg róla, hogy rendszergazdaként megnyitja a PowerShell-parancssort.

Azt is megteheti, hogy meg kell erősítenie, hogy nem *megbízható adattárból*kíván telepíteni. A modul telepítéséhez írja be `Y` vagy válassza az Igen lehetőséget az **összes** elemre.



## <a name="create-a-maintenance-configuration"></a>Karbantartási konfiguráció létrehozása

Hozzon létre egy erőforráscsoportot tárolóként a konfigurációhoz. Ebben a példában egy *myMaintenanceRG* nevű erőforráscsoportot hoznak létre a *eastus*-ben. Ha már van olyan erőforráscsoport, amelyet használni szeretne, kihagyhatja ezt a részt, és az erőforráscsoport nevét lecserélheti a többi példán belülre.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

A [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) használatával hozzon létre egy karbantartási konfigurációt. Ez a példa létrehoz egy *konfig* nevű karbantartási konfigurációt a gazdagépre. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

A `-MaintenanceScope host` használata biztosítja, hogy a rendszer a karbantartási konfigurációt használja a gazdagép frissítéseinek vezérlésére.

Ha azonos nevű konfigurációt próbál létrehozni, de egy másik helyen, hibaüzenetet fog kapni. A konfiguráció nevének egyedinek kell lennie az előfizetésben.

A [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration)használatával lekérdezheti a rendelkezésre álló karbantartási konfigurációkat.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>A konfiguráció kiosztása

A [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) használatával rendelje hozzá a konfigurációt az elkülönített virtuális géphez vagy az Azure dedikált gazdagéphez.

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Alkalmazza a konfigurációt egy virtuális gépre a konfiguráció AZONOSÍTÓjának használatával. Adja meg a `-ResourceType VirtualMachines`t, és adja meg a virtuális gép nevét `-ResourceName`számára, valamint a virtuális gép erőforráscsoportot a `-ResourceGroupName`hoz. 

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

Ha a konfigurációt dedikált gazdagépre szeretné alkalmazni, a `-ResourceType hosts`is meg kell adnia, `-ResourceParentName` a gazda-csoport nevével, és `-ResourceParentType hostGroups`. 


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

A [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) használatával ellenőrizze, hogy van-e függőben lévő frissítés. A `-subscription` használatával megadhatja a virtuális gép Azure-előfizetését, ha az eltér a bejelentkezett fióktól.

Ha nincsenek frissítések, a parancs hibaüzenetet ad vissza: `Resource not found...StatusCode: 404`.

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

A függőben lévő frissítések alkalmazásához használja a [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) .

### <a name="isolated-vm"></a>Elkülönített virtuális gép

Hozzon létre egy, az elkülönített virtuális gépre vonatkozó frissítések alkalmazására vonatkozó kérelmet.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

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
A [Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) használatával megtekintheti a frissítések állapotát. Az alábbi parancsok a legújabb frissítés állapotát mutatják `default` használatával a `-ApplyUpdateName` paraméterhez. Egy adott frissítés állapotának lekéréséhez helyettesítse be a frissítés nevét (a [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) parancs által visszaadottak szerint).

Ha nincsenek megjeleníthető frissítések, a parancs hibaüzenetet ad vissza: `Resource not found...StatusCode: 404`.

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
   -ApplyUpdateName default
```

## <a name="remove-a-maintenance-configuration"></a>Karbantartási konfiguráció eltávolítása

A [Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) használatával törölje a karbantartási konfigurációt.

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Következő lépések
További információ: [karbantartás és frissítések](maintenance-and-updates.md).
