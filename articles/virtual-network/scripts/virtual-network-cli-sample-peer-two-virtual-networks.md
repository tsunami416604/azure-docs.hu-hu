---
title: Az Azure CLI parancsfájl minta - társ két virtuális hálózatok |} Microsoft Docs
description: Az Azure CLI parancsfájl minta - társ két virtuális hálózatok.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 244d7f6ff64643386c417d708f7fb1e9bbc34209
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="peer-two-virtual-networks"></a>A két partner virtuális hálózatok

Ezt a parancsfájlt hoz létre, és két virtuális hálózat ugyanabban a régióban csatlakozik az Azure-hálózatot. A parancsfájl futtatása után, hogy társviszony-létesítés virtuális hálózatok között.

A parancsfájl az Azure-ból hajthat végre [felhő rendszerhéj](https://shell.azure.com/bash), vagy a helyi Azure CLI-telepítés. Helyileg a CLI-t használja, ha ezt a parancsfájlt van szükség, hogy futnak-e 2.0.28 verzió vagy újabb. A telepített verzió megkereséséhez futtassa `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja a CLI, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Futtassa a következő parancs futtatásával távolítsa el az erőforráscsoportot, virtuális gép és az összes kapcsolódó erőforrások:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy virtuális gép és minden kapcsolódó erőforrás létrehozásához. Minden parancs az alábbi táblázatban hivatkozások parancs-specifikus dokumentációját:

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [az hálózati vnetben társviszony-létesítés létrehozása](/cli/azure/network/vnet/peering#az_network_vnet_peering_create) | Létrehozza a társviszony-létesítés virtuális hálózatok között.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További virtuális hálózati CLI parancsfájl minták található [virtuális hálózati CLI minták](../cli-samples.md).
