---
title: VHD-pillanatkép több azonos felügyelt lemez létrehozásához (Linux) – PowerShell
description: Azure PowerShell-példaszkript – Pillanatkép létrehozása virtuális merevlemezről több azonos felügyelt lemez gyors létrehozásához
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 8a80056ed3368f6a2a5c4ad7fa31424524e824b2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079927"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-linux"></a>Pillanatkép létrehozása virtuális merevlemezről több azonos felügyelt lemez létrehozásához a PowerShell (Linux) használatával kis idő alatt

Ez a szkript létrehoz egy pillanatképet egy VHD-fájlról egy azonos vagy eltérő előfizetéshez tartozó tárfiókban. A szkript segítségével importálhat egy speciális (nem általánosított vagy syspreppel előkészített) virtuális merevlemezt egy pillanatképbe, majd a pillanatképpel létrehozhat több, egymással azonos felügyelt lemezt rövid idő alatt. Emellett használhatja arra is, hogy importál egy adatokat tartalmazó virtuális merevlemezt egy pillanatképbe, majd a pillanatképpel létrehoz több felügyelt lemez rövid idő alatt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsok használatával hoz létre felügyelt lemezt egy VHD-ból egy másik előfizetésben. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Új – AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Létrehoz egy lemezkonfigurációt lemezlétrehozáshoz. Tartalmazza a tárolás típusát, a helyet, a szülő VHD-fájlt tároló tárfiók erőforrás-azonosítóját, valamint a szülő VHD URI-ját. |
| [Új – AzDisk](/powershell/module/az.compute/new-azdisk) | Létrehoz egy lemezt a paraméterként megadott lemezkonfiguráció, lemeznév és erőforráscsoport-név alapján. |

## <a name="next-steps"></a>További lépések

[Felügyelt lemez létrehozása pillanatképből](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
