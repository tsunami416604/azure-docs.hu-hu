---
title: Felügyelt lemez létrehozása pillanatképből (Linux) – PowerShell-minta
description: Azure PowerShell-példaszkript – Felügyelt lemez létrehozása pillanatképből
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
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 2faa98ec21d260e6a7cbdf9eceba24baf4b13c61
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501135"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell-linux"></a>Felügyelt lemez létrehozása pillanatképből a PowerShell használatával (Linux)

Ez a szkript egy felügyelt lemezt hoz létre egy pillanatképből. A használatával virtuális gépeket állíthat vissza operációsrendszer- és adatlemezek pillanatképeiből. A megfelelő pillanatképekből létrehozhatja operációs rendszerek és adatok felügyelt lemezeit, majd a felügyelt lemezek csatolásával létrehozhat egy új virtuális gépet. Meglévő virtuális gépek adatlemezeit is visszaállíthatja pillanatképekből létrehozott adatlemezek csatolásával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja arra, hogy létrehozzon egy felügyelt lemezt egy pillanatképből. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Beolvassa a pillanatkép tulajdonságait.  |
| [Új – AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Létrehoz egy lemezkonfigurációt lemezlétrehozáshoz. Tartalmazza a szülőpillanatkép erőforrás-azonosítóját, a helyet (mely azonos a szülőpillanatkép helyével) és a tárolás típusát.  |
| [Új – AzDisk](/powershell/module/az.compute/new-azdisk) | Létrehoz egy lemezt a paraméterként megadott lemezkonfiguráció, lemeznév és erőforráscsoport-név alapján. |

## <a name="next-steps"></a>Következő lépések


[Virtuális gép létrehozása felügyelt lemezből](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) találhat.
