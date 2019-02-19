---
title: Az Azure Policy használatával korlátozhatja a VM-bővítmény telepítése |} A Microsoft Docs
description: Az Azure Policy használatával korlátozza a bővítmény központi telepítések.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: 4dc68127f2d19426c372be027634bb2563dbfa6c
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341657"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Az Azure Policy használatával korlátozhatja a bővítmények telepítése Windows virtuális gépeken

Ha azt szeretné, hogy használatát vagy a Windows virtuális gépek az egyes bővítmények telepítését, létrehozhat egy Azure szabályzat bővítmények korlátozása a virtuális gépek erőforráscsoporton belül a PowerShell használatával. 

Ebben az oktatóanyagban az Azure PowerShell belül a Cloud Shell, amely folyamatosan frissül a legújabb verzióra. 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="create-a-rules-file"></a>Hozzon létre egy szabályok fájlt

Korlátozhatja a telepíthető bővítmények, rendelkeznie kell egy [szabály](../../governance/policy/concepts/definition-structure.md#policy-rule) azonosíthatja a bővítményt a logika biztosít.

Ez a példa bemutatja, hogyan szabályok fájl létrehozásával az Azure Cloud Shellben "Microsoft.Compute" által közzétett bővítmények megtagadása, de ha a PowerShell helyileg dolgozik, is hozzon létre egy helyi fájlt és az elérési út ($home/clouddrive) az elérési útját a helyi fájlt a gépén.

Az egy [Cloud Shell](https://shell.azure.com/powershell), írja be:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Másolja és illessze be a következő .JSON kiterjesztésű fájlba.

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

Amikor végzett, nyomja le az **Ctrl + O** , majd **Enter** szeretné menteni a fájlt. Találati **Ctrl + X** gombra kattintva zárja be a fájl- és kilépési.

## <a name="create-a-parameters-file"></a>A paraméterfájl létrehozása

Is szükség van egy [paraméterek](../../governance/policy/concepts/definition-structure.md#parameters) fájlt, amely számára, hogy az blokkolja a kiterjesztések listája megadásának struktúrát hoz létre. 

Ez a példa bemutatja, hogyan hozhat létre olyan paramétereket tartalmazó fájlt a Cloud Shell-beli virtuális gépek, de ha a PowerShell helyileg dolgozik, is hozzon létre egy helyi fájlt és az elérési út ($home/clouddrive) az a számítógépen a helyi fájl elérési útját.

A [Cloud Shell](https://shell.azure.com/powershell), írja be:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Másolja és illessze be a következő .JSON kiterjesztésű fájlba.

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

Amikor végzett, nyomja le az **Ctrl + O** , majd **Enter** szeretné menteni a fájlt. Találati **Ctrl + X** gombra kattintva zárja be a fájl- és kilépési.

## <a name="create-the-policy"></a>A szabályzat létrehozása

Szabályzat-definíció egy olyan objektum, a konfigurációt, amely a használni kívánt tárolja. A szabályzatdefiníció a szabályzat meghatározására szabályok és a paraméterek-fájlokat használja. Hozzon létre egy szabályzat definíciója a [New-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition) parancsmagot.

 A szabályok és a paraméterek a létrehozott és a cloud shellben .JSON kiterjesztésű fájlként tárolja a fájlokat.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>A szabályzat hozzárendelése

Ebben a példában a szabályzatot rendel egy erőforrás csoport használatával [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment). A létrehozott virtuális Gépeket a **myResourceGroup** erőforráscsoport nem fogja tudni telepíteni a Virtuálisgép-hozzáférési ügynök vagy egyéni parancsfájl-bővítmények. 

Használja a [Get-AzSubscription |} Táblázat formázása](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) -parancsmaggal beolvasható az előfizetés-Azonosítóját használja egy, a példában helyett.

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

## <a name="test-the-policy"></a>A házirend tesztelése

Ha tesztelni szeretné a szabályzatot, próbálja meg használni a Virtuálisgép-hozzáférési bővítmény. A következő üzenettel kell sikertelen "Set-AzVMAccessExtension: Erőforrás "myVMAccess" szabályzat tiltja."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

A portálon a jelszó módosítása kell sikertelen, és az "a sablon telepítése nem sikerült szabályzat megsértése miatt." az üzenet.

## <a name="remove-the-assignment"></a>A hozzárendelés eltávolítása

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Távolítsa el a szabályzatot

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>További lépések
További információkért lásd: [Azure Policy](../../governance/policy/overview.md).
