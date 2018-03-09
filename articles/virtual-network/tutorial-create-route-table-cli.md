---
title: "Hálózati forgalmat - az Azure parancssori felület |} Microsoft Docs"
description: "Útmutató egy útválasztási táblázathoz, az Azure parancssori felület használatával irányítható a hálózati forgalom."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Hálózati forgalom egy útválasztási táblázathoz, az Azure parancssori felület használatával

Az Azure automatikusan útvonalak forgalom alapértelmezés szerint egy virtuális hálózatban található alhálózatok között. A saját Azure felülbírálására útvonalak létrehozása alapértelmezett útválasztási. Képes létrehozni az egyéni útvonalak akkor hasznos, ha például azt szeretné, a tűzfalon keresztül alhálózatok közötti forgalom irányítására. A cikkben megismerheti, hogyan:

> [!div class="checklist"]
> * Hozzon létre egy útválasztási táblázatot
> * Útvonal létrehozása
> * Társítsa a virtuális hálózati alhálózat egy útválasztási táblázatot
> * Útválasztás tesztelése
> * Útválasztás hibaelhárítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Rendszererőforrásokra telepíti, és a parancssori felület helyileg, a gyors üzembe helyezés megköveteli, hogy futnak-e az Azure parancssori felület 2.0.28 verzió vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Hozzon létre egy útválasztási táblázatot

Az Azure útvonalak forgalom virtuális hálózatban, alapértelmezés szerint minden alhálózatok között. Azure alapértelmezett útvonalak kapcsolatos további információkért lásd: [rendszerútvonalak](virtual-networks-udr-overview.md). Azure alapértelmezett útválasztási felülbírálását, létrehoz egy útválasztási táblázatot, amely tartalmazza az útvonalak, a virtuális hálózati alhálózat útválasztási táblázatot.

