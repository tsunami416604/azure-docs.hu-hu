---
title: Egy virtuális gép hálózati útválasztási probléma diagnosztizálása - Azure parancssori Felülettel |} Microsoft Docs
description: Ebből a cikkből megtanulhatja a probléma diagnosztizálása érdekében a virtuális gép hálózati útválasztási a következő Ugrás funkcióval az Azure hálózati figyelőt.
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
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182499"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>A virtuális gép hálózati útválasztási probléma diagnosztizálása - Azure parancssori felület

Ebben a cikkben egy virtuális gép (VM) telepítése, és ellenőrizze a kommunikáció egy IP-címet, és az URL-CÍMÉT. Kommunikációs hiba, és hogy hogyan oldható meg okának megállapításához.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ez a cikk számára szükséges, hogy futnak-e az Azure parancssori felület 2.0.28 verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Miután meggyőződött a CLI-verziót, futtassa `az login` kapcsolat létrehozása az Azure-ral. Ebben a cikkben a parancssori felület parancsait formázott a rendszerhéjakba futtatásához.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

A virtuális gépek létrehozása előtt létre kell hoznia a virtuális Gépet tartalmazó erőforráscsoport. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. Az alábbi példakód létrehozza a virtuális gépek nevű *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A további lépéseket nem folytatható, amíg a virtuális gép létrehozása és a CLI-kimenet visszaadása.

## <a name="test-network-communication"></a>Tesztelje a hálózati kommunikációban

Hálózati kommunikáció a hálózati figyelőt teszteléséhez először engedélyezze a régióban, a tesztelni kívánt virtuális gép olyan hálózati figyelőt, és hálózati figyelőt a következő ugrás funkció segítségével tesztelheti a kommunikációs.

### <a name="enable-network-watcher"></a>Engedélyezze a hálózati figyelőt

Ha már van egy hálózati figyelőt engedélyezve az USA keleti régiójában, folytassa a [használja következő ugrásként](#use-next-hop). Használja a [az hálózati figyelőt konfigurálása](/cli/azure/network/watcher#az-network-watcher-configure) parancs futtatásával hozzon létre egy hálózati figyelőt USA keleti régiójában:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Használja a következő ugrás

Azure automatikusan létrehozza az alapértelmezett célhelyekre útvonalak. Létrehozhat egyéni útvonalakat, amelyek felülírják az alapértelmezett útvonalak. Egyes esetekben egyéni útvonalak okozhat kommunikáció sikertelen lesz. Egy virtuális gép útválasztási teszteléséhez [az hálózati figyelő megjelenítése-következő ugrási](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) határozható meg, hogy a következő útválasztási Ugrás forgalmat egy adott cím szánt számára.

Tesztelje a virtuális gép kimenő kommunikáció www.bing.com IP-címek egyikére:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Néhány másodpercen belül a kimeneti figyelmeztet, amely a **nextHopType** van **Internet**, és hogy a **routeTableId** van **Rendszerútvonal**. Ez az eredmény lehetővé teszi, hogy van-e a cél egy érvényes útvonalat.

A virtuális gép 172.31.0.100 kimenő kommunikáció teszteléséhez:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Adott vissza a kimeneti figyelmeztet, amely **nincs** van a **nextHopType**, és hogy a **routeTableId** is **Rendszerútvonal**. Ez az eredmény tudatja, míg a cél érvényes rendszerútvonal, nincs irányíthatja a forgalmat a cél nem következő ugrás.

## <a name="view-details-of-a-route"></a>Egy útvonal részleteinek megtekintése

Elemzéséhez további útválasztási, tekintse át a hálózati illesztő – a hatékony útvonalait a [az hálózati nic megjelenítése-hatályos-útvonal-tábla](/cli/azure/network/nic#az-network-nic-show-effective-route-table) parancs:

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

A visszaadott kimenet tartalmazza a következő szöveget:

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

Ha használja a `az network watcher show-next-hop` 13.107.21.200 a kimenő kommunikációt teszteléséhez parancs [használja következő ugrásként](#use-next-hop), útvonalhoz a **címelőtagja** 0.0.0.0/0** használt azóta irányíthatja a forgalmat a címre, nincs más útvonal a kimenet tartalmazza a címet. Alapértelmezés szerint az összes cím nincs megadva a címelőtagot egy másik útvonalat belül az internethez irányítja.

Ha használja a `az network watcher show-next-hop` 172.31.0.100 kimenő kommunikáció azonban teszteléséhez paranccsal, az eredmény arról, hogy nincs-e nem következő ugrás típusa. A visszaadott kimeneti is látható a következő szöveget:

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

Ahogy látja, hogy a kimenet a `az network watcher nic show-effective-route-table` parancsban, ha nincs olyan alapértelmezett útvonalat a 172.16.0.0/12 előtagot, amely tartalmazza a 172.31.0.100 cím, a **nextHopType** van **nincs**. Azure létrehoz egy alapértelmezett útvonalat 172.16.0.0/12, de nem adja meg a következő ugrás típusa, amíg nem ok arra, hogy. Ha például a 172.16.0.0/12 címtartomány hozzáadni a virtuális hálózat a címtér, Azure módosítja a **nextHopType** való **virtuális hálózati** az útvonal. Egy ellenőrzés ezután volna meg lehet jeleníteni **virtuális hálózati** , a **nextHopType**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy virtuális gép létrehozása, és felderítette a hálózati útválasztást a virtuális gépről. Megtudta, hogy Azure több alapértelmezett útvonalak és tesztelt útválasztási két különböző helyekre. További információ [az Azure útválasztási](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és [hozhat létre egyéni útvonalakat](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Kimenő virtuális gép kapcsolatok, akkor is meghatározhatja, a késés és engedélyezett és letiltott hálózati forgalmat a virtuális gép és a hálózati figyelőt egy végpont között [kapcsolati hibáinak elhárítása](network-watcher-connectivity-cli.md) funkció. A hálózati figyelőt kapcsolat figyelő funkcióval időbeli egy virtuális gép és egy végpontot, például az IP-címet vagy URL-címe, közötti kommunikáció figyelheti. Megtudhatja, hogyan, lásd: [a hálózati kapcsolat figyelése](connection-monitor.md).