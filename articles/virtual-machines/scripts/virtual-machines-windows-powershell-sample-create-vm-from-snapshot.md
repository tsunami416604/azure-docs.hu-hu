---
title: "Az Azure PowerShell-parancsfájl minták – hozzon létre egy virtuális Gépet egy pillanatképből |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minták – hozzon létre egy virtuális Gépet egy pillanatképből"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 63d108bbfd0f58f8a40bf1c7c8649e3a1f7ed288
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell"></a>Hozzon létre egy virtuális gépet egy pillanatképből, a PowerShell használatával

Ez a parancsfájl létrehoz egy virtuális gépet egy operációsrendszer-lemez egy pillanatképből. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a pillanatkép tulajdonságainak beolvasása hozzon létre egy felügyelt lemezes pillanatképből, és hozzon létre egy virtuális Gépet. A parancs adott dokumentáció tábla mutató összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/get-azurermsnapshot) | Lekérdezi a pillanatkép-név használatával pillanatképet. |
| [Új AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) | Létrehozza a lemezkonfigurációt. Ez a konfiguráció a létrehozási folyamat használja. |
| [Új AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) | Egy felügyelt lemezt hoz létre. |
| [Új AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Létrehoz egy Virtuálisgép-konfiguráció. Ez a konfiguráció tartoznak a virtuális gép nevét, az operációs rendszer és a rendszergazdai hitelesítő adatokkal. A konfiguráció a Virtuálisgép-létrehozása során használatos. |
| [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | A felügyelt lemezes csatolja az operációsrendszer-lemez, a virtuális gép |
| [Új AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Létrehoz egy nyilvános IP-címet. |
| [Új AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Létrehoz egy adott hálózati csatoló. |
| [Új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Létrehoz egy virtuális gépet. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolítja az erőforráscsoportot és belül található összes erőforrást. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).