---
title: Azure CLI-példaszkript – Linux rendszerű virtuális gép létrehozása WordPress használatával | Microsoft Docs
description: Azure CLI-példaszkript – Linux rendszerű virtuális gép létrehozása WordPress használatával
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1c543c2357dd31bb06a19c7d53284c65a7ca7299
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928124"
---
# <a name="create-a-vm-with-wordpress"></a>Virtuális gép létrehozása WordPress használatával

Ez a szkript létrehoz egy virtuális gépet, és az Azure Virtual Machine egyéni szkriptbővítménnyel telepíti a WordPresst. A szkript futtatása után a WordPress konfigurációs webhelyét itt érheti el: `http://<public IP of VM>/wordpress`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-wordpress-mysql/create-wordpress-mysql.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#az_vm_open_port) | Létrehoz egy hálózati biztonsági csoportra vonatkozó szabályt a befelé irányuló forgalom engedélyezésére. Ebben a példában a 80-as portot nyitjuk meg a HTTP-forgalom számára. |
| [az vm extension set](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Hozzáadja az egyéni szkriptbővítményt a virtuális géphez, amely meghív egy szkriptet a WordPress telepítésére. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
