---
title: Azure PowerShell-példaszkript – Pillanatkép létrehozása virtuális merevlemezről több, egymással azonos felügyelt lemez gyors létrehozásához | Microsoft Docs
description: Azure PowerShell-példaszkript – Pillanatkép létrehozása virtuális merevlemezről több azonos felügyelt lemez gyors létrehozásához
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: fbadf7e26b847043013c033f8f0d277c5e404ac8
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248736"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Pillanatkép létrehozása virtuális merevlemezről több, egymással azonos felügyelt lemez gyors létrehozásához a PowerShell segítségével

Ez a szkript létrehoz egy pillanatképet egy VHD-fájlról egy azonos vagy eltérő előfizetéshez tartozó tárfiókban. A szkript segítségével importálhat egy speciális (nem általánosított vagy syspreppel előkészített) virtuális merevlemezt egy pillanatképbe, majd a pillanatképpel létrehozhat több, egymással azonos felügyelt lemezt rövid idő alatt. Emellett használhatja arra is, hogy importál egy adatokat tartalmazó virtuális merevlemezt egy pillanatképbe, majd a pillanatképpel létrehoz több felügyelt lemez rövid idő alatt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsok használatával hoz létre felügyelt lemezt egy VHD-ból egy másik előfizetésben. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Létrehoz egy lemezkonfigurációt lemezlétrehozáshoz. Tartalmazza a tárolás típusát, a helyet, a szülő VHD-fájlt tároló tárfiók erőforrás-azonosítóját, valamint a szülő VHD URI-ját. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Létrehoz egy lemezt a paraméterként megadott lemezkonfiguráció, lemeznév és erőforráscsoport-név alapján. |

## <a name="next-steps"></a>További lépések

[Felügyelt lemez létrehozása pillanatképből](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
