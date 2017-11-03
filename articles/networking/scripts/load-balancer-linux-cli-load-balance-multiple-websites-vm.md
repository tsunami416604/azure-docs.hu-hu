---
title: "Az Azure CLI parancsfájl minta - terhelésének elosztása több webhely, az Azure parancssori felülettel |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - terhelésének elosztása több webhely ugyanahhoz a virtuális géphez"
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 98b07bfabf2d01c7ae3db7365cfbab3639c6f026
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="load-balance-multiple-websites"></a>Terhelésének elosztása több webhely

Parancsfájl a következő példában két virtuális gépekkel (VM), amelyek egy rendelkezésre állási csoport tagjai hoz létre egy virtuális hálózatot. A terheléselosztó két különböző IP-címeket, a két virtuális gépek forgalmát irányítja. A parancsfájl futtatása után telepíthet webkiszolgáló szoftver a virtuális gépek és a gazdagép több webhely, mindegyiket a saját IP-címét.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő parancsot az erőforráscsoport, virtuális gép és az összes kapcsolódó erőforrások eltávolítása.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, a virtuális hálózati, a terheléselosztó és a minden kapcsolódó erőforrások létrehozásához. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az hálózati virtuális hálózat létrehozása](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Létrehoz egy Azure-beli virtuális hálózat és az alhálózatot. |
| [az hálózati nyilvános ip-létrehozása](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Hoz létre egy nyilvános IP-cím egy statikus IP-címet és egy társított DNS-névvel. |
| [az hálózati terheléselosztó létrehozása](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Egy Azure terheléselosztót hoz létre. |
| [az hálózati lb mintavétel létrehozása](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Terheléselosztói mintavétel létrehozása. Terheléselosztói mintavétel egyes virtuális gépek, a betöltés terheléselosztó-készlet figyelésére használható. Ha a virtuális gép elérhetetlenné válik, nem továbbítódik a virtuális géphez. |
| [az hálózati terheléselosztó szabály létrehozása](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Létrehoz egy terheléselosztó szabályhoz. Ez a példa létrejön egy szabály 80-as porton. HTTP-forgalom érkezik a terheléselosztót, mert a 80-as port továbbításuk a terhelés terheléselosztó-készlet a virtuális gépek közül. |
| [az előtér-IP-hálózati terheléselosztó létrehozása](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) | Hozzon létre egy front-end IP-címet a terheléselosztóhoz. |
| [az hálózati lb-címkészlet létrehozása](https://docs.microsoft.com/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) | Létrehoz egy háttér címkészletet. |
| [az hálózati hálózati adapter létrehozása](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | A virtuális hálózati kártya létrehozza, és azt a virtuális hálózati és alhálózati csatolja. |
| [az virtuális gép rendelkezésre állási-csoport létrehozása](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Létrehoz egy rendelkezésre állási csoportot. Rendelkezésre állási készletek úgy a virtuális gépek fizikai erőforrások között úgy, hogy hiba esetén nem történik teljes alkalmazás hasznos üzemidő biztosítása. |
| [az hálózati hálózati adapter ip-konfiguráció létrehozása](https://docs.microsoft.com/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) | Létrehoz egy IP-confiuration. Rendelkeznie kell a Microsoft.Network/AllowMultipleIpConfigurationsPerNic funkció engedélyezett az előfizetéséhez. Csak egy konfigurációs ki lehet egy hálózati adapter, az elsődleges IP-konfiguráció használata a--ellenőrizze elsődleges jelzőt. |
| [az virtuális gép létrehozása](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | A virtuális gépet hoz létre, és csatlakozik a hálózati kártya, virtuális hálózatot, alhálózatot és NSG. Ez a parancs is meghatározza a virtuálisgép-lemezkép használt és a felügyeleti hitelesítő adatokat kell.  |
| [az csoport törlése](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Egy olyan erőforráscsoport, beleértve az összes beágyazott erőforrások törlése. |

## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További hálózati CLI parancsfájl minták megtalálhatók a [Azure hálózati áttekintés dokumentáció](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
