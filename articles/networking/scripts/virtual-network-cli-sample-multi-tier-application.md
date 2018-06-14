---
title: Az Azure CLI-parancsfájlt minta - hálózat a többrétegű alkalmazások létrehozása |} Microsoft Docs
description: Az Azure CLI-parancsfájlt minta - Többrétegű alkalmazások virtuális hálózat létrehozása.
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
ms.openlocfilehash: aa5fef6e8910a5b0b5fe89d5c8cfb141415d07a6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846638"
---
# <a name="create-a-network-for-multi-tier-applications"></a>A többrétegű alkalmazások hálózat létrehozása

Ez a parancsfájl-minta előtér- és alhálózatok hoz létre a virtuális hálózat. Az előtér-alhálózat felé irányuló forgalom korlátozódik HTTP és az SSH-közben az adatforgalom a háttér-alhálózathoz MySQL, port 3306 korlátozódik. A parancsfájl futtatása után két virtuális gép, egy-egy mindegyik alhálózat, amely központilag telepíthető a webkiszolgáló és szoftverrel a MySQL fog rendelkezni.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Példaszkript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, a virtuális hálózat és a hálózati biztonsági csoportok létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az hálózati virtuális hálózat létrehozása](/cli/azure/network/vnet#az_network_vnet_create) | Egy Azure-beli virtuális hálózat és az előtér-alhálózatot hoz létre. |
| [az alhálózaton létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Háttér-alhálózatot hoz létre. |
| [az hálózati nyilvános ip-létrehozása](/cli/azure/network/public-ip#az_network_public_ip_create) | Létrehoz egy nyilvános IP-címet a virtuális gép az internetről való eléréséhez. |
| [az hálózati hálózati adapter létrehozása](/cli/azure/network/nic#az_network_nic_create) | Létrehozza a virtuális hálózati adapterhez, és csatolja őket a virtuális hálózat előtér- és alhálózatok. |
| [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create) | Hálózati biztonsági csoportok (NSG) az előtér- és alhálózatához tartozó hoz létre. |
| [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |Hoz létre, amely engedélyezi vagy letiltja az adott portok meghatározott alhálózatokra NSG-szabályok. |
| [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) | Létrehozza a virtuális gépeket, és minden virtuális gép egy hálózati adapter csatlakozik. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép és a rendszergazdai hitelesítő adatait. |
| [az group delete](/cli/azure/group#az_group_delete) | Törli az erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További hálózati CLI parancsfájl minták megtalálhatók a [Azure hálózati áttekintés dokumentáció](../cli-samples.md)