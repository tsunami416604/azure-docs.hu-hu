---
title: "Az Azure PowerShell-parancsfájl minta - exportálási vagy másolási pillanatképének, virtuális Merevlemezt egy másik régióban található tárfiókot |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - exportálási vagy másolási pillanatképének VHD-t, a tárfiók ugyanabban a különböző régióban"
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
ms.openlocfilehash: be21a891121df1d645b430d87b572cde6c945d61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Exportálás/másolás felügyelt pillanatfelvételek VHD-t, a tárfiók más régióban a PowerShell használatával

Ez a parancsfájl egy másik régióban található tárfiókot felügyelt pillanatkép exportálja. Először hoz létre a SAS URI-jának a pillanatképet, és azt használja egy másik régióban található tárfiókot másolásához. Ez a parancsfájl segítségével biztonsági mentés a felügyelt lemezek vész-helyreállítási másik régióban.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

Ez a parancsfájl SAS URI-t a felügyelt pillanatkép létrehozásához a következő parancsok használja, és másolja a pillanatkép SAS URI-t használó tárfiókot. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Támogatás-AzureRmSnapshotAccess](/powershell/module/azurerm.compute/New-AzureRmDisk) | A pillanatkép másolja azt a tárfiók használt állít elő, SAS URI-t. |
| [Új AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | A tárfiók környezetét a fióknevet és a kulcs segítségével hoz létre. Ebben a környezetben a tárfiók olvasási/írási műveletek végrehajtásához használható. |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Másolja az alapul szolgáló virtuális merevlemez a pillanatfelvétel a storage-fiók |

## <a name="next-steps"></a>Következő lépések

[Felügyelt lemezes virtuális merevlemez létrehozása](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[A felügyelt lemezes virtuális gép létrehozása](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).