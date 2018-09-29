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
ms.openlocfilehash: 15a6a7f4753d51118d23d2e3c021010218d2d2d7
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451833"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Az Azure Policy használatával korlátozhatja a bővítmények telepítése Windows virtuális gépeken

Ha azt szeretné, hogy használatát vagy a Windows virtuális gépek az egyes bővítmények telepítését, létrehozhat egy Azure szabályzat bővítmények korlátozása a virtuális gépek erőforráscsoporton belül a PowerShell használatával. 

Ebben az oktatóanyagban az Azure PowerShell belül a Cloud Shell, amely folyamatosan frissül a legújabb verzióra. Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. 

## <a name="create-a-rules-file"></a>Hozzon létre egy szabályok fájlt

Korlátozhatja a telepíthető bővítmények, rendelkeznie kell egy [szabály](/azure/azure-policy/policy-definition#policy-rule) azonosíthatja a bővítményt a logika biztosít.

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

Is szükség van egy [paraméterek](/azure/azure-policy/policy-definition#parameters) fájlt, amely számára, hogy az blokkolja a kiterjesztések listája megadásának struktúrát hoz létre. 

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

Szabályzat-definíció egy olyan objektum, a konfigurációt, amely a használni kívánt tárolja. A szabályzatdefiníció a szabályzat meghatározására szabályok és a paraméterek-fájlokat használja. Hozzon létre egy szabályzat definíciója a [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) parancsmagot.

 A szabályok és a paraméterek a létrehozott és a cloud shellben .JSON kiterjesztésű fájlként tárolja a fájlokat.


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>A szabályzat hozzárendelése

Ebben a példában a szabályzatot rendel egy erőforrás csoport használatával [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). A létrehozott virtuális Gépeket a **myResourceGroup** erőforráscsoport nem fogja tudni telepíteni a Virtuálisgép-hozzáférési ügynök vagy egyéni parancsfájl-bővítmények. 

Használja a [Get-AzureRMSubscription |} Táblázat formázása](/powershell/module/azurerm.profile/get-azurermsubscription) -parancsmaggal beolvasható az előfizetés-Azonosítóját használja egy, a példában helyett.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzureRMPolicyAssignment `
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

Ha tesztelni szeretné a szabályzatot, próbálja meg használni a Virtuálisgép-hozzáférési bővítmény. A következő üzenettel kell sikertelen "Set-AzureRmVMAccessExtension: erőforrás"myVMAccess"szabályzat tiltja."

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

A portálon a jelszó módosítása kell sikertelen, és az "a sablon telepítése nem sikerült szabályzat megsértése miatt." az üzenet.

## <a name="remove-the-assignment"></a>A hozzárendelés eltávolítása

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Távolítsa el a szabályzatot

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>További lépések
További információkért lásd: [Azure Policy](../../azure-policy/azure-policy-introduction.md).
