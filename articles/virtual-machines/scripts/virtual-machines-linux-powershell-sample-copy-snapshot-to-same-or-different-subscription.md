---
title: Felügyelt lemez pillanatképének másolása előfizetésbe – PowerShell-minta
description: Azure PowerShell-parancsfájlminta – felügyelt lemez pillanatképének másolása (vagy áthelyezése) ugyanarra vagy másik előfizetésre
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 65a3e39206864f10c41e79ba6b3e7a89da99dc6f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75463772"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Felügyelt lemez pillanatképének másolása előfizetésen belül vagy előfizetések között a PowerShell használatával

Ez a szkript átmásolja egy felügyelt lemez pillanatképét az előfizetésen belül vagy előfizetések között. Ezt a parancsfájlt a következő esetekben használhatja:

1. A költségek csökkentése érdekében áttelepítheti a pillanatképet a prémium szintű storage (Premium_LRS) szolgáltatásban a standard szintű tárterületre (Standard_LRS vagy Standard_ZRS).
1. A zrs-tároló nagyobb megbízhatóságának kihasználása érdekében a helyileg redundáns tárolásról (Premium_LRS, Standard_LRS) a zónaredundáns tárolásra (Standard_ZRS) áttelepítheti a pillanatképet.
1. Helyezzen át egy pillanatképet ugyanabban a régióban egy másik előfizetésbe hosszabb megőrzési érdekében.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy, a forráspillanatkép azonosítóját használó pillanatkép a célelőfizetésben történő létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Új-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Egy, a pillanatképek létrehozásához használt pillanatkép-konfigurációt hoz létre. Tartalmazza a szülőpillanatkép erőforrás-azonosítóját, valamint a helyet, amely azonos a szülőpillanatkép helyével.  |
| [Új-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Létrehoz egy pillanatképet a pillanatkép-konfiguráció, a pillanatkép neve és az erőforráscsoport neve alapján, amelyek paraméterként vannak megadva. |

## <a name="next-steps"></a>További lépések

[Virtuális gép létrehozása pillanatképből](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.