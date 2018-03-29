---
title: Azure-házirend segítségével korlátozhatja a Virtuálisgép-bővítmény telepítése |} Microsoft Docs
description: Azure-házirend segítségével bővítmény központi telepítéseket korlátozza.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: danis;cynthn
ms.openlocfilehash: da5b0db997ba1aa0e998f6fe2645e955b490951d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Azure-házirend segítségével korlátozhatja a bővítmények telepítése a Windows virtuális gépeken

Ha meg szeretné akadályozni, hogy használatát vagy a Windows-alapú virtuális gépek bizonyos bővítmények telepítése, létrehozhat egy Azure szabályzatot bővítmények korlátozása a virtuális gépek erőforráscsoporton belül a PowerShell használatával. 

Ebben az oktatóanyagban szereplő Azure PowerShell belül a felhő felület, amely folyamatosan frissül a legújabb verzióra. PowerShell telepítéséhez és használatához helyileg választja, az oktatóanyag az Azure PowerShell 3,6 vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. 

## <a name="create-a-rules-file"></a>Szabályok fájl létrehozása

Ahhoz, hogy korlátozza a bővítményekről telepíthető, rendelkeznie kell egy [szabály](/azure/azure-policy/policy-definition#policy-rule) arra, hogy azonosítsa a kiterjesztést a logikát.

Ez a példa bemutatja, hogyan Azure Cloud rendszerhéj szabályok fájlként "Microsoft.Compute" által közzétett bővítmények megtagadása, de ha dolgozik a PowerShellben helyileg, is hozzon létre egy helyi fájlt és az elérési út ($home/clouddrive) az elérési útját a helyi fájl a számítógépre.

Az egy [felhő rendszerhéj](https://shell.azure.com/powershell), típus:

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

Amikor elkészült, kattintson a **Ctrl + O** , majd **Enter** fájl mentéséhez. Találati **Ctrl + X** a fájl- és Kilépés gombra.

## <a name="create-a-parameters-file"></a>Paraméterek fájl létrehozása

Emellett szükség van egy [paraméterek](/azure/azure-policy/policy-definition#parameters) fájlt, amely blokkolja a sikeres a bővítmények listájának használatára struktúrát hoz létre. 

Ez a példa bemutatja, hogyan hozzon létre egy paraméterfájl rendszerhéj felhő virtuális gépek, de ha dolgozik a PowerShellben helyileg, is hozzon létre egy helyi fájlt és az elérési út ($home/clouddrive) és a számítógépre a helyi fájl elérési útját.

A [felhő rendszerhéj](https://shell.azure.com/powershell), típus:

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

Amikor elkészült, kattintson a **Ctrl + O** , majd **Enter** fájl mentéséhez. Találati **Ctrl + X** a fájl- és Kilépés gombra.

## <a name="create-the-policy"></a>A házirend létrehozása

Házirend-definíció egy objektum tárolására használni kívánt konfigurációról. A házirend-definíció a házirend meghatározásához a szabályok és a paraméterek fájlokat használja. Hozzon létre egy definition házirendekkel a [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) parancsmag.

 A szabályok és a paraméterek olyan a létrehozott és a felhő rendszerhéj .JSON kiterjesztésű fájlként tárolódik.


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>A házirend kijelölése

Ebben a példában a házirendet hozzárendeli egy erőforrás csoport használatával [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). A létrehozott bármely virtuális gép a **myResourceGroup** erőforráscsoport nem lesz képes a hozzáférési ügynököt vagy egyéni parancsfájl-bővítményt telepíteni. 

Használja a [Get-AzureRMSubscription |} Táblázat formázása](/powershell/module/azurerm.profile/get-azurermsubscription) parancsmagot, hogy megkapja az előfizetés-Azonosítóval, a példában egy helyett.

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

Tesztelje a házirendet, próbálja meg a virtuális gép hozzáférés bővítmény használatához. A következő üzenettel kell sikertelen "Set-AzureRmVMAccessExtension:"myVMAccess"erőforrás házirend tiltja."

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

A portálon a jelszó módosítása kell sikertelen, és a "sablon-üzembehelyezés sikertelen megsértése miatt." Üzenet.

## <a name="remove-the-assignment"></a>A hozzárendelés eltávolítása

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>A házirend eltávolítása

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>További lépések
További információkért lásd: [Azure házirend](../../azure-policy/azure-policy-introduction.md).
