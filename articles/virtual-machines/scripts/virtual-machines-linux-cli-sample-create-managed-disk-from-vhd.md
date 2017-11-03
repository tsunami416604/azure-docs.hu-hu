---
title: "Az Azure CLI-parancsfájlt minta - hozhat létre egy felügyelt lemezt a tárfiók ugyanahhoz az előfizetéshez a VHD-fájl |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minta - hozhat létre egy felügyelt lemezt a tárfiók ugyanahhoz az előfizetéshez a VHD-fájl"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ede9457f5843d0a8a04503779970a553c5ed4f96
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Hozhat létre egy felügyelt lemezt a tárfiók ugyanahhoz az előfizetéshez a parancssori felület a VHD-fájl

Ez a parancsfájl egy felügyelt lemezt a tárfiók ugyanahhoz az előfizetéshez a VHD-fájl hoz létre. Ezen parancsfájl segítségével importálja a speciális (nem általánosított/Sysprep használatával előkészített) felügyelt operációsrendszer-lemez VHD-egy virtuális gép létrehozásához. Másik lehetőségként az adatimportálás egy virtuális merevlemez felügyelt adatok lemezre. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsok hozhat létre egy felügyelt lemezt egy virtuális Merevlemezt. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az lemez létrehozása](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | A virtuális merevlemez URI használatát a tárfiók ugyanahhoz az előfizetéshez felügyelt lemezt hozott létre |

## <a name="next-steps"></a>Következő lépések

[Virtuális gép létrehozása az operációs rendszer lemezeként felügyelt lemezes csatolásával](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép és a felügyelt lemezek CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
