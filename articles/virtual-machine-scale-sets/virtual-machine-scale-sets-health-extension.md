---
title: Alkalmazásállapot-bővítmény használata az Azure-beli virtuálisgép-méretezési csoportokkal |} A Microsoft Docs
description: Ismerje meg, hogyan az alkalmazások állapotának bővítmény használatával a virtual machine scale sets szolgáltatásban üzembe helyezett alkalmazások állapotának monitorozásához.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: 1ac7b5f41c0c941db08a63c516febabaf9f07b3e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491382"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Bővítményt a virtuálisgép-méretezési csoportokat használ alkalmazásállapot
Az alkalmazás állapotának monitorozása egy fontos jel, kezeléséhez és az üzemelő példány frissítése. Azure-beli virtuálisgép-méretezési csoportok biztosít támogatást [a működés közbeni frissítés](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) beleértve [automatikus operációsrendszer-lemezkép frissítéseinek](virtual-machine-scale-sets-automatic-upgrade.md), amely támaszkodjon szolgáltatásállapot-figyelést az egyes példányainak az üzemelő példány frissítése .

Ez a cikk bemutatja, hogyan használhatja az alkalmazás állapotának bővítmény állapotát, a virtual machine scale sets szolgáltatásban üzembe helyezett alkalmazások figyelésére.

## <a name="pre-requisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy Ön ismeri a:
-   Az Azure virtuális gép [bővítmények](../virtual-machines/extensions/overview.md)
-   [Módosítása](virtual-machine-scale-sets-upgrade-scale-set.md) a virtual machine scale sets

## <a name="when-to-use-the-application-health-extension"></a>Mikor érdemes használni az alkalmazásállapot-bővítmény
Az alkalmazás állapotának bővítmény virtuálisgép-méretezési készlet példány és a méretezési csoport példány belül a virtuális gép állapota a jelentések belül van telepítve. Beállíthatja, hogy a bővítmény alkalmazása a végpont mintavételi és azon a példányon az alkalmazás állapotának frissítése. A példány állapota be van jelölve, az Azure-példány jogosult a frissítési műveletek meghatározásához.

A bővítmény jelentések összesíti a virtuális Gépen belül, mint a bővítmény használható helyzetekben, ahol a külső alkalmazás állapotadat-mintavételek például mintavételek (, amelyek használják az egyéni Azure Load Balancer [mintavételek](../load-balancer/load-balancer-custom-probe-overview.md)) nem használható.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON az alkalmazásállapot bővítmény sémáját jeleníti meg. A bővítmény telepítéséhez a "http" vagy "tcp" kérést egy kapcsolódó portot vagy a kérés útvonala pedig minimális.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus
| ---- | ---- | ---- | ----
| apiVersion | `2018-10-01` | dátum |
| publisher | `Microsoft.ManagedServices` | sztring |
| type | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | sztring |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Beállítások

| Name (Név) | Érték és példa | Adattípus
| ---- | ---- | ----
| protokoll | `http` vagy `tcp` | sztring |
| port | Nem kötelező, ha a protokoll `http`kötelező, ha a protokoll `tcp` | int |
| requestPath | Kötelező, ha a protokoll `http`, nem engedélyezett, ha a protokoll `tcp` | sztring |

## <a name="deploy-the-application-health-extension"></a>Az alkalmazás állapotának bővítmény telepítése
Többféleképpen is üzembe helyezése a méretezési csoport bővítményének állítja be, ahogy az az alábbi példák az alkalmazás állapotát.

### <a name="rest-api"></a>REST API

Az alábbi példa az alkalmazásállapot-bővítmény (a név myHealthExtension) ad hozzá a extensionprofile profilt a Windows-alapú méretezési a méretezési csoport modelljéből.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Használat `PATCH` egy már üzembe helyezett bővítmény szerkesztése.

### <a name="azure-powershell"></a>Azure PowerShell

Használja a [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmag használatával adja hozzá az alkalmazás állapotának bővítményt a méretezési csoport állítsa be a modell definícióját.

Az alábbi példa hozzáadja a alkalmazásállapot bővítményt a `extensionProfile` a méretezésicsoport-modellben Windows-alapú méretezési. A példa az új Az PowerShell-modult használja.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

Használat [az vmss-bővítmény beállítása](/cli/azure/vmss/extension#az-vmss-extension-set) hozzáadása az alkalmazásállapot bővítményt a méretezési csoport modell definícióját.

Az alábbi példa az alkalmazásállapot-bővítmény hozzáadása egy Windows-alapú méretezési csoportot, a méretezési csoport modelljéből.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Hibaelhárítás
Bővítmény végrehajtás kimenetének a rendszer naplózza a következő könyvtárban található fájlok:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

A naplók rendszeres időközönként rögzíti az alkalmazás állapot ellenőrzése.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [az alkalmazás üzembe helyezéséhez](virtual-machine-scale-sets-deploy-app.md) a virtuálisgép-méretezési csoportok.
