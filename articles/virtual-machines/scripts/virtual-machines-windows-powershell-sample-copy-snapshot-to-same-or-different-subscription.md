---
title: Az Azure PowerShell-parancsfájl minta - ugyanazon vagy másik előfizetésbe felügyelt lemezes pillanatképet másolása (áthelyezése) |} Microsoft Docs
description: Az Azure PowerShell-parancsfájl minta - ugyanazon vagy másik előfizetésbe felügyelt lemezes pillanatképet másolása (áthelyezése)
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
ms.openlocfilehash: 064b5355da10fe683563fa078cfafc65080f7ea2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23879658"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Másolja át a felügyelt lemezes pillanatképet ugyanahhoz az előfizetéshez vagy másik előfizetést, a PowerShell használatával

Ez a parancsfájl pillanatkép másolatot készít a azonos ugyanahhoz az előfizetéshez vagy másik előfizetést. Ezen parancsfájl segítségével pillanatkép áthelyezése az adatok megőrzésének másik előfizetést. Tárolni pillanatképek másik előfizetésben található a pillanatképek a fő előfizetésben véletlen törlés elleni védelme. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a célként megadott előfizetés, a forrás pillanatkép azonosítóját használja a pillanatkép létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új AzureRmSnapshotConfig](/powershell/module/azurerm.compute/New-AzureRmSnapshotConfig) | A pillanatkép létrehozásához használt pillanatkép-konfiguráció létrehozása Ez magában foglalja az erőforrás-azonosítót a szülő pillanatkép és a helyre, amely ugyanaz, mint a szülő pillanatkép.  |
| [Új AzureRmSnapshot](/powershell/module/azurerm.compute/New-AzureRmDisk) | Pillanatkép készítése a pillanatkép-beállítása, pillanatfelvétel neve és paraméterként erőforráscsoport-név használatával. |


## <a name="next-steps"></a>Következő lépések

[Hozzon létre egy virtuális gépet egy pillanatképből](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

További virtuális gép PowerShell-parancsfájl példák találhatók a [Azure Windows virtuális dokumentációját](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).