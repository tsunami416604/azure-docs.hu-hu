---
title: Felügyelt lemez virtuális merevlemezének exportálása/másolása egy különböző régióban lévő tárfiókba – PowerShell-minta
description: Azure PowerShell-példaszkript – Felügyelt lemez mögöttes VHD-fájljának exportálása/másolása egy ugyanabban vagy egy másik régióban lévő tárfiókba
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
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: 9d088c443490d22a68570daf4ce035639861b90d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75368861"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell"></a>Felügyelt lemez mögöttes VHD-fájljának exportálása/másolása egy másik régióban lévő tárfiókba a PowerShell használatával

Ez a szkript egy felügyelt lemez mögöttes VHD-fájlját exportálja egy másik régióban lévő tárfiókba. Először létrehozza a felügyelt lemez SAS URI-azonosítóját, majd annak használatával átmásolja a mögöttes VHD-fájlt egy másik régióban lévő tárfiókba. A szkript használatával másolatot készíthet felügyelt lemezeiről egy másik régióba a regionális terjeszkedéshez.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy felügyelt lemez SAS URI-azonosítójának létrehozásához, majd a mögöttes VHD-fájl a SAS URI használatával való tárfiókba másolásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Grant-AzdiskAccess](https://docs.microsoft.com/powershell/module/az.compute/grant-azdiskaccess) | Létrehozza egy felügyelt lemez SAS URI azonosítóját, amelynek használatával a mögöttes VHD átmásolható egy tárfiókba. |
| [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/New-AzureStorageContext) | Létrehozza egy tárfiókkörnyezetet a fióknév és -kulcs használatával. Ennek a környezetnek a használatával olvasási/írási műveletek hajthatók végre a tárfiókon. |
| [Start-AzureStorageBlobCopy](https://docs.microsoft.com/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Másolatot készít egy pillanatkép mögöttes VHD-fájljáról egy tárfiókba |

## <a name="next-steps"></a>További lépések

[Felügyelt lemez létrehozása VHD-ből](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Virtuális gép létrehozása felügyelt lemezből](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Windows rendszerű virtuális gépekre vonatkozó dokumentációjában](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.