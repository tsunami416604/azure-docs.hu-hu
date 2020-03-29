---
title: Alkalmazásállapot-bővítmény használata az Azure virtuálisgép-méretezési készleteivel
description: Megtudhatja, hogyan használhatja az Alkalmazásállapot bővítményt a virtuálisgép-méretezési csoportokban telepített alkalmazások állapotának figyelésére.
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: 37d93b04e6755512eac6c2a168bd2a04f8ac298f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275882"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Az Application Health bővítmény használata virtuálisgép-méretezési csoportokkal
Az alkalmazás állapotának figyelése fontos jelzés a központi telepítés kezeléséhez és frissítéséhez. Az Azure virtuálisgép-méretezési készletek támogatják a [működés közbeni frissítéseket,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) beleértve az [automatikus operációsrendszer-lemezkép-frissítéseket,](virtual-machine-scale-sets-automatic-upgrade.md)amelyek az egyes példányok állapotfigyelésétől függenek a központi telepítés frissítéséhez.

Ez a cikk ismerteti, hogyan használhatja az Alkalmazásállapot bővítmény a virtuálisgép-méretezési csoportokban telepített alkalmazások állapotának figyeléséhez.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy ismeri a következőket:
-   Azure [virtuálisgép-bővítmények](../virtual-machines/extensions/overview.md)
-   [Virtuálisgép-méretezési](virtual-machine-scale-sets-upgrade-scale-set.md) készletek módosítása

## <a name="when-to-use-the-application-health-extension"></a>Mikor kell használni az Alkalmazásállapot bővítményt?
Az Alkalmazásállapot-bővítmény egy virtuálisgép-méretezési csoport példányában van telepítve, és a méretezési csoport példányán belülről jelentést tesz a virtuális gép állapotáról. Konfigurálhatja a bővítményt egy alkalmazásvégpont mintavételére, és frissítheti az alkalmazás állapotát az adott példányon. Ezt a példányállapotot az Azure ellenőrzi annak megállapításához, hogy egy példány jogosult-e a frissítési műveletekre.

Mivel a bővítmény egy virtuális gépállapot-jelentést, a bővítmény olyan helyzetekben használható, ahol a külső mintavételek, például az Application Health Mintavételek (amelyek egyéni Azure Load Balancer [mintavételeket](../load-balancer/load-balancer-custom-probe-overview.md)használnak) nem használhatók.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON az alkalmazásállapot-bővítmény sémáját mutatja be. A bővítmény legalább egy "tcp" vagy "http" kérelmet igényel egy társított porthoz vagy kérelem elérési úthoz.

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

### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték / Példa | Adattípus
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | dátum |
| közzétevő | `Microsoft.ManagedServices` | sztring |
| type | `ApplicationHealthLinux`(Linux), `ApplicationHealthWindows` (Windows) | sztring |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Beállítások

| Név | Érték / Példa | Adattípus
| ---- | ---- | ----
| Protokoll | `http` vagy `tcp` | sztring |
| port | Nem kötelező, `http`ha a protokoll kötelező, ha a protokoll`tcp` | int |
| requestPath | Kötelező, ha `http`a protokoll nem engedélyezett, ha a protokoll`tcp` | sztring |

## <a name="deploy-the-application-health-extension"></a>Az Alkalmazásállapot-bővítmény telepítése
Az alkalmazásállapot-bővítmény méretezési csoportra való üzembe helyezésének több módja is van az alábbi példákban részletezett módon.

### <a name="rest-api"></a>REST API

A következő példa hozzáadja az Alkalmazásállapot-bővítményt (myHealthExtension névvel) az extensionProfile-hoz egy Windows-alapú méretezési készlet modellben.

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
Egy `PATCH` már üzembe helyezett bővítmény szerkesztésére használható.

### <a name="azure-powershell"></a>Azure PowerShell

Az [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmag segítségével adja hozzá az Alkalmazásállapot-bővítményt a méretezési készlet modelldefinícióhoz.

A következő példa hozzáadja az `extensionProfile` Alkalmazásállapot-bővítményt a Windows-alapú méretezési készlet méretezési modelljéhez. A példa az új Az PowerShell-modult használja.

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

Használja [az vmss bővítménykészletet](/cli/azure/vmss/extension#az-vmss-extension-set) az Alkalmazásállapot-bővítmény hozzáadása a méretezési készlet modell definíciójához.

A következő példa hozzáadja az Alkalmazásállapot-bővítményt egy Linux-alapú méretezési készlet méretezési modellhez.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
Az extension.json fájl tartalma.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Hibaelhárítás
A bővítmény-végrehajtási kimenet a következő könyvtárakban található fájlokba kerül:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

A naplók is rendszeresen rögzíti az alkalmazás állapotát.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [telepítheti az alkalmazást](virtual-machine-scale-sets-deploy-app.md) a virtuális gép méretezési csoportok.
