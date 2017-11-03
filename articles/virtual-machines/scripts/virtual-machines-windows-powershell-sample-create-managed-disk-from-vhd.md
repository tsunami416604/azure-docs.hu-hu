---
title: "Az Azure PowerShell-parancsfájl minta - hozhat létre egy felügyelt lemezt egy VHD-fájl ugyanazon vagy másik előfizetés tárfiókokban |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - hozhat létre egy felügyelt lemezt egy VHD-fájlt olyan tárfiókban ugyanazon vagy másik előfizetésben található"
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
ms.openlocfilehash: 6d37fb1308ce0b866b42f961ada84d0869f25615
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Felügyelt lemezes egy tárfiókot, a PowerShell segítségével az ugyanazon vagy másik előfizetést a VHD-fájl létrehozása

Ezt a parancsfájlt egy felügyelt lemezt egy VHD-fájlt olyan tárfiókban ugyanazon vagy másik előfizetést hoz létre. Ezen parancsfájl segítségével importálja a speciális (nem általánosított/Sysprep használatával előkészített) felügyelt operációsrendszer-lemez VHD-egy virtuális gép létrehozásához. Használja továbbá az adatimportálás egy virtuális merevlemez felügyelt adatok lemezre. 

Ne hozzon létre több azonos felügyelt lemezeket a VHD-fájl kis időn belül. Felügyelt lemezek létrehozásához egy vhd-fájlt a blob pillanatképet készíteni a vhd-fájl jön létre, és felügyelt lemezek létrehozásához használt majd. Csak egy blob pillanatkép létrehozása lemezhiba szabályozás miatt okozó egy percen belül hozhatók létre. A sávszélesség-szabályozás elkerüléséhez hozzon létre egy [felügyelt pillanatfelvételt a vhd-fájlt a](virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) és majd a felügyelt pillanatképének létrehozása több kezelt lemezek rövid időn. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához a helyi, ez az oktatóanyag van szükség, hogy-e az Azure PowerShell 4.0-s vagy újabb verziója. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha szeretné telepíteni vagy frissíteni, lásd: [Azure PowerShell telepítése](/powershell/azure/install-azurerm-ps). Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsok hozhat létre egy felügyelt lemezt egy másik előfizetésben található virtuális merevlemez. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | A lemez létrehozásához használt lemezkonfiguráció hoz létre. Ez magában foglalja a tárolási típus, hely, erőforrás a tárfiók a szülő virtuális merevlemez tárolásához, a szülő virtuális Merevlemezt a virtuális merevlemez URI azonosítója. |
| [Új AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Lemezkonfiguráció, a lemez neve és a paraméterként erőforráscsoport-név használatával lemezt hozott létre. |

## <a name="next-steps"></a>Következő lépések

[Virtuális gép létrehozása az operációs rendszer lemezeként felügyelt lemezes csatolásával](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).