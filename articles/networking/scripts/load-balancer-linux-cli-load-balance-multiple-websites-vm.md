---
title: Azure CLI-Példaszkript – több webhely terheléselosztását az Azure CLI-vel |} A Microsoft Docs
description: Azure CLI-Példaszkript – több webhely terheléselosztását ugyanahhoz a virtuális géphez
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: e3dc9476d188382db31b03b37b2a23affc61aed3
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700655"
---
# <a name="load-balance-multiple-websites"></a>Több webhely terheléselosztását kezelheti

Ez a példaszkript létrehoz egy virtuális hálózatot két virtuális gépekkel (VM), amely egy rendelkezésre állási csoport tagja. A terheléselosztó két különböző IP-cím forgalmát irányítja a két virtuális gépre. A szkript futtatása után telepíthet a webkiszolgáló szoftvert a virtuális gépekre, és több webhelyet is üzemeltethet, mindegyiket a saját IP-címével.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, virtuális hálózat, terheléselosztó és minden kapcsolódó erőforrás létrehozására. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Létrehoz egy Azure-beli virtuális hálózatot és alhálózatot. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) | Létrehoz egy nyilvános IP-címet egy statikus IP-címmel és egy hozzárendelt DNS-névvel. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) | Azure Load Balancert hoz létre. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) | Terheléselosztói mintavételt hoz létre. A terheléselosztói mintavétel a terheléselosztó-készlet egyes virtuális gépeinek figyelésére használható. Ha valamelyik virtuális gép elérhetetlenné válik, a terheléselosztó nem irányít rá forgalmat. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Terheléselosztó-szabályt hoz létre. Ebben a példában egy, a 80-es portra vonatkozó szabály jön létre. A hálózati terheléselosztóra érkező HTTP-forgalom a terheléselosztó csoporthoz tartozó egyik virtuális gép 80-as portjára lesz irányítva. |
| [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip) | Előtérbeli IP-címet hoz létre a terheléselosztóhoz. |
| [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool) | Hátércímkészletet hoz létre. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | Létrehoz egy virtuális hálózati kártyát, és csatlakoztatja a virtuális hálózathoz és alhálózathoz. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Létrehoz egy rendelkezésre állási csoportot. A rendelkezésre állási csoportok az alkalmazások rendelkezésre állását biztosítják a virtuális gépek fizikai erőforrások közötti elosztásával, hogy az esetlegesen fellépő hibák ne érintsék a teljes készletet. |
| [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config) | Létrehoz egy IP-konfigurációt. Az előfizetésében engedélyezve kell lennie a Microsoft.Network/AllowMultipleIpConfigurationsPerNic funkciónak. A --make-primary jelzővel hálózati adapterenként csak egy konfigurációt lehet elsődleges IP-konfigurációként kijelölni. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | Létrehozza a virtuális gépet, és csatlakoztatja a hálózati kártyához, a virtuális hálózathoz, az alhálózathoz és az NSG-hez. A parancs megadja továbbá a használandó virtuálisgép-rendszerképet és a rendszergazdai jelszavakat.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További hálózatkezelési CLI-példaszkripteket az [Azure-hálózatkezelés áttekintő dokumentációjában](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) találhat.
