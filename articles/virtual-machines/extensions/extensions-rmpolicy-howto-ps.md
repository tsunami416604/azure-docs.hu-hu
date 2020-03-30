---
title: A virtuális gépbővítmény telepítésének korlátozása az Azure-szabályzathasználatával
description: Az Azure Policy használatával korlátozhatja a bővítmények üzembe helyezését.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073118"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Az Azure-szabályzat használata a bővítmények windowsos virtuális gépeken történő telepítésének korlátozásához

Ha meg szeretné akadályozni bizonyos bővítmények használatát vagy telepítését a Windows virtuális gépeken, létrehozhat egy Azure-szabályzatot a PowerShell használatával, amely korlátozza a virtuális gépek egy erőforráscsoporton belüli bővítményeket. 

Ez az oktatóanyag az Azure PowerShellt használja a Felhőrendszerhéjon belül, amely folyamatosan frissül a legújabb verzióra. 

 

## <a name="create-a-rules-file"></a>Szabályfájl létrehozása

Annak érdekében, hogy korlátozza, hogy milyen bővítmények telepíthetők, rendelkeznie kell egy [szabály,](../../governance/policy/concepts/definition-structure.md#policy-rule) amely a bővítmény azonosításához a logika.

Ebben a példában bemutatja, hogyan tagadhatja meg a "Microsoft.Compute" által közzétett bővítményeket egy szabályfájl létrehozásával az Azure Cloud Shellben, de ha helyileg dolgozik a PowerShellben, létrehozhat egy helyi fájlt is, és lecserélheti az elérési utat ($home/clouddrive) a gépen lévő helyi fájl elérési útjára.

A [felhőhéjban](https://shell.azure.com/powershell)írja be a következőt:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Másolja a fájlt a következő .json fájlba.

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

Ha elkészült, nyomja meg a **Ctrl + O billentyűkombinációt,** majd az **Enter billentyűt** a fájl mentéséhez. Nyomja **le a Ctrl + X billentyűkombinációt** a fájl bezárásához és a kilépéshez.

## <a name="create-a-parameters-file"></a>Paraméterfájl létrehozása

Szüksége van egy [paraméterfájlra](../../governance/policy/concepts/definition-structure.md#parameters) is, amely létrehoz egy struktúrát, amelyet a letiltandó bővítmények listájának átadásához használhat. 

Ez a példa bemutatja, hogyan hozhat létre paraméterfájlt a virtuális gépekhez a Cloud Shellben, de ha helyileg dolgozik a PowerShellben, létrehozhat egy helyi fájlt is, és lecserélheti az elérési utat ($home/clouddrive) a helyi fájl elérési útját a gépen.

A [Cloud Shell](https://shell.azure.com/powershell)mezőbe írja be a következőt:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Másolja a fájlt a következő .json fájlba.

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

Ha elkészült, nyomja meg a **Ctrl + O billentyűkombinációt,** majd az **Enter billentyűt** a fájl mentéséhez. Nyomja **le a Ctrl + X billentyűkombinációt** a fájl bezárásához és a kilépéshez.

## <a name="create-the-policy"></a>A házirend létrehozása

A házirend-definíció olyan objektum, amely a használni kívánt konfiguráció tárolására szolgál. A házirend-definíció a szabályok és a paraméterek fájljait használja a házirend meghatározásához. Hozzon létre egy házirend-definíciót a [New-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition) parancsmag használatával.

 A házirendszabályok és -paraméterek a .json fájlokként létrehozott és a felhőbeli rendszerhéjban tárolt fájlok.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>A szabályzat hozzárendelése

Ez a példa a házirendet egy erőforráscsoporthoz rendeli a [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment)használatával. A **myResourceGroup** erőforráscsoportban létrehozott virtuális gépek nem tudják telepíteni a Virtuálisgép-hozzáférési ügynököt vagy az Egyéni parancsfájl-bővítményeket. 

A [Get-AzSubscription használata | Format-Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) parancsmag az előfizetés-azonosító használatának lehetővé teszi a példában szereplő helyett.

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

A házirend teszteléséhez próbálja meg használni a Virtuálisgép-hozzáférési bővítményt. A következő nem lehet a "Set-AzVMAccessExtension : Resource 'myVMAccess" volt, nem engedélyezett a házirend."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

A portálon a jelszó módosítása sikertelen nek kell lennie a "A sablon központi telepítése nem sikerült, mert a házirend megsértése." üzenetet küldi vissza.

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
