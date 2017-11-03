---
title: "Az Azure PowerShell-parancsfájl minta - pillanatképet készíteni virtuális Merevlemezét kis időn belül több azonos felügyelt lemezek létrehozásához |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - pillanatkép létrehozása a virtuális merevlemez létrehozása több, azonos felügyelt lemezek kis időn belül"
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 4cd6d9cc4f2b1fa41530349c957e180e2513586e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Pillanatkép létrehozása olyan virtuális merevlemezről több azonos kezelt lemez létrehozása a PowerShell-lel kis időn belül

Ez a parancsfájl pillanatképet hoz létre a VHD-fájl ugyanazon vagy másik előfizetés tárfiókokban. Ezen parancsfájl segítségével importálja a speciális (nem általánosított/Sysprep használatával előkészített) VHD-fájlt egy pillanatképet, majd használja a pillanatkép létrehozásához több azonos által kezelt lemezeken kis időn belül. Használja továbbá azt is adatimportálás egy virtuális Merevlemezt egy pillanatképre, majd a pillanatkép létrehozása több felügyelt lemezek kis időn belül. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához a helyi, ez az oktatóanyag van szükség, hogy-e az Azure PowerShell 4.0-s vagy újabb verziója. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha szeretné telepíteni vagy frissíteni, lásd: [Azure PowerShell telepítése](/powershell/azure/install-azurerm-ps). Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsok hozhat létre egy felügyelt lemezt egy másik előfizetésben található virtuális merevlemez. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | A lemez létrehozásához használt lemezkonfiguráció hoz létre. Ez magában foglalja a tárolási típus, hely, erőforrás-azonosítót a szülő virtuális Merevlemezt tároló tárfióknak és a szülő virtuális Merevlemezt a virtuális merevlemez URI. |
| [Új AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Lemezkonfiguráció, a lemez neve és a paraméterként erőforráscsoport-név használatával lemezt hozott létre. |

## <a name="next-steps"></a>Következő lépések

[Hozzon létre egy felügyelt lemezes pillanatképből](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Virtuális gép létrehozása az operációs rendszer lemezeként felügyelt lemezes csatolásával](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).