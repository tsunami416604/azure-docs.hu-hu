---
title: "Az Azure CLI parancsfájl minta - útvonal forgalmát a hálózati virtuális készülék |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - hálózat virtuális készülékként egy tűzfalat-útvonal forgalmát."
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
ms.openlocfilehash: af6e9d55038acba194f3cc631ff1b4e2e371852c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>A hálózati virtuális készülék-útvonal forgalmát

Ez a parancsfájl-minta előtér- és alhálózatok hoz létre a virtuális hálózat. Az IP-továbbítás engedélyezve van a két alhálózat közötti forgalom egy virtuális Gépet is létrehoz. A parancsfájl futtatása után telepítheti a hálózati szoftverek, például egy tűzfal alkalmazást, a virtuális géphez.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Mintaparancsfájl


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

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
| [az alhálózaton létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Háttér- és DMZ-alhálózatot hoz létre. |
| [az hálózati nyilvános ip-létrehozása](/cli/azure/network/public-ip#az_network_public_ip_create) | Létrehoz egy nyilvános IP-címet a virtuális gép az internetről való eléréséhez. |
| [az hálózati hálózati adapter létrehozása](/cli/azure/network/nic#az_network_nic_create) | Létrehoz egy virtuális hálózati adapter és az IP-továbbítás engedélyezése. |
| [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create) | A hálózati biztonsági csoport (NSG) hoz. |
| [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Hoz létre NSG-szabályok, amelyek lehetővé teszik a VM bejövő HTTP és HTTPS-portok. |
| [az hálózat vnet alhálózat frissítése](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| Az NSG-ket és útvonaltáblák alhálózatokra társítja. |
| [az hálózati útvonal-táblázat létrehozása](/cli/azure/network/route-table#az_network_route_table_create)| Az összes útvonal egy útválasztási táblázatot hoz létre. |
| [az hálózati útvonaltábla útvonal létrehozása](/cli/azure/network/route-table/route#az_network_route_table_route_create)| Irányíthatja a forgalmat a alhálózatok között, valamint az interneten keresztül a virtuális gép útvonalakat hoz létre. |
| [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) | Létrehoz egy virtuális gépet, és a hálózati adapter csatlakozik. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép és a rendszergazdai hitelesítő adatait. |
| [az csoport törlése](/cli/azure/group#az_group_delete) | Törli az erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).

További hálózati CLI parancsfájl minták megtalálhatók a [Azure hálózati áttekintés dokumentáció](../cli-samples.md)