---
title: Felügyelt lemez létrehozása egy VHD-fájlból egy előfizetés-PowerShell-minta Storage-fiókjában
description: Azure PowerShell-példaszkript – Felügyelt lemez létrehozása VHD-fájlból azonos vagy eltérő előfizetéshez tartozó tárfiókban
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 1d743d840afc4505cdc39b258b21856b6c3e7aa9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89323146"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Felügyelt lemez létrehozása VHD-fájlból azonos vagy eltérő előfizetéshez tartozó tárfiókban a PowerShell segítségével

Ez a szkript létrehoz egy felügyelt lemezt egy VHD-fájlból egy azonos vagy eltérő előfizetéshez tartozó tárfiókban. A szkript használatával egy specializált (nem általános/Sysprep használatával létrehozott) virtuális merevlemezt importálhat egy felügyelt rendszerlemezre egy virtuális gép létrehozása céljából. Emellett a szkripttel virtuális adatlemezt is importálhat egy felügyelt lemezre. 

Ne hozzon létre több azonos felügyelt lemezt VHD-fájlból rövid idő alatt. Felügyelt lemezek VHD-fájlból való létrehozásakor blob-pillanatkép jön létre a VHD-fájlból, és ennek használatával hozza létre a rendszer a felügyelt lemezeket. Csak egy blob-pillanatkép hozható létre egy perc alatt, mely lemez-létrehozási hibákhoz vezethet teljesítménykorlátozó leszabályozás miatt. Ennek a leszabályozásnak az elkerüléséhez hozzon létre egy [felügyelt pillanatképet a VHD-fájlból](virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json). Ezután a felügyelt pillanatkép segítségével már hozhat létre rövid idő alatt több felügyelt lemezt. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsok használatával hoz létre felügyelt lemezt egy VHD-ból egy másik előfizetésben. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Új – AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Létrehoz egy lemezkonfigurációt lemezlétrehozáshoz. Magában foglalja a tároló típusát, helyét, erőforrás-AZONOSÍTÓját, valamint azt a Storage-fiókot, ahol a szülő virtuális merevlemezt tárolják, a szülő virtuális merevlemez VHD URI-JÁT. |
| [Új – AzDisk](/powershell/module/az.compute/new-azdisk) | Létrehoz egy lemezt a paraméterként megadott lemezkonfiguráció, lemeznév és erőforráscsoport-név alapján. |

## <a name="next-steps"></a>További lépések

[Virtuális gép létrehozása felügyelt lemez rendszerlemezként való csatlakoztatásával](virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
