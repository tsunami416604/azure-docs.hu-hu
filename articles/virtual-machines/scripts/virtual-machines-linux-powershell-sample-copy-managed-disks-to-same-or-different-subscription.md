---
title: Felügyelt lemezek másolása az előfizetésbe (Linux) – PowerShell-minta
description: Azure PowerShell parancsfájl mintája – a felügyelt lemezek másolása (vagy áthelyezése) ugyanabba vagy egy másik előfizetésbe
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
ms.openlocfilehash: ca594801c0886903f395dddb7ccbfccb42bff15f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069384"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell-linux"></a>Felügyelt lemezek másolása ugyanabban az előfizetésben vagy eltérő előfizetésben a PowerShell-lel (Linux)

Ez a szkript egy meglévő felügyelt lemez másolatát hozza létre ugyanabban az előfizetésben vagy más előfizetésben. Az új lemez ugyanabban a régióban jön létre, mint a szülő felügyelt lemez.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy új felügyelt lemez létrehozásához a célul szolgáló előfizetésben a forrásul szolgáló felügyelt lemez azonosítója alapján. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Új – AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Létrehoz egy lemezkonfigurációt lemezlétrehozáshoz. Tartalmazza a szülő lemez erőforrás-azonosítóját, valamint a szülő lemez helyét megegyező helyet.  |
| [Új – AzDisk](/powershell/module/az.compute/new-azdisk) | Létrehoz egy lemezt a paraméterként megadott lemezkonfiguráció, lemeznév és erőforráscsoport-név alapján. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
