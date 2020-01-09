---
title: Felügyelt lemez pillanatképének másolása előfizetés-CLI-mintára
description: Azure CLI-példaszkript – Felügyelt lemez pillanatképének másolása (áthelyezése) előfizetésen belül vagy előfizetések között a CLI használatával
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ddcf585af86a265410f9c2d424c85acf5abbc8fd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75375916"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Felügyelt lemez pillanatképének másolása előfizetésen belül vagy előfizetések között a CLI használatával

Ez a szkript átmásolja egy felügyelt lemez pillanatképét az előfizetésen belül vagy előfizetések között. Használja ezt a parancsfájlt a következő esetekben:

1. A prémium szintű Storage-ban (Premium_LRS) lévő pillanatkép áttelepíthető a standard Storage-ba (Standard_LRS vagy Standard_ZRS) a díjak csökkentése érdekében.
1. A ZRS-tároló nagyobb megbízhatóságának kihasználása érdekében áttelepíthet egy pillanatképet a helyileg redundáns tárterületről (Premium_LRS, Standard_LRS) a zónák redundáns tárolására (Standard_ZRS).
1. Áthelyezheti a pillanatképet más előfizetésbe ugyanabban a régióban a hosszú megőrzés érdekében.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy, a forráspillanatkép azonosítóját használó pillanatkép a célelőfizetésben történő létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Lekérdezi egy pillanatkép összes tulajdonságát a pillanatkép neve és erőforráscsoport-tulajdonságai alapján. Az „Id” tulajdonság a pillanatkép másik előfizetésbe való másolásakor használatos.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Lemásol egy pillanatképet úgy, hogy a szülőpillanatkép azonosítójának és nevének használatával egy másik előfizetésben hoz létre egy pillanatképet.  |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További virtuális gépek és felügyelt lemezek a CLI-parancsfájlok az [Azure Windows VM dokumentációjában](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)találhatók.
