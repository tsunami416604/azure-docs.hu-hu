---
title: Virtuális gép hálózati útválasztási probléma diagnosztizálása – Azure CLI-vel |} A Microsoft Docs
description: Ebből a cikkből elsajátíthatja, hogyan útválasztási problémáinak diagnosztizálása virtuális gépek hálózati használatával az Azure Network Watcher következő ugrás képességét.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: fcb7ec2e40b5c0e8794d2f4d70395dcbecca019c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38618950"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Virtuális gép hálózati útválasztási probléma diagnosztizálása – Azure CLI-vel

Ebben a cikkben üzembe helyezése egy virtuális gépet (VM), és ezután ellenőrizze a kommunikációt egy IP-cím és az URL-címe. Meghatározza a kommunikációs hiba okát és feloldásának módját.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület, ez a cikk megköveteli, hogy futnak-e az Azure CLI 2.0.28-as vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). A CLI-verzió ellenőrzése után futtassa az `az login` parancsot az Azure-hoz való kapcsolódáshoz. Ebben a cikkben a CLI-parancsok futtatásához Bash-felületen vannak formázva.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Mielőtt virtuális gépet hozhatna létre, létre kell hoznia egy erőforráscsoportot, amely majd tartalmazza a virtuális gépet. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. Az alábbi példában egy *myVmM* nevű virtuális gépet hozunk létre:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a további lépések megtételét, amíg létre nem jön a virtuális gép, és a parancssori felület vissza nem adja a kimenetet.

## <a name="test-network-communication"></a>Hálózati kommunikáció tesztelése

Hálózati kommunikáció és a Network Watcher teszteléséhez először engedélyezni a network watchert a régióban, a tesztelni kívánt virtuális gép, és a Network Watcher következő ugrás képesség segítségével kommunikáció tesztelése.

### <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha már rendelkezik egy hálózati figyelő engedélyezve van az USA keleti régiójában, ugorjon [használja a következő Ugrás](#use-next-hop). Használja a [az a network watcher konfigurálása](/cli/azure/network/watcher#az-network-watcher-configure) parancs egy network watcher létrehozásához az USA keleti régiójában:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>A következő ugrás használata

Az Azure automatikusan létrehoz útvonalakat az alapértelmezett célokhoz. Egyéni útvonalakat is létrehozhat, amelyekkel felülírhatja az Azure alapértelmezett útvonalait. Bizonyos esetekben az egyéni útvonalak kommunikációs hibákat eredményezhetnek. Útválasztás a virtuális gép teszteléséhez használhatja a [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) meghatározni a következő útválasztási Ugrás, ha van egy adott cím szolgáltatásba irányuló forgalom.

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

Néhány másodperc elteltével a kimeneti figyelmeztet, amely a **nexthoptype típusa** van **Internet**, és hogy a **routeTableId** van **Rendszerútvonal**. Ez az eredmény jelzi, hogy nincs-e egy érvényes útvonalat a cél.

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

Kapott kimenetre figyelmeztet, amely **nincs** van a **nexthoptype típusa**, és hogy a **routeTableId** is **Rendszerútvonal**. Ez az eredmény azt jelzi, hogy létezik érvényes rendszerútvonal a cél felé, de nincs következő ugrás, hogy a forgalmat a cél felé irányítsa.

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

Elemzéséhez további útválasztási, tekintse át a hálózati adapter az érvényes útvonalak a [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) parancsot:

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

A következő szöveget a visszaadott kimenetet szerepel:

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

Ha használta a `az network watcher show-next-hop` parancs 13.107.21.200 a kimenő kommunikáció tesztelése [használja a következő Ugrás](#use-next-hop), az útvonal a **addressPrefix** 0.0.0.0/0** használt irányíthatja a forgalmat a címre, mivel nincs más útvonal a kimenet tartalmazza a címet. Alapértelmezés szerint minden olyan cím, amely nincs egy másik útvonal címelőtagjában meghatározva, az internetre lesz irányítva.

Ha használta a `az network watcher show-next-hop` 172.31.0.100 kimenő kommunikáció azonban teszteléséhez paranccsal, az eredmény tájékoztatja, hogy nincs következő ugrási típus nem létezik. A visszaadott kimenetben a következő szöveget is megjelenik:

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

Kimenetében láthatja a `az network watcher nic show-effective-route-table` parancsot, azonban, a 172.16.0.0/12 előtagot, amely tartalmazza a 172.31.0.100 egy alapértelmezett útvonalat címet, a **nextHopType** van **None**. Az Azure létrehoz egy alapértelmezett útvonalat a 172.16.0.0/12 címhez, de amíg nincs oka rá, nem határozza meg a következő ugrás típusát. Ha például a 172.16.0.0/12-címtartományt adott a virtuális hálózat címteréhez, Azure módosítja a **nextHopType** való **virtuális hálózati** útvonal. Egy ellenőrzés jelenik majd meg **virtuális hálózati** , a **nextHopType**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy virtuális Gépet, és felderítette a hálózati útválasztás a virtuális gépről. Megtudta, hogy az Azure számos alapértelmezett utat létrehoz, és tesztelte az útválasztást két különböző cél felé. További tudnivalók az [Azure-beli útválasztásról](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és az [egyéni útvonalak létrehozásáról](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Virtuális gép kimenő kapcsolatok esetén azt is meghatározhatja, a késés és engedélyezett és letiltott a virtuális gép és a egy végpontot a Network watcherrel közötti hálózati forgalom [kapcsolódási hibák elhárítása](network-watcher-connectivity-cli.md) képesség. A Network Watcher kapcsolódási figyelő funkcióval idővel egy virtuális Gépet és a egy végpontot, például az IP-cím vagy URL-cím közötti kommunikáció követheti nyomon. További információ [hálózati kapcsolat figyeléséhez](connection-monitor.md).