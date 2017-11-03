---
title: "Az Azure CLI-parancsfájlt minta - hálózat a többrétegű alkalmazások létrehozása |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minta - Többrétegű alkalmazások virtuális hálózat létrehozása."
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
ms.openlocfilehash: de65d820f2d9eea49b58185c81d815675fd76740
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-network-for-multi-tier-applications"></a>A többrétegű alkalmazások hálózat létrehozása

Ez a parancsfájl-minta előtér- és alhálózatok hoz létre a virtuális hálózat. Az előtér-alhálózat felé irányuló forgalom korlátozódik HTTP és az SSH-közben az adatforgalom a háttér-alhálózathoz MySQL, port 3306 korlátozódik. A parancsfájl futtatása után két virtuális gép, egy-egy mindegyik alhálózat, amely központilag telepíthető a webkiszolgáló és szoftverrel a MySQL fog rendelkezni.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Mintaparancsfájl


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

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
| [az alhálózaton létrehozása](/cli/azure/network/vnet/subnet#create) | Háttér-alhálózatot hoz létre. |
| [az hálózati nyilvános ip-létrehozása](/cli/azure/network/public-ip#create) | Létrehoz egy nyilvános IP-címet a virtuális gép az internetről való eléréséhez. |
| [az hálózati hálózati adapter létrehozása](/cli/azure/network/nic#create) | Létrehozza a virtuális hálózati adapterhez, és csatolja őket a virtuális hálózat előtér- és alhálózatok. |
| [az hálózati nsg létrehozása](/cli/azure/network/nsg#create) | Hálózati biztonsági csoportok (NSG) az előtér- és alhálózatához tartozó hoz létre. |
| [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#create) |Hoz létre, amely engedélyezi vagy letiltja az adott portok meghatározott alhálózatokra NSG-szabályok. |
| [az virtuális gép létrehozása](/cli/azure/vm#create) | Létrehozza a virtuális gépeket, és minden virtuális gép egy hálózati adapter csatlakozik. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép és a rendszergazdai hitelesítő adatait. |
| [az csoport törlése](/cli/azure/group#delete) | Törli az erőforráscsoportot és a benne található összes erőforrást. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](/cli/azure/overview).

További hálózati CLI parancsfájl minták megtalálhatók a [Azure hálózati áttekintés dokumentáció](../cli-samples.md)