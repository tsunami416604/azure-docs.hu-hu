---
title: "Az Azure CLI-parancsfájlt minta - létrehozott két virtuális gépet egy belső és külső NSG |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minták – hozzon létre két belső és külső NSG"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc
ms.openlocfilehash: 84092e3a70f1bfde923ba3395fbc0a46c11e233e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="secure-network-traffic-between-virtual-machines"></a>Virtuális gépek közötti hálózati forgalmának biztonságossá tétele

Ez a parancsfájl létrehoz két virtuális gép, és mindkét bejövő forgalmat. Egy virtuális gép elérhető az interneten, és úgy a hálózati biztonsági csoport (NSG) beállítva, hogy engedélyezze a forgalmat a 3389-es és a 80-as porton. A második virtuális gép nem érhető el az interneten, és hogy csak az első virtuális gép forgalmát az NSG konfigurálta. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, virtuális gép és minden kapcsolódó erőforrás létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az hálózati virtuális hálózat létrehozása](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Létrehoz egy Azure-beli virtuális hálózat és az alhálózatot. |
| [az alhálózaton virtuális hálózat létrehozása](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | -Alhálózatot hoz létre. |
| [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | A virtuális gépet hoz létre, és csatlakozik a hálózati kártya, virtuális hálózatot, alhálózatot és NSG. Ez a parancs is meghatározza a használandó virtuálisgép-lemezkép, és rendszergazdai hitelesítő adatait.  |
| [az hálózati nsg-szabály frissítése](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_update) | Az NSG-szabály frissítése. Ez a minta a háttér-szabály csak az előtér-alhálózat a forgalom áthaladását frissül. |
| [az hálózati nsg-szabályok listája](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_list) | A hálózati biztonsági csoport szabály információt ad vissza. A példában a szabály neve használható később a parancsfájl egy változó tárolódik. |
| [az csoport törlése](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép CLI parancsfájl minták megtalálhatók a [Azure Windows virtuális dokumentációját](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
