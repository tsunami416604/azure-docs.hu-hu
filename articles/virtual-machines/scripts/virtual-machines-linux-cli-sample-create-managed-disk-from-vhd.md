---
title: Azure CLI-példaszkript – Felügyelt lemez létrehozása VHD-fájlból az előfizetés egy másik tárfiókjában | Microsoft Docs
description: Azure CLI-példaszkript – Felügyelt lemez létrehozása VHD-fájlból az előfizetés egy másik tárfiókjában
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 93a4a47b539184910f01fe9261562ffd8816ad45
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581232"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Azure CLI-példaszkript – Felügyelt lemez létrehozása VHD-fájlból a parancssori felülettel az előfizetés egy másik tárfiókjában

Ez a szkript egy felügyelt lemezt hoz létre egy VHD-fájlból az előfizetés egy másik tárfiókjában. A szkript használatával egy specializált (nem általános/Sysprep használatával létrehozott) virtuális merevlemezt importálhat egy felügyelt rendszerlemezre egy virtuális gép létrehozása céljából. Másik lehetőségként egy virtuális adatlemezt importálhat egy felügyelt adatlemezre. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy felügyelt lemez egy VHD-ból történő létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Az előfizetés egy másik tárfiókjában létrehoz egy felügyelt lemezt egy VHD URI-azonosítója alapján. |

## <a name="next-steps"></a>További lépések

[Virtuális gép létrehozása felügyelt lemez rendszerlemezként való csatlakoztatásával](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez és felügyelt lemezekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
