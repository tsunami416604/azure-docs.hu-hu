---
title: Azure CLI-példaszkript – Virtuális gép hálózati forgalmának szűrése | Microsoft Docs
description: Azure CLI-példaszkript – Virtuális gép kimenő és bejövő hálózati forgalmának szűrése.
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
ms.openlocfilehash: 8e667b2ffd8820b78cbba560724527ba2c2b3bc4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="filter-inbound-and-outbound-vm-network-traffic-script-sample"></a>Virtuális gép kimenő és bejövő hálózati forgalmának szűrése – példaszkript

Ez a példaszkript előtérbeli és háttérbeli alhálózattal rendelkező virtuális hálózatot hoz létre. Az előtérbeli alhálózat bejövő hálózati forgalma a HTTP-, HTTPS- és SSH-protokollon keresztül engedélyezett, a háttérbeli alhálózat felől az internetre irányuló kimenő forgalom pedig nem engedélyezett. A szkript futtatása után egyetlen, két NIC-vel rendelkező virtuális gép fog a rendelkezésére állni. Mindegyik NIC eltérő alhálózathoz csatlakozik.

A szkriptet az Azure [Cloud Shellben](https://shell.azure.com/bash) vagy egy helyi Azure CLI-telepítésből futtathatja. Ha a parancssori felületet helyileg használja, akkor ehhez a szkripthez az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja a parancssori felületet, akkor emellett a `az login` futtatásával kapcsolatot kell teremtenie az Azure-ral.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

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
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Háttérbeli alhálózatot hoz létre. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Az NSG-ket alhálózatokhoz társítja. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Nyilvános IP-címet hoz létre, amely lehetővé teszi a virtuális gép elérését az internetről. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Virtuális hálózati adaptereket hoz létre, és a virtuális hálózat előtérbeli és háttérbeli alhálózataihoz csatolja őket. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Az előtérbeli és a háttérbeli alhálózatokhoz társított hálózati biztonsági csoportokat (NSG) hoz létre. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |NSG-szabályokat hoz létre, amelyek engedélyeznek vagy blokkolnak adott alhálózatokra mutató bizonyos portokat. |
| [az vm create](/cli/azure/vm#az_vm_create) | Virtuális gépeket hoz létre, és minden virtuális géphez csatol egy NIC-t. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai hitelesítő adatokat. |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

A [virtuális hálózatokra vonatkozó CLI-minták](../cli-samples.md) között további, a virtuális hálózatokra vonatkozó CLI-példaszkripteket talál.
