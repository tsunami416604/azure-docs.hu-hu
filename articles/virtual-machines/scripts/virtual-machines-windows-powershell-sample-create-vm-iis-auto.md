---
title: "Új-AzVM segítségével az IIS virtuális gép létrehozása az Azure-ban |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - használatával új-AzVM IIS-t futtató virtuális gép létrehozása."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: cynthn
ms.openlocfilehash: 932920483e9196251b125065f8efe68d27dda559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-iis-vm-using-the-new-azvm-powershell-cmdlet-preview"></a>Az IIS virtuális gép létrehozása a New-AzVM PowerShell parancsmag (előzetes verzió)

Ezt a parancsfájlt hoz létre a Windows Server 2016 rendszert futtató Azure virtuális gépeket, és az Azure virtuális gép egyéni parancsprogramok futtatására szolgáló bővítmény használatával telepítse az IIS. A parancsfájl futtatása után érheti el az alapértelmezett IIS webhelyet a nyilvános IP-címhez, a virtuális gép. 

Ez a minta a New-AzVM parancsmagot, amely jelenleg előzetes verzióban érhető használja. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

## <a name="sample-script"></a>Mintaparancsfájl

```azurepowershell-interactive

New-AzVM -Name myVM `
    -VirtualNetworkName myVNet `
    -Location westeurope `
    -SecurityGroupName myNSG `
    -PublicIpAddressName myPIP


# Create an inbound network security group rule for port 80

$nsgRuleHTTP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleHTTP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 80 -Access Allow

$nsg = Get-AzureRmNetworkSecurityGroup -Name myNSG -ResourceGroupName myVMResourceGroup
$nsg | Add-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleHTTP -NetworkSecurityGroup myNSG 
$nsg | Set-AzureRmNetworkSecurityGroup

# Install IIS

Set-AzureRmVMExtension -ExtensionName "IIS" -ResourceGroupName myVMResourceGroup -VMName myVM `
  -Publisher "Microsoft.Compute" -ExtensionType "CustomScriptExtension" -TypeHandlerVersion 1.4 `
  -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' -Location westeurope
  
```

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a központi telepítés létrehozásához. A parancs adott dokumentáció tábla mutató összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [Új AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Létrehoz egy hálózati biztonsági csoport szabály konfigurációt. Ez a konfiguráció segítségével egy NSG-szabály létrehozása az NSG létrehozásakor. |
| [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup) | NSG-információk lekérése. |
| [Adja hozzá AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig) | Az új szabály hozzáadása a konfigurációt. |
| [Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/Set-AzureRmNetworkSecurityGroup) | Az NSG-t frissíti az új szabályt. |
| [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) | A virtuális gépet egy Virtuálisgép-bővítmény hozzáadása. Ez a példa az egyéni parancsprogramok futtatására szolgáló bővítmény segítségével telepítse az IIS. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolítja az erőforráscsoportot és belül található összes erőforrást. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
