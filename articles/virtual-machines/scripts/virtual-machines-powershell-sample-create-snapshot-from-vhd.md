---
title: VHD-pillanatkép több azonos felügyelt lemez létrehozásához (Windows) – PowerShell
description: Azure PowerShell-példaszkript – Pillanatkép létrehozása virtuális merevlemezről több azonos felügyelt lemez gyors létrehozásához
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 47ade94ad2e0be0ce44d346336c6c7eea4fca78b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89323157"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-windows"></a>Pillanatkép létrehozása virtuális merevlemezről több, azonos felügyelt lemez létrehozásához a PowerShell (Windows) használatával kis idő alatt

Ez a szkript létrehoz egy pillanatképet egy VHD-fájlról egy azonos vagy eltérő előfizetéshez tartozó tárfiókban. A szkript segítségével importálhat egy speciális (nem általánosított vagy syspreppel előkészített) virtuális merevlemezt egy pillanatképbe, majd a pillanatképpel létrehozhat több, egymással azonos felügyelt lemezt rövid idő alatt. Emellett használhatja arra is, hogy importál egy adatokat tartalmazó virtuális merevlemezt egy pillanatképbe, majd a pillanatképpel létrehoz több felügyelt lemez rövid idő alatt. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>További lépések

[Felügyelt lemez létrehozása pillanatképből](virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Virtuális gép létrehozása felügyelt lemez rendszerlemezként való csatlakoztatásával](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
