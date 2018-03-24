---
title: Az Azure CLI parancsfájl minta - szűrő virtuális gépek hálózati forgalmához |} Microsoft Docs
description: Az Azure CLI-parancsfájlt minták – a bejövő és kimenő virtuális gép hálózati forgalom szűrésére.
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
ms.openlocfilehash: bfc894ea718205ac77be48552f6cb5a076572395
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>Bejövő és kimenő virtuális gép hálózati forgalom szűrésére

Ez a parancsfájl-minta előtér- és alhálózatok hoz létre a virtuális hálózat. Bejövő hálózati forgalom az előtér-alhálózathoz csak HTTP, HTTPS és SSH-, míg a kimenő forgalom az internethez, a háttér-alhálózatból nem engedélyezett. A parancsfájl futtatása után a két hálózati adaptert egy virtuális gép lesz. Egyes hálózati adapterek csatlakoztatva van egy másik alhálózat.

A parancsfájl az Azure-ból hajthat végre [felhő rendszerhéj](https://shell.azure.com/bash), vagy a helyi Azure CLI-telepítés. Helyileg a CLI-t használja, ha ezt a parancsfájlt van szükség, hogy futnak-e 2.0.28 verzió vagy újabb. A telepített verzió megkereséséhez futtassa `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja a CLI, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Futtassa a következő parancs futtatásával távolítsa el az erőforráscsoportot, virtuális gép és az összes kapcsolódó erőforrások:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, a virtuális hálózat és a hálózati biztonsági csoportok létrehozásához. Minden parancs az alábbi táblázatban hivatkozások parancs-specifikus dokumentációját:

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Egy Azure-beli virtuális hálózat és az előtér-alhálózatot hoz létre. |
| [az alhálózaton létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Háttér-alhálózatot hoz létre. |
| [az hálózat vnet alhálózat frissítése](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Társítja az NSG-ket alhálózatokra. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Létrehoz egy nyilvános IP-címet a virtuális gép az internetről való eléréséhez. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Létrehozza a virtuális hálózati adapterhez, és csatolja őket a virtuális hálózat előtér- és alhálózatok. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Hálózati biztonsági csoportok (NSG) az előtér- és alhálózatához tartozó hoz létre. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |Hoz létre, amely engedélyezi vagy letiltja az adott portok meghatározott alhálózatokra NSG-szabályok. |
| [az vm create](/cli/azure/vm#az_vm_create) | Létrehozza a virtuális gépeket, és minden virtuális gép egy hálózati adapter csatlakozik. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép és a rendszergazdai hitelesítő adatait. |
| [az group delete](/cli/azure/group#az_group_delete) | Törli az erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További virtuális hálózati CLI parancsfájl minták található [virtuális hálózati CLI minták](../cli-samples.md).
