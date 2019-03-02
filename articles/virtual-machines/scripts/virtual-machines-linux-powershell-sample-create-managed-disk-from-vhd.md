---
title: Azure PowerShell-példaszkript – Felügyelt lemez létrehozása VHD-fájlból azonos vagy eltérő előfizetéshez tartozó tárfiókban | Microsoft Docs
description: Azure PowerShell-példaszkript – Felügyelt lemez létrehozása VHD-fájlból azonos vagy eltérő előfizetéshez tartozó tárfiókban
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
ms.openlocfilehash: fd62b3e7008bcae4e5d6d91e165f4a382167b1e6
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248729"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Felügyelt lemez létrehozása VHD-fájlból azonos vagy eltérő előfizetéshez tartozó tárfiókban a PowerShell segítségével

Ez a szkript létrehoz egy felügyelt lemezt egy VHD-fájlból egy azonos vagy eltérő előfizetéshez tartozó tárfiókban. A szkript használatával egy specializált (nem általános/Sysprep használatával létrehozott) virtuális merevlemezt importálhat egy felügyelt rendszerlemezre egy virtuális gép létrehozása céljából. Emellett a szkripttel virtuális adatlemezt is importálhat egy felügyelt lemezre.

Ne hozzon létre több azonos felügyelt lemezt VHD-fájlból rövid idő alatt. Felügyelt lemezek VHD-fájlból való létrehozásakor blob-pillanatkép jön létre a VHD-fájlból, és ennek használatával hozza létre a rendszer a felügyelt lemezeket. Csak egy blob-pillanatkép hozható létre egy perc alatt, mely lemez-létrehozási hibákhoz vezethet teljesítménykorlátozó leszabályozás miatt. Ennek a leszabályozásnak az elkerüléséhez hozzon létre egy [felügyelt pillanatképet a VHD-fájlból](virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ezután a felügyelt pillanatkép segítségével már hozhat létre rövid idő alatt több felügyelt lemezt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsok használatával hoz létre felügyelt lemezt egy VHD-ból egy másik előfizetésben. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Létrehoz egy lemezkonfigurációt lemezlétrehozáshoz. Tartalmazza a tárolás típusát, a helyet, a szülő VHD-fájlt tároló tárfiók erőforrás-azonosítóját, valamint a szülő VHD URI-ját. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Létrehoz egy lemezt a paraméterként megadott lemezkonfiguráció, lemeznév és erőforráscsoport-név alapján. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
