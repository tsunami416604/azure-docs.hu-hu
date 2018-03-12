---
title: "Az Azure CLI parancsfájl minta - betöltési oszthatja el a forgalmat a virtuális gépek magas rendelkezésre állásra |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - betöltési oszthatja el a forgalmat a virtuális gépek magas rendelkezésre álláshoz"
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
ms.openlocfilehash: 810899c671ea141f45f7ce140c3f862f568a2f87
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>Betöltési oszthatja el a forgalmat a virtuális gépek magas rendelkezésre álláshoz

A parancsfájl minta létrehoz minden szükséges több Ubuntu virtuális gép magas rendelkezésre állású konfigurált futtatásához, majd betölteni az elosztott terhelésű konfigurációját. A parancsfájl futtatása után meg kell három virtuális gépet, az Azure rendelkezésre állási csoport, és az Azure Load Balancer keresztül érhető el. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport, virtuális gép, a rendelkezésre állási csoporthoz, terheléselosztó és minden kapcsolódó erőforrások létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az hálózati virtuális hálózat létrehozása](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Létrehoz egy Azure-beli virtuális hálózat és az alhálózatot. |
| [az hálózati nyilvános ip-létrehozása](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Hoz létre egy nyilvános IP-cím egy statikus IP-címet és egy társított DNS-névvel. |
| [az hálózati terheléselosztó létrehozása](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Egy Azure terheléselosztót hoz létre. |
| [az hálózati lb mintavétel létrehozása](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Terheléselosztói mintavétel létrehozása. Terheléselosztói mintavétel egyes virtuális gépek, a betöltés terheléselosztó-készlet figyelésére használható. Ha a virtuális gép elérhetetlenné válik, nem továbbítódik a virtuális géphez. |
| [az hálózati terheléselosztó szabály létrehozása](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Létrehoz egy terheléselosztó szabályhoz. Ez a példa létrejön egy szabály 80-as porton. HTTP-forgalom érkezik a terheléselosztót, mert a 80-as port továbbításuk a Terheléselosztó-készlet a virtuális gépek közül. |
| [az hálózati terheléselosztó bejövő forgalmat kezelő nat-szabályok létrehozása](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | Terheléselosztó hálózati címfordítás (NAT) szabály hoz létre.  NAT-szabályok leképezése egy portot a virtuális gép egy terheléselosztó-portot. Ez a példa egy NAT-szabály jön létre a terhelés terheléselosztó-készlet minden egyes virtuális gépek SSH forgalmát.  |
| [az hálózati nsg létrehozása](https://docs.microsoft.com/cli/azure/network/nsg#az_network_nsg_create) | Hálózati biztonsági csoport (NSG), amely az internethez és a virtuális gép biztonsági határt hoz létre. |
| [az hálózati nsg-szabály létrehozása](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Létrehoz egy NSG-szabály, amely engedélyezi a bejövő forgalmat. Ez a példa 22-es portot SSH forgalom van megnyitva. |
| [az hálózati hálózati adapter létrehozása](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Létrehoz egy virtuális hálózati kártya, és csatolja azt a virtuális hálózati alhálózat és NSG. |
| [az virtuális gép rendelkezésre állási-csoport létrehozása](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Létrehoz egy rendelkezésre állási csoportot. Rendelkezésre állási készletek úgy a virtuális gépek fizikai erőforrások között úgy, hogy hiba esetén nem történik teljes alkalmazás hasznos üzemidő biztosítása. |
| [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) | A virtuális gépet hoz létre, és csatlakozik a hálózati kártya, virtuális hálózatot, alhálózatot és NSG. Ez a parancs is meghatározza a virtuálisgép-lemezkép használt és a felügyeleti hitelesítő adatokat kell.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure-hálózat CLI parancsfájl minták megtalálhatók a [Azure Networking dokumentáció](../cli-samples.md).