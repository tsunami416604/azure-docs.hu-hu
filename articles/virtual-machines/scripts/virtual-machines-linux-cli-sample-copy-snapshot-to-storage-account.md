---
title: "Az Azure CLI parancsfájl minta - exportálási vagy másolási pillanatképének, virtuális Merevlemezt egy másik régióban található tárfiókot |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - exportálási vagy másolási pillanatkép virtuális merevlemez, egy tárfiókkal azonos vagy azoktól eltérő előfizetés"
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
ms.openlocfilehash: cd0128256e016a329b0940eec4be41426cdaf51a
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-cli"></a>Exportálás/másolás felügyelt pillanatfelvételek virtuális merevlemez egy másik régióban található a CLI tárfiókkal

Ez a parancsfájl egy másik régióban található tárfiókot felügyelt pillanatkép exportálja. Először hoz létre a SAS URI-jának a pillanatképet, és azt használja egy másik régióban található tárfiókot másolásához. Ez a parancsfájl segítségével biztonsági mentés a felügyelt lemezek vész-helyreállítási másik régióban. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

Ez a parancsfájl SAS URI-t a felügyelt pillanatkép létrehozásához a következő parancsok használja, és másolja a pillanatkép SAS URI-t használó tárfiókot. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az pillanatkép-hozzáférés engedélyezése](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_grant_access) | Csak olvasható, alapul szolgáló VHD-fájlt másolja a storage-fiók, vagy töltse le a helyszínen használt SAS generál  |
| [az tárolási blob másolási indítása](https://docs.microsoft.com/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | Másolja át a blob aszinkron módon egy tárfiók egy másikra |

## <a name="next-steps"></a>Következő lépések

[Felügyelt lemezes virtuális merevlemez létrehozása](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[A felügyelt lemezes virtuális gép létrehozása](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép és a felügyelt lemezek CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
