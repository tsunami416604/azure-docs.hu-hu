---
title: Felügyelt lemezek másolása előfizetésre – CLI-minta
description: Azure CLI-példaszkript – Felügyelt lemezek másolása (mozgatása) előfizetésen belül vagy előfizetések között
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
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
ms.openlocfilehash: 4b1516e492b13426bed6cae8637abd5c713bcd63
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010121"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Felügyelt lemezek másolása előfizetésen belül vagy előfizetések között a CLI használatával

Ez a szkript átmásol egy felügyelt lemezt egy előfizetésen belül vagy egy eltérő, de ugyanabban a régióban található előfizetésbe. A másolás csak akkor működik, ha az előfizetések ugyanahhoz a HRE-bérlőhöz tartoznak.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy új felügyelt lemez létrehozásához a célul szolgáló előfizetésben a forrásul szolgáló felügyelt lemez azonosítója alapján. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az disk show](/cli/azure/disk) | Lekérdezi egy felügyelt lemez összes tulajdonságát a lemez neve és erőforráscsoport-tulajdonságai alapján. Az „Id” tulajdonság a felügyelt lemez másik előfizetésbe való másolásakor használatos.  |
| [az disk create](/cli/azure/disk) | Lemásol egy felügyelt lemezt úgy, hogy a szülő felügyelt lemez azonosítójának és nevének használatával egy másik előfizetésben hoz létre egy felügyelt lemezt.  |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További virtuális gépek és felügyelt lemezek a CLI-parancsfájlok az [Azure Windows VM dokumentációjában](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)találhatók.
