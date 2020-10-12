---
title: Felügyelt lemez létrehozása VHD-fájlból ugyanabban a fiókban – CLI-minta
description: Azure CLI-példaszkript – Felügyelt lemez létrehozása VHD-fájlból az előfizetés egy másik tárfiókjában
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: d0795f67390faec4a08882adb1f2b15efcbc6619
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274877"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli-linux"></a>Felügyelt lemez létrehozása VHD-fájlból ugyanabban az előfizetésben a parancssori felület (Linux) használatával

Ez a szkript egy felügyelt lemezt hoz létre egy VHD-fájlból az előfizetés egy másik tárfiókjában. A szkript használatával egy specializált (nem általános/Sysprep használatával létrehozott) virtuális merevlemezt importálhat egy felügyelt rendszerlemezre egy virtuális gép létrehozása céljából. Másik lehetőségként egy virtuális adatlemezt importálhat egy felügyelt adatlemezre. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy felügyelt lemez egy VHD-ból történő létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az disk create](/cli/azure/disk) | Az előfizetés egy másik tárfiókjában létrehoz egy felügyelt lemezt egy VHD URI-azonosítója alapján. |

## <a name="next-steps"></a>További lépések

[Virtuális gép létrehozása felügyelt lemez rendszerlemezként való csatlakoztatásával](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

A virtuális gépekhez és felügyelt lemezekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
