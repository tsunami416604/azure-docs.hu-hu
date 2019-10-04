---
title: Azure PowerShell parancsfájl minta – Pillanatkép exportálása/másolása virtuális merevlemezként egy másik régióban lévő Storage-fiókba | Microsoft Docs
description: Azure PowerShell parancsfájl minta – Pillanatkép exportálása/másolása virtuális merevlemezként ugyanazon a különböző régióban lévő Storage-fiókba
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 6f7350958ca1c17c5e1a730fa2e401f39ac8ffcb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090819"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Felügyelt Pillanatképek exportálása/másolása virtuális merevlemezként egy másik régióban lévő Storage-fiókba a PowerShell használatával

Ez a szkript egy felügyelt pillanatképet exportál egy másik régió tárfiókjába. Először létrehozza a pillanatkép SAS URI-azonosítóját, majd annak használatával átmásolja a pillanatképet egy másik régióban lévő tárfiókba. A szkript használatával vészhelyreállítási célokból eltérő régiókban készíthet biztonsági másolatokat a felügyelt lemezeiről.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy felügyelt pillanatkép SAS URI-azonosítójának létrehozásához, majd a pillanatkép a SAS URI használatával egy tárfiókba való másolásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Grant-AzSnapshotAccess](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | SAS URI-t hoz létre egy olyan pillanatképhez, amely egy Storage-fiókba való másoláshoz használatos. |
| [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/New-AzureStorageContext) | Létrehozza egy tárfiókkörnyezetet a fióknév és -kulcs használatával. Ennek a környezetnek a használatával olvasási/írási műveletek hajthatók végre a tárfiókon. |
| [Start-AzureStorageBlobCopy](https://docs.microsoft.com/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Másolatot készít egy pillanatkép mögöttes VHD-fájljáról egy tárfiókba |

## <a name="next-steps"></a>További lépések

[Felügyelt lemez létrehozása virtuális merevlemezből](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Virtuális gép létrehozása felügyelt lemezből](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.