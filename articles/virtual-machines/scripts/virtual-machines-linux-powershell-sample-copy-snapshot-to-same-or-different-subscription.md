---
title: Felügyelt lemez előfizetése (Linux) – PowerShell
description: Azure PowerShell parancsfájl mintája – a felügyelt lemez pillanatképének másolása (vagy áthelyezése) azonos vagy eltérő előfizetésre
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
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 54fbdc86ecd035593960eaa57187fbf9e35393fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069321"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-linux"></a>Felügyelt lemez pillanatképének másolása azonos előfizetésben vagy eltérő előfizetésben a PowerShell-lel (Linux)

Ez a szkript átmásolja egy felügyelt lemez pillanatképét az előfizetésen belül vagy előfizetések között. Használja ezt a parancsfájlt a következő esetekben:

1. A prémium szintű Storage-ban (Premium_LRS) lévő pillanatkép áttelepíthető a standard Storage-ba (Standard_LRS vagy Standard_ZRS) a díjak csökkentése érdekében.
1. A ZRS-tároló nagyobb megbízhatóságának kihasználása érdekében áttelepíthet egy pillanatképet a helyileg redundáns tárterületről (Premium_LRS, Standard_LRS) a zónák redundáns tárolására (Standard_ZRS).
1. Áthelyezheti a pillanatképet más előfizetésbe ugyanabban a régióban a hosszú megőrzés érdekében.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy, a forráspillanatkép azonosítóját használó pillanatkép a célelőfizetésben történő létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Új – AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) | Egy, a pillanatképek létrehozásához használt pillanatkép-konfigurációt hoz létre. Tartalmazza a szülőpillanatkép erőforrás-azonosítóját, valamint a helyet, amely azonos a szülőpillanatkép helyével.  |
| [Új – AzSnapshot](/powershell/module/az.compute/new-azdisk) | Létrehoz egy pillanatképet a pillanatkép-konfiguráció, a pillanatkép neve és az erőforráscsoport neve alapján, amelyek paraméterként vannak megadva. |

## <a name="next-steps"></a>További lépések

[Virtuális gép létrehozása pillanatképből](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
