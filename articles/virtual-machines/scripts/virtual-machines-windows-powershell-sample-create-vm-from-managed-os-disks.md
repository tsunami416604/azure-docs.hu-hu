---
title: Az Azure PowerShell-parancsfájl minta - virtuális gép létrehozása az operációs rendszer lemezeként felügyelt lemezes csatolásával |} Microsoft Docs
description: Az Azure PowerShell-parancsfájl minta - virtuális gép létrehozása az operációs rendszer lemezeként felügyelt lemezes csatolásával
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ec532811e94647c8a04b9faf9474f6749969f83e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23928215"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Hozzon létre egy virtuális gép egy meglévő felügyelt operációsrendszer-lemez a PowerShell használatával

Ez a parancsfájl által az operációs rendszer lemezeként felügyelt meglévő lemez csatolása létrehoz egy virtuális gépet. Használja ezt a parancsfájlt előző forgatókönyvek:
* Egy meglévő felügyelt operációsrendszer-lemez, amely egy másik előfizetésben található felügyelt lemezes másolta a virtuális gép létrehozása
* Lemezről egy meglévő felügyelt speciális VHD-fájl alapján létrehozott virtuális gép létrehozása 
* Egy meglévő felügyelt operációsrendszer-lemez, amely egy pillanatképből hozták létre a virtuális gép létrehozása 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a felügyelt lemezes tulajdonságait, egy felügyelt lemezt csatolni a új virtuális gép, és hozzon létre egy virtuális Gépet. A parancs adott dokumentáció tábla mutató összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmDisk](/powershell/module/azurerm.compute/Get-AzureRmDisk) | Lekérdezi a neve és az erőforráscsoport egy lemez alapú objektumát. A visszaadott objektumát tulajdonsága használt csatolni a lemezt egy új virtuális gép |
| [Új AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Létrehoz egy Virtuálisgép-konfiguráció. Ez a konfiguráció tartoznak a virtuális gép nevét, az operációs rendszer és a rendszergazdai hitelesítő adatokkal. A konfiguráció a Virtuálisgép-létrehozása során használatos. |
| [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | Csatolja az Id tulajdonság, a lemez használatával új virtuális gép operációsrendszer-lemez felügyelt lemezes |
| [Új AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Létrehoz egy nyilvános IP-címet. |
| [Új AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Létrehoz egy adott hálózati csatoló. |
| [Új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Hozzon létre egy virtuális gépet. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolítja az erőforráscsoportot és belül található összes erőforrást. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).