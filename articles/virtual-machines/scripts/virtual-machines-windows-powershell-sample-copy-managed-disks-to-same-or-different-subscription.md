---
title: Az Azure PowerShell-parancsfájl minta - példány (áthelyezése) által kezelt lemezeken ugyanazon vagy másik előfizetésbe |} Microsoft Docs
description: Az Azure PowerShell-parancsfájl minta - példány (áthelyezése) által kezelt lemezeken ugyanazon vagy másik előfizetésbe
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: a14b25236fc233ef7b98b29e62a1270c5e4d8f53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23879630"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>Felügyelt lemezeket másolja a ugyanahhoz az előfizetéshez vagy másik előfizetést, a PowerShell használatával

Ez a parancsfájl egy létező felügyelt lemezes másolatot készít a ugyanahhoz az előfizetéshez vagy másik előfizetést. Az új lemez ugyanabban a régióban, mint a szülő kezelt lemez jön létre.   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy új kezelt lemez létrehozása a célként megadott előfizetés azonosítóját felügyelt lemezt használja. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | A lemez létrehozásához használt lemezkonfiguráció hoz létre. Ez magában foglalja az erőforrás-azonosítót a szülőlemezt, és a helyre, amely ugyanaz, mint a szülő lemez helyét.  |
| [Új AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Lemezkonfiguráció, a lemez neve és a paraméterként erőforráscsoport-név használatával lemezt hozott létre. |


## <a name="next-steps"></a>Következő lépések

[A felügyelt lemezes virtuális gép létrehozása](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).