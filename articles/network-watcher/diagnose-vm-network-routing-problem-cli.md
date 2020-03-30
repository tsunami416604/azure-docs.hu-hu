---
title: Virtuálisgép hálózati útválasztási problémájának diagnosztizálása – Azure CLI
titleSuffix: Azure Network Watcher
description: Ebben a cikkben megtudhatja, hogyan diagnosztizálhatja a virtuális gép hálózati útválasztási probléma az Azure Network Watcher következő ugrási képesség használatával.
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
ms.openlocfilehash: ae139ea7aca7c3896fcd7b0acf2bf6673490a2f4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382902"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Virtuálisgép-hálózati útválasztási probléma diagnosztizálása – Azure CLI

Ebben a cikkben üzembe helyez egy virtuális gépet (VM), majd ellenőrizze a kommunikáció t ip-címés URL-cím. Meghatározza a kommunikációs hiba okát és feloldásának módját.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy telepíti és használja az Azure CLI helyileg, ez a cikk megköveteli, hogy az Azure CLI 2.0.28-as vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. Miután ellenőrizte az Azure CLI-verziót, futtassa `az login` a kapcsolatot az Azure-ral. Az Azure CLI-parancsok ebben a cikkben vannak formázva, hogy egy Bash shell futtatásához.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Mielőtt virtuális gépet hozhatna létre, létre kell hoznia egy erőforráscsoportot, amely majd tartalmazza a virtuális gépet. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A következő példa létrehoz egy *myVm*nevű virtuális gép:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a fennmaradó lépéseket, amíg a virtuális gép létre nem jön, és az Azure CLI kimenetet ad vissza.

## <a name="test-network-communication"></a>Hálózati kommunikáció tesztelése

A hálózati kommunikáció teszteléséhez a Hálózati figyelővel először engedélyeznie kell egy hálózati figyelőt abban a régióban, amelyben a tesztelni kívánt virtuális gép található, majd a Hálózati figyelő következő ugrási képességét kell használnia a kommunikáció teszteléséhez.

### <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha már engedélyezve van a hálózatfigyelő az USA keleti régiójában, ugorjon a [Következő ugrás használata](#use-next-hop)elemre. Használja az [az hálózati figyelő konfigurálása](/cli/azure/network/watcher#az-network-watcher-configure) parancsot, hogy hozzon létre egy hálózati figyelő az USA keleti régiójában:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>A következő ugrás használata

Az Azure automatikusan létrehoz útvonalakat az alapértelmezett célokhoz. Egyéni útvonalakat is létrehozhat, amelyekkel felülírhatja az Azure alapértelmezett útvonalait. Bizonyos esetekben az egyéni útvonalak kommunikációs hibákat eredményezhetnek. A virtuális gépről történő útválasztás teszteléséhez használja [az az hálózati figyelő show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) segítségével határozza meg a következő útválasztási ugrást, ha a forgalom egy adott címre irányul.

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

Néhány másodperc múlva a kimenet arról tájékoztat, hogy a **nextHopType** **az Internet**, és hogy a **routeTableId** a **Rendszerútvonal**. Ez az eredmény tudatja Önvel, hogy van egy érvényes útvonal a célhoz.

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

A visszaadott kimenet arról tájékoztat, hogy a **Nincs** a **nextHopType**, és hogy a **routeTableId** egyben **rendszerútvonal**is. Ez az eredmény azt jelzi, hogy létezik érvényes rendszerútvonal a cél felé, de nincs következő ugrás, hogy a forgalmat a cél felé irányítsa.

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

Az útválasztás további elemzéséhez tekintse át a hálózati adapter hatékony útvonalait az [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) paranccsal:

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

A visszaadott kimenet a következő szöveget tartalmazza:

```
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

Amikor a `az network watcher show-next-hop` paranccsal tesztelte a kimenő kommunikációt a 13.107.21.200-ra [a Következő ugrás használata](#use-next-hop)korban, a 0.0.0.0/0** **addressPrefix-lel** rendelkező útvonalat használták a forgalom címhez való irányításához, mivel a kimenetben egyetlen más útvonal sem tartalmazza a címet. Alapértelmezés szerint minden olyan cím, amely nincs egy másik útvonal címelőtagjában meghatározva, az internetre lesz irányítva.

Amikor azonban `az network watcher show-next-hop` a paranccsal tesztelte a kimenő kommunikációt a 172.31.0.100-ra, az eredmény arról tájékoztatott, hogy nincs következő ugrástípus. A visszaadott kimeneten a következő szöveg is látható:

```
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

Amint az a `az network watcher nic show-effective-route-table` parancs kimenetében látható, bár van egy alapértelmezett útvonal a 172.16.0.0/12 előtaghoz, amely tartalmazza a 172.31.0.100 címet, a **nextHopType** **nincs**. Az Azure létrehoz egy alapértelmezett útvonalat a 172.16.0.0/12 címhez, de amíg nincs oka rá, nem határozza meg a következő ugrás típusát. Ha például hozzáadta a 172.16.0.0/12 címtartományt a virtuális hálózat címteréhez, az Azure a **nextHopType-ot** **virtuális hálózatra** módosítja az útvonalhoz. Az ellenőrzés ekkor a **virtuális hálózatot** fogja megjeleníteni **nextHopType**néven.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy virtuális gép, és diagnosztizált hálózati útválasztás a virtuális gépről. Megtudta, hogy az Azure számos alapértelmezett utat létrehoz, és tesztelte az útválasztást két különböző cél felé. További tudnivalók az [Azure-beli útválasztásról](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és az [egyéni útvonalak létrehozásáról](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Kimenő virtuálisgép-kapcsolatok esetén is meghatározhatja a késést, és engedélyezett és megtagadta a hálózati forgalmat a virtuális gép és egy végpont között a Network Watcher [kapcsolathibaelhárítási](network-watcher-connectivity-cli.md) képesség használatával. A virtuális gép és a végpont, például egy IP-cím vagy URL közötti kommunikáció figyelhető a Network Watcher kapcsolatfigyelő funkció használatával. Ebből, hogy miként, olvassa el [a Hálózati kapcsolat figyelése ..](connection-monitor.md)
