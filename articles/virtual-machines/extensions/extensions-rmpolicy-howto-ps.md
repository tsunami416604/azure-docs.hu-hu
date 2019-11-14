---
title: A virtuálisgép-bővítmények telepítésének korlátozása Azure Policy használatával
description: A bővítmények üzembe helyezésének korlátozásához használja a Azure Policy.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 428db340ce43463939ce71ffadd4188060f3e732
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073118"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>A bővítmények Windows rendszerű virtuális gépeken való telepítésének korlátozása Azure Policy használatával

Ha meg szeretné akadályozni bizonyos bővítmények használatát vagy telepítését a Windows rendszerű virtuális gépeken, létrehozhat egy Azure-szabályzatot a PowerShell használatával, és korlátozhatja a virtuális gépek bővítményeit az erőforráscsoporthoz. 

Ez az oktatóanyag Azure PowerShellt használ a Cloud Shellon belül, amely folyamatosan frissül a legújabb verzióra. 

 

## <a name="create-a-rules-file"></a>Szabály létrehozása fájl

A bővítmények telepítésének korlátozásához rendelkeznie kell egy olyan [szabállyal](../../governance/policy/concepts/definition-structure.md#policy-rule) , amely megadja a logikát a bővítmény azonosításához.

Ebből a példából megtudhatja, hogyan tagadhatja meg a "Microsoft. számítás" által közzétett bővítményeket egy szabály fájljának létrehozásával Azure Cloud Shellban, de ha helyileg dolgozik a PowerShellben, létrehozhat egy helyi fájlt is, és lecserélheti az elérési utat ($home/clouddrive) a következő elérési úttal: helyi fájl a gépen.

A [Cloud Shell](https://shell.azure.com/powershell)írja be a következőt:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Másolja és illessze be a következő. JSON fájlt a fájlba.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Ha elkészült, nyomja le a **CTRL + O billentyűkombinációt** , majd a fájl mentéséhez **írja be** a következőt:. Nyomja le a **CTRL + X** billentyűkombinációt a fájl bezárásához és a kilépéshez.

## <a name="create-a-parameters-file"></a>Parameters-fájl létrehozása

Szükség van egy [paraméter](../../governance/policy/concepts/definition-structure.md#parameters) -fájlra is, amely létrehoz egy struktúrát, amellyel elvégezhető a letiltani kívánt bővítmények listájának átadása. 

Ebből a példából megtudhatja, hogyan hozhat létre paramétereket a virtuális gépekhez Cloud Shellban, de ha helyileg dolgozik a PowerShellben, létrehozhat egy helyi fájlt is, és lecserélheti az elérési utat ($home/clouddrive) a helyi fájl elérési útjára a gépen.

A [Cloud Shell](https://shell.azure.com/powershell)írja be a következőt:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Másolja és illessze be a következő. JSON fájlt a fájlba.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Ha elkészült, nyomja le a **CTRL + O billentyűkombinációt** , majd a fájl mentéséhez **írja be** a következőt:. Nyomja le a **CTRL + X** billentyűkombinációt a fájl bezárásához és a kilépéshez.

## <a name="create-the-policy"></a>A szabályzat létrehozása

A házirend-definíció a használni kívánt konfiguráció tárolására szolgáló objektum. A házirend-definíció a szabályok és paraméterek fájlok használatával határozza meg a szabályzatot. Hozzon létre egy szabályzat-definíciót a [New-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition) parancsmag használatával.

 A házirend-szabályok és paraméterek a létrehozott és. JSON-fájlként tárolt fájlok a Cloud shellben.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>A szabályzat kiosztása

Ez a példa a szabályzatot egy erőforráscsoporthoz rendeli hozzá a [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment)használatával. A **myResourceGroup** erőforráscsoporthoz létrehozott összes virtuális gép nem tudja telepíteni a virtuálisgép-hozzáférési ügynököt vagy az egyéni parancsfájl-bővítményeket. 

A [Get-AzSubscription használata | Formázza a Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) parancsmagot, hogy az előfizetés-azonosítót a példában szereplő egyik helyett használja.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>A szabályzat tesztelése

A szabályzat teszteléséhez próbálja meg használni a virtuális gép elérési bővítményét. A következőnek sikertelennek kell lennie, ha a házirend nem engedélyezte a "set-AzVMAccessExtension: Resource" myVMAccess "üzenetet."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

A portálon a jelszó módosításának sikertelennek kell lennie a "a sablon központi telepítése sikertelen volt a szabályzat megsértése miatt." üzenetet.

## <a name="remove-the-assignment"></a>A hozzárendelés eltávolítása

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>A házirend eltávolítása

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>További lépések
További információ: [Azure Policy](../../governance/policy/overview.md).
