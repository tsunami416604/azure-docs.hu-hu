---
title: Azure CLI-példaszkript – Társviszony létesítése két virtuális hálózat között | Microsoft Docs
description: Azure CLI-példaszkript – Társviszony létesítése két virtuális hálózat között.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: feab9f518076938ed20396319ceb1d5badb9eb8f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841384"
---
# <a name="peer-two-virtual-networks-script-sample"></a>Példaszkript társviszony létesítéséhez két virtuális hálózat között

Ez a példaszkript két virtuális hálózatot hoz létre és csatlakoztat ugyanabban a régióban az Azure-hálózaton keresztül. A szkript futtatása után társviszony jön létre a két virtuális hálózat között.

A szkriptet az Azure [Cloud Shellben](https://shell.azure.com/bash) vagy egy helyi Azure CLI-telepítésből futtathatja. Ha a parancssori felületet helyileg használja, akkor ehhez a szkripthez az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja a parancssori felületet, akkor emellett a `az login` futtatásával kapcsolatot kell teremtenie az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. Az alábbi táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik:

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [az network vnet peering create](/cli/azure/network/vnet/peering#az_network_vnet_peering_create) | Társviszonyt létesít két virtuális hálózat között.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

A [virtuális hálózatokra vonatkozó CLI-minták](../cli-samples.md) között további, a virtuális hálózatokra vonatkozó CLI-példaszkripteket talál.
