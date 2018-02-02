---
title: "Az Azure CLI parancsfájl minta - szűrő virtuális gépek hálózati forgalmához |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minták – a bejövő és kimenő virtuális gép hálózati forgalom szűrésére."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
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
ms.author: jdial
ms.openlocfilehash: 767c32ec265c6fb86de1bea1706cd55d7ce1098e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>Bejövő és kimenő virtuális gép hálózati forgalom szűrésére

Ez a parancsfájl-minta előtér- és alhálózatok hoz létre a virtuális hálózat. Bejövő hálózati forgalom az előtér-alhálózathoz csak HTTP, HTTPS és SSH-, míg a kimenő forgalom az internethez, a háttér-alhálózatból nem engedélyezett. A parancsfájl futtatása után a két hálózati adaptert egy virtuális gép lesz. Egyes hálózati adapterek csatlakoztatva van egy másik alhálózat.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, a virtuális hálózat és a hálózati biztonsági csoportok létrehozásához. Minden egyes parancsa a tábla-parancs-specifikus dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az hálózati virtuális hálózat létrehozása](/cli/azure/network/vnet#az_network_vnet_create) | Egy Azure-beli virtuális hálózat és az előtér-alhálózatot hoz létre. |
| [az alhálózaton létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Háttér-alhálózatot hoz létre. |
| [az hálózat vnet alhálózat frissítése](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Társítja az NSG-ket alhálózatokra. |
| [az hálózati nyilvános ip-létrehozása](/cli/azure/network/public-ip#az_network_public_ip_create) | Létrehoz egy nyilvános IP-címet a virtuális gép az internetről való eléréséhez. |
| [az hálózati hálózati adapter létrehozása](/cli/azure/network/nic#az_network_nic_create) | Létrehozza a virtuális hálózati adapterhez, és csatolja őket a virtuális hálózat előtér- és alhálózatok. |
| [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create) | Hálózati biztonsági csoportok (NSG) az előtér- és alhálózatához tartozó hoz létre. |
| [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |Hoz létre, amely engedélyezi vagy letiltja az adott portok meghatározott alhálózatokra NSG-szabályok. |
| [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) | Létrehozza a virtuális gépeket, és minden virtuális gép egy hálózati adapter csatlakozik. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép és a rendszergazdai hitelesítő adatait. |
| [az csoport törlése](/cli/azure/group#az_group_delete) | Törli az erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).

További hálózati CLI parancsfájl minták megtalálhatók a [Azure hálózati áttekintés dokumentáció](../cli-samples.md)