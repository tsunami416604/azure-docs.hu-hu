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
ms.openlocfilehash: 78091b515c00591a4af8d807945475b6be50188a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
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
| [az csoport létrehozása](/cli/azure/group#create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az hálózati virtuális hálózat létrehozása](/cli/azure/network/vnet#create) | Egy Azure-beli virtuális hálózat és az előtér-alhálózatot hoz létre. |
| [az alhálózaton létrehozása](/cli/azure/network/vnet/subnet#create) | Háttér- és DMZ-alhálózatot hoz létre. |
| [az hálózati nyilvános ip-létrehozása](/cli/azure/network/public-ip#create) | Létrehoz egy nyilvános IP-címet a virtuális gép az internetről való eléréséhez. |
| [az hálózati hálózati adapter létrehozása](/cli/azure/network/nic#create) | Létrehoz egy virtuális hálózati adapter és az IP-továbbítás engedélyezése. |
| [az hálózati nsg létrehozása](/cli/azure/network/nsg#create) | A hálózati biztonsági csoport (NSG) hoz. |
| [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#create) | Hoz létre NSG-szabályok, amelyek lehetővé teszik a VM bejövő HTTP és HTTPS-portok. |
| [az hálózat vnet alhálózat frissítése](/cli/azure/network/vnet/subnet#update)| Az NSG-ket és útvonaltáblák alhálózatokra társítja. |
| [az hálózati útvonal-táblázat létrehozása](/cli/azure/network/route-table#create)| Az összes útvonal egy útválasztási táblázatot hoz létre. |
| [az hálózati útvonaltábla útvonal létrehozása](/cli/azure/network/route-table/route#create)| Irányíthatja a forgalmat a alhálózatok között, valamint az interneten keresztül a virtuális gép útvonalakat hoz létre. |
| [az virtuális gép létrehozása](/cli/azure/vm#create) | Létrehoz egy virtuális gépet, és a hálózati adapter csatlakozik. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép és a rendszergazdai hitelesítő adatait. |
| [az csoport törlése](/cli/azure/group#delete) | Törli az erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).

További hálózati CLI parancsfájl minták megtalálhatók a [Azure hálózati áttekintés dokumentáció](../cli-samples.md)