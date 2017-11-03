---
title: "Az Azure CLI parancsfájl minta - társ két virtuális hálózatok |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - társ két virtuális hálózatok"
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: cbb76d9549effc5da117265d0ab10a82134499a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="peer-two-virtual-networks"></a>A két partner virtuális hálózatok

Ezt a parancsfájlt hoz létre, és összeköti a két virtuális hálózatokat az ugyanabban a régióban trhough az Azure-hálózatot. A parancsfájl futtatása után létrehozhat egy társviszony-létesítés virtuális hálózatok között.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, virtuális gép és minden kapcsolódó erőforrás létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az hálózati virtuális hálózat létrehozása](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Létrehoz egy Azure-beli virtuális hálózat és az alhálózatot. |
| [az hálózati vnetben társviszony-létesítés létrehozása](https://docs.microsoft.com/cli/azure/network/vnet/peering#az_network_vnet_peering_create) | Létrehozza a társviszony-létesítés virtuális hálózatok között.  |
| [az csoport törlése](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További hálózati CLI parancsfájl minták megtalálhatók a [Azure hálózati áttekintés dokumentáció](../cli-samples.md).
