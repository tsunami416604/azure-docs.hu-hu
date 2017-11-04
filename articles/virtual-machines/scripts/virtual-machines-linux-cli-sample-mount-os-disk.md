---
title: "Az Azure CLI parancsfájl minta - csatlakoztatási operációsrendszer-lemez |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - csatlakoztatási operációsrendszer-lemez"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c32ea5e6cade34a9c8dac0eab523ebcaa10ef039
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>A virtuális gépek operációs rendszer lemezhiba elhárítása

Ezt a parancsfájlt az operációsrendszer-lemez hibás vagy problematikus virtuális gép, egy második virtuális gép adatlemezt csatlakoztatja. Ez akkor lehet hasznos, ha a hibaelhárítási lemezes problémákat vagy az adatok helyreállítása. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, virtuális gép és minden kapcsolódó erőforrás létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az vm megjelenítése](https://docs.microsoft.com/cli/azure/vm#az_vm_show) | A virtuális gépek listáját adja vissza. Ebben az esetben a lekérdezési lehetőség szolgál a virtuális gép operációsrendszer-lemez adja vissza. Ezt az értéket egy változó neve "uri" majd kerül. |
| [az a virtuális gép törlése](https://docs.microsoft.com/cli/azure/vm#az_vm_delete) | Törli a virtuális gépet. |
| [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Létrehoz egy virtuális gépet.  |
| [az méretű lemez csatolása](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) | Lemez csatolása a virtuális gép. |
| [az vm-ip-címeinek listáját](https://docs.microsoft.com/cli/azure/vm#az_vm_list_ip_addresses) | A virtuális gépek IP-címét adja vissza. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
