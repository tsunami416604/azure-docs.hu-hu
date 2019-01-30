---
title: Azure CLI-példaszkript – Virtuális gép létrehozása pillanatképből | Microsoft Docs
description: Azure CLI-példaszkript – Virtuális gép létrehozása pillanatképből
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: db0aa1781c3e35b68a59082cf7a1760f7e9a34b4
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239570"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Virtuális gép létrehozása pillanatképből a CLI használatával

Ez a szkript létrehoz egy virtuális gépet egy operációsrendszer-lemez pillanatképéből.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy felügyelt lemez, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Lekérdez egy pillanatképet a pillanatkép és az erőforráscsoport neve alapján. A rendszer a visszaadott objektum „Id” tulajdonságát használja fel egy felügyelt lemez létrehozásához.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Felügyelt lemezeket hoz létre egy pillanatképből a pillanatkép-azonosító, a lemeznév, a tárhelytípus és a méret alapján.  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Létrehoz egy virtuális gépet egy felügyelt operációsrendszer-lemezből. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
