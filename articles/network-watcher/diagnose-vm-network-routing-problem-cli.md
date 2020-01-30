---
title: Virtuálisgép-hálózat útválasztási problémáinak diagnosztizálása – Azure CLI
titleSuffix: Azure Network Watcher
description: Ebből a cikkből megtudhatja, hogyan diagnosztizálhatja a virtuális gépek hálózati útválasztási problémáit az Azure Network Watcher következő ugrási funkciója segítségével.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: bf4c5e364b7f18b363f9915f54e43c7ea54c33c4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834673"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Virtuálisgép-hálózat útválasztási problémáinak diagnosztizálása – Azure CLI

Ebben a cikkben üzembe helyez egy virtuális gépet (VM), majd megtekintheti a kommunikációt egy IP-címmel és egy URL-címmel. Meghatározza a kommunikációs hiba okát és feloldásának módját.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verzióját kell futtatnia. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. A CLI-verzió ellenőrzése után futtassa az `az login` parancsot az Azure-hoz való kapcsolódáshoz. A cikkben szereplő CLI-parancsok egy bash-rendszerhéjban történő futtatásra vannak formázva.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Mielőtt virtuális gépet hozhatna létre, létre kell hoznia egy erőforráscsoportot, amely majd tartalmazza a virtuális gépet. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. Az alábbi példában egy *myVmM* nevű virtuális gépet hozunk létre:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a további lépések megtételét, amíg létre nem jön a virtuális gép, és a parancssori felület vissza nem adja a kimenetet.

## <a name="test-network-communication"></a>Hálózati kommunikáció tesztelése

A Network Watcherával folytatott hálózati kommunikáció teszteléséhez először engedélyeznie kell egy hálózati figyelőt abban a régióban, ahol a tesztelni kívánt virtuális gép be van kapcsolva, majd a kommunikáció teszteléséhez használja a Network Watcher következő ugrási képességét.

### <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha az USA keleti régiójában már engedélyezve van egy hálózati figyelő, ugorjon a [következő ugrás használata](#use-next-hop)lehetőségre. Az az [Network Watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) parancs használatával hozzon létre egy Network watchert az USA keleti régiójában:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>A következő ugrás használata

Az Azure automatikusan létrehoz útvonalakat az alapértelmezett célokhoz. Egyéni útvonalakat is létrehozhat, amelyekkel felülírhatja az Azure alapértelmezett útvonalait. Bizonyos esetekben az egyéni útvonalak kommunikációs hibákat eredményezhetnek. Egy virtuális gép útválasztásának teszteléséhez használja az [az Network Watcher show-Next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) elemet a következő útválasztási ugrás meghatározásához, ha a forgalom egy adott címnek van szánva.

Tesztelje a virtuális gép kimenő kommunikációját a www.bing.com IP-címeinek egyikén:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Néhány másodperc elteltével a kimenet tájékoztatja Önt arról, hogy a **NextHopType** **internetes**, és hogy a **routeTableId** a **rendszer útvonala**. Ebből az eredményből megtudhatja, hogy van-e érvényes útvonal a célhelyre.

Tesztelje a virtuális gép kimenő kommunikációját a 172.31.0.100 címen:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

A visszaadott kimenet tájékoztatja, hogy **egyik sem** a **nextHopType**, és hogy a **routeTableId** is a **rendszer útvonala**. Ez az eredmény azt jelzi, hogy létezik érvényes rendszerútvonal a cél felé, de nincs következő ugrás, hogy a forgalmat a cél felé irányítsa.

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

Az Útválasztás további elemzéséhez tekintse át a hálózati adapter érvényes útvonalait az az [Network NIC show-effektív-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) paranccsal:

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

A visszaadott kimenet a következő szöveget tartalmazza:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

Ha a `az network watcher show-next-hop` parancs használatával teszteli a 13.107.21.200 kimenő kommunikációját a [következő ugrás használata](#use-next-hop)során, a rendszer a 0.0.0.0/0 * * **addressPrefix** útvonalat használta a forgalom a címnek való irányítására, mivel a kimenetben nem szerepel más útvonal a címben. Alapértelmezés szerint minden olyan cím, amely nincs egy másik útvonal címelőtagjában meghatározva, az internetre lesz irányítva.

Ha a `az network watcher show-next-hop` parancsot használta a 172.31.0.100 kimenő kommunikáció tesztelésére, az eredmény arról tájékoztat, hogy nem volt következő ugrási típus. A visszaadott kimenetben a következő szöveg is látható:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Ahogy a `az network watcher nic show-effective-route-table` parancs kimenetében látható, a 172.16.0.0/12 előtag alapértelmezett útvonala, amely magában foglalja a 172.31.0.100-címeket, a **nextHopType** **nincs.** Az Azure létrehoz egy alapértelmezett útvonalat a 172.16.0.0/12 címhez, de amíg nincs oka rá, nem határozza meg a következő ugrás típusát. Ha például hozzáadta a 172.16.0.0/12 címtartományt a virtuális hálózat címterület számára, az Azure a **NextHopType** **virtuális hálózatra** módosítja az útvonalon. Az ellenőrzések után a **virtuális hálózat** **nextHopType**jelenik meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozta a virtuális gépet, és diagnosztizálta a hálózati útválasztást a virtuális gépről. Megtudta, hogy az Azure számos alapértelmezett utat létrehoz, és tesztelte az útválasztást két különböző cél felé. További tudnivalók az [Azure-beli útválasztásról](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és az [egyéni útvonalak létrehozásáról](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

A kimenő virtuálisgép-kapcsolatok esetében meghatározhatja a virtuális gép és a végpont közötti hálózati forgalmat a Network Watcher [kapcsolódási hibákkal kapcsolatos](network-watcher-connectivity-cli.md) funkciójának használatával is. Egy virtuális gép és egy végpont (például IP-cím vagy URL) közötti kommunikációt figyelheti az Network Watcher a kapcsolat figyelője funkciójának használatával. További információ: [hálózati kapcsolatok figyelése](connection-monitor.md).