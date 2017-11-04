---
title: "Az Azure CLI-parancsfájlt minták – hozzon létre egy virtuális Gépet egy pillanatképből |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minták – hozzon létre egy virtuális Gépet egy pillanatképből"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: be282f79445c505ece7c6115df7a29c20a6a5f02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Virtuális gép létrehozása egy pillanatképből, a parancssori felület

Ez a parancsfájl létrehoz egy virtuális gépet egy operációsrendszer-lemez egy pillanatképből.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a felügyelt lemezes virtuális gép és minden kapcsolódó erőforrás létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az pillanatkép megjelenítése](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Lekérdezi pillanatkép használatával pillanatfelvétel neve és az erőforráscsoport neve. A visszaadott objektumot tulajdonsága felügyelt lemezes létrehozására szolgál.  |
| [az lemez létrehozása](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Felügyelt lemezek létrehoz egy pillanatképből használatával pillanatkép-azonosító, a lemez neve, a tárolási típus és a mérete  |
| [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | A virtuális gépek a felügyelt operációsrendszer-lemez létrehozása |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
