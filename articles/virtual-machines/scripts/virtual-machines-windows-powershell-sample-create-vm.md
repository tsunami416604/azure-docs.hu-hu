---
title: "Az Azure PowerShell-parancsfájl minták – Windows virtuális gép létrehozása |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minták – Windows virtuális gép létrehozása"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a9e46aded0cf3792558a7fba6f00e5662166cc0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-fully-configured-virtual-machine-with-powershell"></a>A teljesen konfigurált virtuális gép létrehozása a PowerShell használatával

Ezt a parancsfájlt hoz létre a Windows Server 2016 rendszert futtató Azure virtuális gépeket. A parancsfájl futtatása után a virtuális gép RDP keresztül érheti el.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1 "Create VM detailed")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a központi telepítés létrehozásához. A parancs adott dokumentáció tábla mutató összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [Új-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [Új AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Létrehoz egy alhálózati konfigurációt. Ezt a konfigurációt használja a virtuális hálózat létrehozásának folyamatát. |
| [Új-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Virtuális hálózat létrehozása. |
| [Új AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Létrehoz egy nyilvános IP-címet. |
| [Új AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Létrehoz egy hálózati biztonsági csoport szabály konfigurációt. Ez a konfiguráció segítségével egy NSG-szabály létrehozása az NSG létrehozásakor. |
| [Új AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Hálózati biztonsági csoportot hoz létre. |
| [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) | Lekérdezi az alhálózati adatokat. Ezt az információt a hálózati illesztő létrehozása során használatos. |
| [Új AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Létrehoz egy adott hálózati csatoló. |
| [Új AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Létrehoz egy Virtuálisgép-konfiguráció. Ez a konfiguráció tartoznak a virtuális gép nevét, az operációs rendszer és a rendszergazdai hitelesítő adatokkal. A konfiguráció a Virtuálisgép-létrehozása során használatos. |
| [Új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Hozzon létre egy virtuális gépet. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolítja az erőforráscsoportot és belül található összes erőforrást. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
