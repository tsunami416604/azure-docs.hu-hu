---
title: Az Application Health bővítmény használata Azure-beli virtuálisgép-méretezési csoportokkal
description: Ismerje meg, hogyan használható az alkalmazás állapota a virtuálisgép-méretezési csoportokon telepített alkalmazásai állapotának figyelésére.
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: 37d93b04e6755512eac6c2a168bd2a04f8ac298f
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275882"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Az Application Health bővítmény használata virtuálisgép-méretezési csoportokkal
Az alkalmazás állapotának figyelése fontos jel az üzemelő példány kezeléséhez és frissítéséhez. Az Azure virtuálisgép-méretezési csoportok támogatást nyújtanak a [működés közbeni](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) frissítésekhez, beleértve az [automatikus operációsrendszer-képek](virtual-machine-scale-sets-automatic-upgrade.md)frissítéseit is, amelyek az egyes példányok állapotának figyelésére támaszkodnak az üzembe helyezés frissítéséhez.

Ez a cikk azt ismerteti, hogyan használható az alkalmazás állapota bővítmény a virtuálisgép-méretezési csoportokon telepített alkalmazások állapotának figyelésére.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy ismeri a következőket:
-   Azure-beli virtuálisgép- [bővítmények](../virtual-machines/extensions/overview.md)
-   Virtuálisgép-méretezési csoportok [módosítása](virtual-machine-scale-sets-upgrade-scale-set.md)

## <a name="when-to-use-the-application-health-extension"></a>Mikor kell használni az alkalmazás állapota bővítményt
Az alkalmazás állapotának kiterjesztése egy virtuálisgép-méretezési csoport példányain belül történik, és a virtuális gépek állapotáról szóló jelentések a méretezési csoport példányán belül vannak. A bővítményt beállíthatja a mintavételhez egy alkalmazás-végponton, és frissítheti az alkalmazás állapotát az adott példányon. A példány állapotát az Azure ellenőrzi, hogy a példány jogosult-e a frissítési műveletekre.

Mivel a bővítmény a virtuális gépen belülről jelenti az állapotot, a bővítmény olyan helyzetekben használható, ahol az olyan külső mintavételek, mint az alkalmazás-állapotú mintavételek (amelyek az [egyéni Azure Load Balancer-](../load-balancer/load-balancer-custom-probe-overview.md)mintavételeket használják) nem használhatók.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON az alkalmazás állapotára szolgáló bővítmény sémáját jeleníti meg. A bővítményhez legalább egy "TCP" vagy "http" kérelemnek kell tartoznia egy társított porttal vagy kérelem elérési úttal.

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

### <a name="property-values"></a>Tulajdonságértékek

| Name (Név) | Érték/példa | Adattípus
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | dátum |
| közzétevő | `Microsoft.ManagedServices` | sztring |
| type | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | sztring |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Beállítások

| Name (Név) | Érték/példa | Adattípus
| ---- | ---- | ----
| protokoll | `http` vagy `tcp` | sztring |
| port | A protokoll `http`megadása kötelező, ha a protokoll `tcp` | int |
| requestPath | Kötelező, ha a protokoll `http`, nem engedélyezett, ha a protokoll `tcp` | sztring |

## <a name="deploy-the-application-health-extension"></a>Az alkalmazás állapot-kiterjesztésének üzembe helyezése
Az alábbi példákban több módon is üzembe helyezheti az alkalmazás állapotának kiterjesztését a méretezési csoportokban.

### <a name="rest-api"></a>REST API

A következő példa hozzáadja az Application Health-bővítményt (a name myHealthExtension) a Windows-alapú méretezési csoport méretezési csoport modelljében lévő extensionProfile.

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
A már telepített bővítmények szerkesztéséhez használja a `PATCH`.

### <a name="azure-powershell"></a>Azure PowerShell

Az [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmaggal adja hozzá az alkalmazás-állapot bővítményt a méretezési csoport modellje definícióhoz.

Az alábbi példa hozzáadja az alkalmazás-állapot bővítményt a Windows-alapú méretezési csoport méretezési csoport modelljében lévő `extensionProfile`hoz. A példa az új az PowerShell-modult használja.

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

Az az [vmss Extension set](/cli/azure/vmss/extension#az-vmss-extension-set) paranccsal adja hozzá az alkalmazás állapota bővítményt a méretezési csoport modellje definícióhoz.

Az alábbi példa hozzáadja az alkalmazás-állapot bővítményt egy Linux-alapú méretezési csoport méretezési csoport modelljéhez.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
A. JSON kiterjesztésű fájl tartalma.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Hibaelhárítás
A bővítmény-végrehajtás kimenete a következő címtárakban található fájlokra van naplózva:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

A naplók rendszeresen rögzítik az alkalmazás állapotának állapotát is.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [helyezheti üzembe az alkalmazást](virtual-machine-scale-sets-deploy-app.md) a virtuálisgép-méretezési csoportokban.
