---
title: Az Azure PowerShell-Példaszkript – felügyelt azonos vagy eltérő előfizetéshez lemezek másolása (áthelyezése) |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – felügyelt azonos vagy eltérő előfizetéshez lemezek másolása (áthelyezése)
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
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: c063fc431578cafebac6fb412f15023ffcf70e17
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248727"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>Felügyelt lemezek másolása a ugyanahhoz az előfizetéshez vagy a PowerShell-lel másik előfizetésre

Ez a szkript létrehoz egy olyan meglévő felügyelt lemezről példányát ugyanahhoz az előfizetéshez vagy másik előfizetést. Az új lemez jön létre a szülő felügyelt lemez ugyanabban a régióban.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy új felügyelt lemez létrehozásához a célul szolgáló előfizetésben a forrásul szolgáló felügyelt lemez azonosítója alapján. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Létrehoz egy lemezkonfigurációt lemezlétrehozáshoz. Az erőforrás-azonosítóját a szülőlemezt és a helyet, hogy ugyanaz, mint a szülőlemezt helyét tartalmazza.  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Létrehoz egy lemezt a paraméterként megadott lemezkonfiguráció, lemeznév és erőforráscsoport-név alapján. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.