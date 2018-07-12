---
title: Irányítható a hálózati forgalom – Azure CLI-vel |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan irányíthatja a hálózati forgalom útválasztási táblázat az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: eb4a28b5a57d7e301e800cd4ad87c56b7c5df6d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38701835"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Hálózati forgalom továbbítása az Azure CLI használatával útválasztási táblázat

Egy adott virtuális hálózaton belül az Azure alapértelmezés szerint automatikusan elosztja a forgalmat az összes alhálózat között. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak létrehozása akkor lehet hasznos, hálózati virtuális berendezésen (NVA) keresztül kívánja irányítani az alhálózatok közötti forgalmat. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Útválasztási táblázat létrehozása
* Útvonal létrehozása
* Több alhálózattal rendelkező virtuális hálózat létrehozása
* Útválasztási táblázat társítása alhálózattal
* Forgalmat irányító hálózati virtuális berendezés létrehozása
* Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
* Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Mielőtt létrehozhatna egy útválasztási táblázatot, hozzon létre egy erőforráscsoportot [az csoport létrehozása](/cli/azure/group#az_group_create) az ebben a cikkben létrehozott összes erőforrást. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Hozzon létre egy útválasztási táblázatot az [az network route-table létrehozása](/cli/azure/network/route#az_network_route_table_create). Az alábbi példa létrehoz egy útvonaltáblát, nevű *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Útvonal létrehozása

Hozzon létre egy útvonalat az útvonaltáblában a [az network route-table route létrehozása](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Mielőtt hozzárendelhetne egy útválasztási táblázatot egy alhálózathoz, akkor hozzon létre egy virtuális hálózatot és alhálózatot. Hozzon létre egy virtuális hálózatot egyetlen alhálózattal rendelkező [az network vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create).

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

Társítsa a *myRouteTablePublic* útválasztási táblázatot az *nyilvános* alhálózat [az hálózati virtuális hálózat alhálózati frissítés](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>NVA létrehozása

Az NVA egy olyan virtuális gép, amely hálózati funkciót tölt be, például útválasztóként, tűzfalként vagy WAN-optimalizálóként működik.

Az NVA létrehozása a *DMZ* alhálózat [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Amikor létrehoz egy virtuális Gépet, az Azure létrehozza, és alapértelmezés szerint a virtuális gép nyilvános IP-címet rendel. A `--public-ip-address ""` paraméter nem lehet létrehozni, és a egy nyilvános IP-cím hozzárendelése a virtuális Gépet, mivel a virtuális Gépet nem kell az internetről csatlakozik Azure utasítja. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

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

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Csak akkor folytassa a következő lépés az Azure befejezte a virtuális gép létrehozását és a virtuális gép kimenetet ad vissza. 

Ahhoz, hogy egy hálózati adapter továbbíthassa a neki küldött, de nem a saját IP-címére címzett forgalmat, engedélyeznie kell az IP-továbbítást a hálózati adapteren. A hálózati adaptert az IP-továbbítás engedélyezése [az network nic update](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

A virtuális gépen, az operációs rendszeren, vagy egy a virtuális gépen futó alkalmazáson belül szintén működnie kell a hálózati forgalom továbbításának. A virtuális gép operációs rendszer, amely IP-továbbítást [az virtuálisgép-bővítmény csoportot](/cli/azure/vm/extension#az_vm_extension_set):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
A parancs is igénybe vehet egy percig végrehajtásához.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Két virtuális gép létrehozása a virtuális hálózatban, így ellenőrizheti, hogy a forgalom a *nyilvános* alhálózatra irányítja a rendszer a *privát* alhálózat egy későbbi lépésben az nva-n keresztül. 

A virtuális gép létrehozása a *nyilvános* alhálózat [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). A `--no-wait` paraméter lehetővé teszi, hogy a parancs végrehajtása a háttérben, így folytathatja a következő parancsot az Azure. Ez a cikk egyszerűsítésére, jelszó használatos. Kulcsok jellemzően használják az éles környezetekben. Ha használja kulcsokkal, SSH-ügynöktovábbítást is konfigurálnia kell. További információkért tekintse meg az SSH-ügyfél dokumentációját. Cserélje le `<replace-with-your-password>` az a következő parancsot egy tetszőleges jelszót.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

A virtuális gép létrehozása a *privát* alhálózat.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI információkat jelenít meg az alábbi példához hasonló: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Jegyezze fel a **publicIpAddress** értékét. Ezzel a címmel eléri a virtuális Gépet egy későbbi lépésben az internetről.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

A következő paranccsal hozhat létre az SSH-munkamenetből a *myVmPrivate* virtuális Gépet. Cserélje le *<publicIpAddress>* a virtuális gép nyilvános IP-címét. A fenti példában az IP-cím van *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Amikor a rendszer jelszót kér, adja meg a jelszót, a kiválasztott [hozhat létre virtuális gépeket](#create-virtual-machines).

A következő paranccsal telepíthető útvonalkövetést a *myVmPrivate* virtuális Géphez:

```bash 
sudo apt-get install traceroute
```

A hálózati forgalom útválasztásának teszteléséhez a következő paranccsal a *myVmPublic* virtuális gépről a *myVmPrivate* virtuális Gépet.

```bash
traceroute myVmPublic
```

A válasz a következő példához hasonló:

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Láthatja, hogy a rendszer a *myVmPrivate* virtuális gépről közvetlenül a *myVmPublic* virtuális gépre irányítja a forgalmat. Az Azure alapértelmezett útvonalait, közvetlenül az alhálózatok közötti forgalom irányítása. 

Használja az ssh-n a következő parancsot a *myVmPublic* virtuális gépről a *myVmPrivate* virtuális Géphez:

```bash 
ssh azureuser@myVmPublic
```

A következő paranccsal telepíthető útvonalkövetést a *myVmPublic* virtuális Géphez:

```bash 
sudo apt-get install traceroute
```

A hálózati forgalom útválasztásának teszteléséhez a következő paranccsal a *myVmPrivate* virtuális gépről a *myVmPublic* virtuális Gépet.

```bash
traceroute myVmPrivate
```

A válasz a következő példához hasonló:

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Láthatja, hogy az első ugrás a 10.0.2.4 cím, amely az NVA magánhálózati IP-címe. A második ugrás a 10.0.1.4 cím – ez a *myVmPrivate* virtuális gép magánhálózati IP-címe. A*myRouteTablePublic* útválasztási táblázathoz hozzáadott és a *Magánjellegű* alhálózathoz rendelt útvonal miatt az Azure az NVA-n keresztül továbbította a forgalmat ahelyett, hogy közvetlenül a *Privát* alhálózatra továbbította volna.

Zárja be az SSH-munkamenet egyaránt a *myVmPublic* és *myVmPrivate* virtuális gépeket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, [az csoport törlése](/cli/azure/group#az_group_delete) , távolítsa el az erőforráscsoportot és az összes benne található erőforrást.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy útválasztási táblázatot, és hozzárendelte egy alhálózathoz. Létrehozott egy egyszerű NVA-t, amely átirányította a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. Az [Azure Marketplace-ről](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) számos előre konfigurált NVA-t helyezhet üzembe, amelyek olyan hálózati funkciókat végeznek, mint például a tűzfal és a WAN-optimalizálás. További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Egy virtuális hálózaton belül több Azure-erőforrást helyezhet üzembe, azonban egyes Azure PaaS-szolgáltatások erőforrásai nem helyezhetők üzembe virtuális hálózatban. Ennek ellenére korlátozhatja az egyes Azure PaaS-szolgáltatások erőforrásaihoz való hozzáférést, hogy csak egyetlen virtuális hálózati alhálózatról legyenek elérhetők. További információ [PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása](tutorial-restrict-network-access-to-resources-cli.md).
