---
title: Azure CLI-példaszkript – Forgalom irányítása hálózati virtuális készüléken keresztül | Microsoft Docs
description: Azure CLI-példaszkript – Forgalom irányítása hálózati virtuális készüléken keresztül.
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
ms.openlocfilehash: d0d410fd59574fe2884ae941ef3fd64600930f31
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>Forgalom irányítása hálózati virtuális készüléken keresztül – példaszkript

Ez a példaszkript előtérbeli és háttérbeli alhálózattal rendelkező virtuális hálózatot hoz létre. Ezen kívül létrehoz egy virtuális gépet, amelyen az IP-továbbítás két alhálózat közötti útválasztása engedélyezve van. A szkript futtatása után hálózati szoftvereket, például tűzfal-alkalmazást telepíthet a virtuális gépre.

A szkriptet az Azure [Cloud Shellben](https://shell.azure.com/bash) vagy egy helyi Azure CLI-telepítésből futtathatja. Ha a parancssori felületet helyileg használja, akkor ehhez a szkripthez az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja a parancssori felületet, akkor emellett a `az login` futtatásával kapcsolatot kell teremtenie az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript az alábbi parancsokkal létrehoz egy erőforráscsoportot, egy virtuális hálózatot és hálózati biztonsági csoportokat. Az alábbi táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik:

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Létrehoz egy Azure-beli virtuális hálózatot és előtérbeli alhálózatot. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Háttérbeli és DMZ-alhálózatokat hoz létre. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Nyilvános IP-címet hoz létre, amely lehetővé teszi a virtuális gép elérését az internetről. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Virtuális hálózati adatpert hoz létre, és engedélyezi hozzá az IP-továbbítást. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Egy hálózati biztonsági csoportot (NSG) hoz létre. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | NSG-szabályokat hoz létre, amelyek engedélyezik a virtuális gép bejövő HTTP- és HTTPS-portjait. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| Az alhálózatokhoz rendeli az NSG-ket és az útválasztási táblázatokat. |
| [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create)| Útválasztási táblázatot hoz létre minden útvonalhoz. |
| [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create)| Útvonalakat hoz létre, amellyel irányíthatja a forgalmat az alhálózatok és az internet között a virtuális gépen keresztül. |
| [az vm create](/cli/azure/vm#az_vm_create) | Létrehoz egy virtuális gépet, és csatolja hozzá a NIC-t. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai hitelesítő adatokat. |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

A [virtuális hálózatokra vonatkozó CLI-minták](../cli-samples.md) között további, a virtuális hálózatokra vonatkozó CLI-példaszkripteket talál.
