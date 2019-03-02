---
title: Azure PowerShell-példaszkript – Felügyelt lemez mögöttes VHD-fájljának exportálása/másolása egy másik régióban lévő tárfiókba | Microsoft Docs
description: Azure PowerShell-példaszkript – Felügyelt lemez mögöttes VHD-fájljának exportálása/másolása egy ugyanabban vagy egy másik régióban lévő tárfiókba
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
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: dc1549007feed11919e25ac9dd60e88ef7f401a5
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248725"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell"></a>Felügyelt lemez mögöttes VHD-fájljának exportálása/másolása egy másik régióban lévő tárfiókba a PowerShell használatával

Ez a szkript egy felügyelt lemez mögöttes VHD-fájlját exportálja egy másik régióban lévő tárfiókba. Először létrehozza a felügyelt lemez SAS URI-azonosítóját, majd annak használatával átmásolja a mögöttes VHD-fájlt egy másik régióban lévő tárfiókba. A szkript használatával másolatot készíthet felügyelt lemezeiről egy másik régióba a regionális terjeszkedéshez.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy felügyelt lemez SAS URI-azonosítójának létrehozásához, majd a mögöttes VHD-fájl a SAS URI használatával való tárfiókba másolásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Grant-AzDiskAccess](https://docs.microsoft.com/powershell/module/az.compute/grant-azdiskaccess) | Létrehozza egy felügyelt lemez SAS URI azonosítóját, amelynek használatával a mögöttes VHD átmásolható egy tárfiókba. |
| [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/New-AzureStorageContext) | Létrehozza egy tárfiókkörnyezetet a fióknév és -kulcs használatával. Ennek a környezetnek a használatával olvasási/írási műveletek hajthatók végre a tárfiókon. |
| [Start-AzureStorageBlobCopy](https://docs.microsoft.com/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Másolatot készít egy pillanatkép mögöttes VHD-fájljáról egy tárfiókba |

## <a name="next-steps"></a>További lépések

[Felügyelt lemez létrehozása virtuális merevlemezből](virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Virtuális gép létrehozása felügyelt lemezből](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.