Mielőtt létrehozna egy útválasztási táblázatot, hozzon létre egy erőforráscsoportot, a [az csoport létrehozása](/cli/azure/group#az_group_create) létre ebben a cikkben minden erőforráshoz. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Hozzon létre egy útvonaltábla [az hálózati útvonal-táblázat létrehozása](/cli/azure/network/route#az_network_route_table_create). Az alábbi példa létrehoz egy útválasztási táblázatot nevű *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Útvonal létrehozása

Egy útválasztási táblázatot nulla vagy több útvonalat tartalmazza. Hozzon létre egy útvonalat az útvonaltáblában a [az hálózati útvonaltábla útvonal létrehozása](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

Az útvonal átirányítja egy hálózati 10.0.2.4 IP-címmel rendelkező virtuális készüléken keresztül 10.0.1.0/24 címelőtag irányuló teljes forgalomra. A hálózati virtuális készüléket és alhálózat a megadott címelőtag a későbbi lépésekben jönnek létre. Az útvonal Azure alapértelmezett útválasztási, amely irányítja a forgalmat közvetlen alhálózatba felülbírálja. Minden útvonal a következő ugrás típusa határozza meg. A következő ugrás típusa arra utasítja a Azure hogyan kell a forgalmat. Ebben a példában a következő ugrás típusa van *VirtualAppliance*. Azure-ban, és mikor érdemes használni azokat az összes elérhető következő ugrás típusok kapcsolatos további információkért lásd: [a következő ugrás típusa](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Társítson egy útválasztási táblázatot az alhálózathoz

Ahhoz, hogy társíthasson egy útválasztási táblázatot az alhálózathoz, akkor hozzon létre egy virtuális hálózati és alhálózati. Hozzon létre egy virtuális hálózatot egyetlen alhálózattal rendelkező [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Hozzon létre két további alhálózatokat a [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

A nulla vagy több alhálózatból egy útválasztási táblázatot lehet társítani. Egy alhálózat állhat nulla vagy egy útválasztási táblázatot társítva. Kimenő forgalom alhálózatból származó Azure alapértelmezett útvonalakat, és minden alhálózathoz társít egy útvonaltábla hozzáadott egyéni útvonalak alapján történik. Társítsa a *myRouteTablePublic* útválasztási táblázatot, hogy a *nyilvános* alhálózat [az hálózati vnet alhálózati frissítés](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

Az útvonaltáblák üzemi használatra való telepítése előtt javasoljuk, hogy alaposan feltérképezése [az Azure útválasztási](virtual-networks-udr-overview.md) és [Azure korlátozását](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Útválasztás tesztelése

Útválasztás teszteléséhez beállításához hozzon létre virtuális gép számára, hogy az előző lépésben létrehozott útvonal keresztül továbbítja a hálózati virtuális készülék szolgál. Miután létrehozta a virtuális hálózati készüléknek, a virtuális gép telepítésekor a *nyilvános* és *titkos* alhálózatokat. Akkor lesz majd útvonal-forgalom a *nyilvános* alhálózatában ahhoz, hogy a *titkos* keresztül a virtuális hálózati berendezések.

### <a name="create-a-network-virtual-appliance"></a>A hálózati virtuális készülék létrehozása

Az előző lépésben létrehozott egy olyan útvonalat, amely a hálózati virtuális készülék szerepel a következő ugrás típusa. Egy olyan virtuális géphez egy hálózati alkalmazás gyakran nevezik egy virtuális hálózati berendezések. Éles környezetben telepít a hálózati virtuális készülék legtöbbször egy előre konfigurált virtuális gépet. Számos hálózati virtuális készülékek érhetők el a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). Ez a cikk egy alapszintű a virtuális gép létrehozása. 

Hozzon létre egy hálózati virtuális készülék a *DMZ* alhálózat [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Amikor létrehoz egy virtuális gépet, az Azure hoz létre, és egy nyilvános IP-címet rendel a virtuális gép alapértelmezés szerint. A `--public-ip-address ""` paraméter arra utasítja az Azure nem hozhat létre, és egy nyilvános IP-cím hozzárendelése a virtuális gép, mert a virtuális gép nem kell kapcsolódnia kell az internetről. Ha SSH-kulcsok még nem léteznek a kulcs alapértelmezett helye, a parancs létrehozza azokat. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

A virtuális gép létrehozásához néhány percet vesz igénybe. Ne folytassa a következő lépéssel mindaddig, amíg az Azure létrehozza a virtuális gépet, és a virtuális géppel kapcsolatos-kimenet visszaadása. Éles környezetben telepít a hálózati virtuális készülék legtöbbször egy előre konfigurált virtuális gépet. Számos hálózati virtuális készülékek érhetők el a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Engedélyeznie kell az IP-továbbítást minden Azure [hálózati illesztő](virtual-network-network-interface.md) egy virtuális géphez, amely bármely IP-cím, amely nincs hozzárendelve a hálózati adapter felé forgalmat továbbítja. A hálózati virtuális készülék létrehozásakor a *DMZ* alhálózat, a Azure automatikusan létrejön egy adott hálózati csatoló nevű *myVmNvaVMNic*, a hálózati illesztő a virtuális géphez csatolt és a privát IP-címmel *10.0.2.4* a hálózati illesztőhöz. Engedélyezze a hálózati illesztő – az IP-továbbítást [az hálózati nic frissítés](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

A virtuális gépekről az operációs rendszer és a virtuális gépen futó alkalmazások is kell tudni továbbítja a hálózati forgalmat. A hálózati virtuális készülék éles környezetben üzembe helyezésekor a készülék általában szűrők, naplózza, vagy forgalmat előtt néhány egyéb funkciót hajt végre. Ez a cikk azonban az operációs rendszer egyszerűen továbbítja kap az összes forgalom. A virtuális gép operációs rendszerben az IP-továbbítás engedélyezése [az virtuálisgép-bővítmény készlet](/cli/azure/vm/extension#az_vm_extension_set), amely, amely lehetővé teszi az operációs rendszerből IP-továbbítás parancs végrehajtása.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
A parancs is tarthat egy percre hajtható végre.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Két virtuális gép létrehozása virtuális hálózatban, így ellenőrizheti, hogy a forgalom a *nyilvános* alhálózati annak biztosítására, hogy a *titkos* alhálózatot a hálózati virtuális készülék egy későbbi lépésben keresztül. 

A virtuális gép létrehozása a *nyilvános* alhálózat [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). A `--no-wait` paraméter lehetővé teszi, hogy az Azure a parancs végrehajtása a háttérben, így a következő paranccsal is. Ez a cikk egyszerűsítésére, a jelszó szolgál. Kulcsok általában használják az éles környezetekben. Ha a kulcsok használatához SSH-ügynöktovábbítást is konfigurálnia kell. További információkért az SSH-ügyfél a dokumentációban. Cserélje le `<replace-with-your-password>` az alábbi parancs a jelszóval.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

A virtuális gép létrehozása a *titkos* alhálózat.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A virtuális gép létrehozásához néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure parancssori felület információkat jeleníti meg az alábbi példához hasonló: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Vegye figyelembe a **publicIpAddress**. Ez a cím a virtuális gép egy későbbi lépésben az internetről való eléréséhez használt.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>A hálózati virtuális készülék-útvonal forgalmát

Az SSH-munkamenetet létrehozni, az alábbi parancs segítségével a *myVmMgmt* virtuális gépet. Cserélje le  *<publicIpAddress>*  a virtuális gép a nyilvános IP-címmel. A fenti példában az IP-cím van *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Amikor a rendszer kéri a jelszót, írja be a jelszót, a kiválasztott [virtuális gépek létrehozása](#create-virtual-machines).

A következő paranccsal telepíthető traceroute a *myVmMgmt* virtuális gépet:

```bash 
sudo apt-get install traceroute
```

Ellenőrizze a hálózati forgalmat és az Útválasztás a következő parancs segítségével a *myVmWeb* virtuális gépek létrehozását a *myVmMgmt* virtuális gépet.

```bash
traceroute myvmweb
```

A rendszer a választ az alábbi példához hasonló:

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Láthatja, hogy továbbítódik közvetlenül a *myVmMgmt* a virtuális gépet a *myVmWeb* virtuális gépet. Azure alapértelmezett útvonalak, közvetlen alhálózatba irányíthatja a forgalmat. 

A következő parancs segítségével SSH-kapcsolatot a *myVmWeb* a virtuális gép a *myVmMgmt* virtuális gépet:

```bash 
ssh azureuser@myVmWeb
```

A következő paranccsal telepíthető traceroute a *myVmWeb* virtuális gépet:

```bash 
sudo apt-get install traceroute
```

Ellenőrizze a hálózati forgalmat és az Útválasztás a következő parancs segítségével a *myVmMgmt* virtuális gépek létrehozását a *myVmWeb* virtuális gépet.

```bash
traceroute myvmmgmt
```

A rendszer a választ az alábbi példához hasonló:

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Láthatja, hogy az első ugrásnál 10.0.2.4, amely a hálózati virtuális készülék magánhálózati IP-cím-e. A kétugrásos 10.0.1.4, a magánhálózati IP-címe a *myVmMgmt* virtuális gépet. Az útvonal hozzá a *myRouteTablePublic* útvonaltábla és társított a *nyilvános* alhálózati, hogy a forgalmat az NVA keresztül, és nem közvetlenül az Azure a *saját* alhálózat.

Zárja be az SSH-munkamenet, mind a *myVmWeb* és *myVmMgmt* virtuális gépek.

## <a name="troubleshoot-routing"></a>Útválasztás hibaelhárítása

A korábbi lépésekben megismert Azure érvényes alapértelmezett útvonalakat, szükség esetén felülírható saját útvonalak. Egyes esetekben forgalom irányítása nem feltétlenül, nincs rá szükség. Használjon [az hálózati figyelő megjelenítése-következő ugrási](/cli/azure/network/watcher#az_network_watcher_show_next_hop) annak meghatározásához, hogy hogyan továbbítódik két virtuális gépek között. Például a következő parancsot a forgalom útválasztásához megvizsgálja-e a *myVmWeb* (10.0.0.4) a virtuális gépet a *myVmMgmt* (10.0.1.4) virtuális gépet:

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
A következő kimenetet visszaadja a rövid várakozás után:

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

A kimeneti arról tájékoztatja, hogy a következőugrás-forgalmat az IP-cím *myVmWeb* való *myVmMgmt* 10.0.2.4 van (a *myVmNva* virtuális gép), ,hogykövetkezőugrás-típusértéke *VirtualAppliance*, és hogy van-e az útvonaltáblát az útválasztási okozó *myRouteTablePublic*.

A hatékony útvonalak mindegyik hálózati interfész olyan Azure alapértelmezett útvonalak és bármely útvonalakat adhat meg. Tekintse meg az összes útvonal a hálózati illesztő a virtuális gép hatékony [az hálózati nic megjelenítése-hatályos-útvonal-tábla](/cli/azure/network/nic#az_network_nic_show_effective_route_table). Ahhoz például, hogy a hatékony útvonalait megjelenítése a *MyVmWebVMNic* a kapcsolat a *myVmWeb* virtuális gépet, adja meg a következő parancsot:

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

Vissza az összes alapértelmezett útvonalak és az előző lépésben felvett útvonal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [az csoport törlése](/cli/azure/group#az_group_delete) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy útválasztási táblázatot, és az alhálózathoz társított. A hálózati virtuális készülék titkos alhálózathoz nyilvános alhálózatból származó forgalmat portleképezéseit létrehozott. A virtuális hálózaton belül számos Azure-erőforrások telepítése során egyes Azure PaaS szolgáltatások erőforrás nem telepíthető virtuális hálózatba. Továbbra is korlátozzuk néhány Azure PaaS-szolgáltatást csak a virtuális hálózati alhálózat forgalom erőforrásaihoz, ha. A következő oktatóanyag áttekintésével megismerheti, hogyan Azure PaaS erőforrásokhoz való hálózati hozzáférés korlátozása továbblépés.

> [!div class="nextstepaction"]
> [Hálózati hozzáférés korlátozása PaaS erőforrások](virtual-network-service-endpoints-configure.md#azure-cli)
