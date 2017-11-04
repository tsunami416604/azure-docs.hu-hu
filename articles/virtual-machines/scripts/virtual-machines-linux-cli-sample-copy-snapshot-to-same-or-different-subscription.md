---
title: "Az Azure CLI parancsfájl minta - azonos vagy különböző-előfizetéshez CLI felügyelt lemezes pillanatképet másolása (áthelyezése) |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - azonos vagy különböző-előfizetéshez CLI felügyelt lemezes pillanatképet másolása (áthelyezése)"
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
ms.openlocfilehash: 7c301a314ee946bb9199650bb7f674b8dce7c141
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Másolja a felügyelt lemezes pillanatképet CLI ugyanazon vagy másik előfizetés

Ez a parancsfájl egy felügyelt lemezes pillanatképet ugyanazon vagy másik előfizetés másolja. Ezen parancsfájl segítségével pillanatkép áthelyezése másik előfizetésben található a szülőhely pillanatképet ugyanabban a régióban.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a célként megadott előfizetés, a forrás pillanatkép azonosítóját használja a pillanatkép létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az pillanatkép megjelenítése](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Lekérdezi a az alábbi néven pillanatkép tulajdonságainak és a pillanatkép erőforráscsoport tulajdonságai. Azonosító tulajdonságot használja a pillanatkép másolása másik előfizetést.  |
| [az pillanatkép létrehozása](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_create) | Másolja át a pillanatképet hoz létre pillanatképet különböző előfizetési azonosító és a szülő pillanatkép neve.  |

## <a name="next-steps"></a>Következő lépések

[Hozzon létre egy virtuális gépet egy pillanatképből](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép és a felügyelt lemezek CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
