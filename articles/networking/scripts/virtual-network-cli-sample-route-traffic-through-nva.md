---
title: Azure CLI-példaszkript – Forgalom irányítása hálózati virtuális készüléken keresztül | Microsoft Docs
description: Azure CLI-példaszkript – Forgalom irányítása hálózati virtuális készüléken keresztül.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: jdial
ms.openlocfilehash: 62077f45d96e96a7fef35cf025740849d2b99445
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "42059464"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Forgalom irányítása hálózati virtuális készüléken keresztül

Ez a példaszkript előtérbeli és háttérbeli alhálózattal rendelkező virtuális hálózatot hoz létre. Ezen kívül létrehoz egy virtuális gépet, amelyen az IP-továbbítás két alhálózat közötti útválasztása engedélyezve van. A szkript futtatása után hálózati szoftvereket, például tűzfal-alkalmazást telepíthet a virtuális gépre.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Példaszkript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript az alábbi parancsokkal létrehoz egy erőforráscsoportot, egy virtuális hálózatot és hálózati biztonsági csoportokat. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Létrehoz egy Azure-beli virtuális hálózatot és előtérbeli alhálózatot. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Háttérbeli és DMZ-alhálózatokat hoz létre. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Létrehoz egy nyilvános IP-címet a virtuális gép elérését az internetről. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Virtuális hálózati adatpert hoz létre, és engedélyezi hozzá az IP-továbbítást. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Egy hálózati biztonsági csoportot (NSG) hoz létre. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | NSG-szabályokat hoz létre, amelyek engedélyezik a virtuális gép bejövő HTTP- és HTTPS-portjait. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| Az alhálózatokhoz rendeli az NSG-ket és az útválasztási táblázatokat. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Útválasztási táblázatot hoz létre minden útvonalhoz. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Alhálózatok és az interneten keresztül a virtuális gép közötti forgalom irányítására útvonalakat hoz létre. |
| [az vm create](/cli/azure/vm#az_vm_create) | Létrehoz egy virtuális gépet, és csatolja hozzá a NIC-t. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai hitelesítő adatokat. |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További hálózati CLI-példaszkripteket megtalálható a [hálózati áttekintése az Azure-dokumentáció](../cli-samples.md)