---
title: Felügyelt lemez a VHD-fájlból ugyanabba a fiókba (Windows) – CLI-minta
description: Azure CLI-példaszkript – Felügyelt lemez létrehozása VHD-fájlból az előfizetés egy másik tárfiókjában
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
ms.openlocfilehash: 34f05318840bcb4356de0aab8dd12f62ac79d345
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069256"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli-windows"></a>Felügyelt lemez létrehozása VHD-fájlból ugyanabban az előfizetésben a parancssori felület (Windows) használatával

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

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További virtuális gépek és felügyelt lemezek a CLI-parancsfájlok az [Azure Windows VM dokumentációjában](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)találhatók.
