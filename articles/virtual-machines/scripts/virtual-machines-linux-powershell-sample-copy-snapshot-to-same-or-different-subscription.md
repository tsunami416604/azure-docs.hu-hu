---
title: Azure PowerShell-példaszkript – Felügyelt lemez pillanatképének másolása (áthelyezése) előfizetésen belül vagy előfizetések között | Microsoft Docs
description: Azure PowerShell-példaszkript – Felügyelt lemez pillanatképének másolása (áthelyezése) előfizetésen belül vagy előfizetések között
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
ms.openlocfilehash: aadcdc679d8d0df82c7ddc41b4dcdd6766bb7426
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248708"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Felügyelt lemez pillanatképének másolása előfizetésen belül vagy előfizetések között a PowerShell használatával

Ez a szkript átmásolja egy felügyelt lemez pillanatképét az előfizetésen belül vagy előfizetések között. Ez a szkript a következő esetekben használja:

1. Telepítse át a Premium storage (Premium_LRS) pillanatkép standard szintű tárolóba (Standard_LRS vagy Standard_ZRS) a költségek csökkentése érdekében.
1. Telepítse át egy pillanatképet a helyileg redundáns tárolás (Premium_LRS, Standard_LRS) zónaredundáns tárolás (Standard_ZRS) számára, hogy a nagyobb megbízhatóságot ZRS tárhelyet.
1. Helyezze át a pillanatkép másik előfizetésbe tartozó hosszabb adatmegőrzés megadásához ugyanabban a régióban.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy, a forráspillanatkép azonosítóját használó pillanatkép a célelőfizetésben történő létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Egy, a pillanatképek létrehozásához használt pillanatkép-konfigurációt hoz létre. Tartalmazza a szülőpillanatkép erőforrás-azonosítóját, valamint a helyet, amely azonos a szülőpillanatkép helyével.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Létrehoz egy pillanatképet a pillanatkép-konfiguráció, a pillanatkép neve és az erőforráscsoport neve alapján, amelyek paraméterként vannak megadva. |

## <a name="next-steps"></a>További lépések

[Virtuális gép létrehozása pillanatképből](./virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

A virtuális gépekhez kapcsolódó további PowerShell-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.