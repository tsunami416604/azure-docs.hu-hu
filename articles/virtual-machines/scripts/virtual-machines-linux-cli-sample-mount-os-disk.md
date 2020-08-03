---
title: Azure CLI-példaszkript – Operációsrendszer-lemez csatolása
description: Azure CLI-példaszkript – Operációsrendszer-lemez csatolása
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 735b16ad054edfea7cf0ad2e1e5b9c76b664e6c3
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479630"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Egy virtuális gép operációsrendszer-lemezének hibaelhárítása

Ez a szkript egy hibás vagy problémás virtuális gép operációsrendszer-lemezét csatolja hozzá adatlemezként egy második virtuális géphez. A használata lemezproblémák hibaelhárításához és adatok helyreállításához javallott.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az vm show](/cli/azure/vm) | Visszaadja a virtuális gépek listáját. Jelen esetben a rendszer a lekérdezési beállítással adja vissza a virtuális gép operációsrendszer-lemezét. Ezt az értéket ezután hozzáadja egy „uri” nevű változóhoz. |
| [az vm delete](/cli/azure/vm) | Töröl egy virtuális gépet. |
| [az vm create](/cli/azure/vm) | Létrehoz egy virtuális gépet.  |
| [az vm disk attach](/cli/azure/vm/disk) | Csatlakoztat egy lemezt egy virtuális géphez. |
| [az vm list-ip-addresses](/cli/azure/vm) | Visszaadja egy virtuális gép IP-címeit. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
