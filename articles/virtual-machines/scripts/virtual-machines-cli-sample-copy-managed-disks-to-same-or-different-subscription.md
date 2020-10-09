---
title: Felügyelt lemezek másolása az előfizetés-CLI-mintára
description: Azure CLI-parancsfájl minta – felügyelt lemezek másolása (vagy áthelyezése) egy másik előfizetésbe
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ed593b37de556d5b62cfec6b726322d12f4d8104
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89052256"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Felügyelt lemezek másolása előfizetésen belül vagy előfizetések között a CLI használatával

Ez a szkript átmásol egy felügyelt lemezt egy előfizetésen belül vagy egy eltérő, de ugyanabban a régióban található előfizetésbe. A másolás csak akkor működik, ha az előfizetések ugyanahhoz az Azure AD-bérlőhöz tartoznak.


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

[Virtuális gép létrehozása felügyelt lemezből](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

A virtuális gépekhez és felügyelt lemezekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
