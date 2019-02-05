---
title: Azure CLI-példaszkript – Linux rendszerű virtuális gép gyors létrehozása | Microsoft Docs
description: Azure CLI-példaszkript – Linux rendszerű virtuális gép gyors létrehozása
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
ms.openlocfilehash: ce424fcd7521893caf0dd0f7924c3021fb0f2599
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695560"
---
# <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Ez a szkript egy Azure-beli virtuális gépet hoz létre Ubuntu operációs rendszerrel és kapcsolódó hálózati erőforrásokkal. A szkript futtatása után a virtuális gépet SSH-kapcsolaton keresztül érheti el.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-quick/create-vm-quick.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, virtuális hálózathoz, alhálózathoz és hálózati biztonsági csoporthoz. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A virtuális gépekhez kapcsolódó további CLI-példaszkripteket az [Azure Linux rendszerű virtuális gépekre vonatkozó dokumentációjában](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) találhat.
