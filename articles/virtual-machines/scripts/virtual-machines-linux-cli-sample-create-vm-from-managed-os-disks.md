---
title: "Az Azure CLI-parancsfájlt minta - virtuális gép létrehozása az operációs rendszer lemezeként felügyelt lemezes csatolásával |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minta - virtuális gép létrehozása az operációs rendszer lemezeként felügyelt lemezes csatolásával"
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
ms.openlocfilehash: 2141ea4fd25dfc69ada02c54c4f6b6b717b8e7db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Hozzon létre egy virtuális gép egy meglévő felügyelt operációsrendszer-lemez a parancssori felület használatával

Ez a parancsfájl által az operációs rendszer lemezeként felügyelt meglévő lemez csatolása létrehoz egy virtuális gépet. Használja ezt a parancsfájlt előző forgatókönyvek:
* Egy meglévő felügyelt operációsrendszer-lemez, amely egy másik előfizetésben található felügyelt lemezes másolta a virtuális gép létrehozása
* Lemezről egy meglévő felügyelt speciális VHD-fájl alapján létrehozott virtuális gép létrehozása 
* Egy meglévő felügyelt operációsrendszer-lemez, amely egy pillanatképből hozták létre a virtuális gép létrehozása 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a felügyelt lemezes tulajdonságait, egy felügyelt lemezt csatolni a új virtuális gép, és hozzon létre egy virtuális Gépet. A parancs adott dokumentáció tábla mutató összes elemére.

| Parancs | Megjegyzések |
|---|---|
| [az lemez megjelenítése](https://docs.microsoft.com/cli/azure/disk#az_disk_show) | Lekérdezi a felügyelt lemezes tulajdonságok lemez neve és az erőforráscsoport-név használatával. ID tulajdonságának használatával felügyelt lemezes csatolható egy új virtuális géphez |
| [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | A virtuális gépek a felügyelt operációsrendszer-lemez létrehozása |
## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
