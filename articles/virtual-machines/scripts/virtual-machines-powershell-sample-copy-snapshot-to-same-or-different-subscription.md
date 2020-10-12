---
title: Felügyelt lemez pillanatképének másolása az előfizetésbe (Windows) – PowerShell
description: Azure PowerShell-példaszkript – Felügyelt lemez pillanatképének másolása (áthelyezése) előfizetésen belül vagy előfizetések között
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: a2fd6f8b5f2ba0771449178a2d8d3958329e83eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89651600"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-windows"></a>Felügyelt lemez pillanatképének másolása azonos előfizetésben vagy eltérő előfizetésben a PowerShell-lel (Windows)

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
| [Új – AzSnapshot](/powershell/module/az.compute/new-azsnapshot) | Létrehoz egy pillanatképet a pillanatkép-konfiguráció, a pillanatkép neve és az erőforráscsoport neve alapján, amelyek paraméterként vannak megadva. |

## <a name="next-steps"></a>További lépések

[Virtuális gép létrehozása pillanatképből](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
