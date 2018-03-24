---
title: Hálózati forgalmat - az Azure parancssori felület |} Microsoft Docs
description: Útmutató egy útválasztási táblázathoz, az Azure parancssori felület használatával irányítható a hálózati forgalom.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 871b562fa12b93d1b65e23ca58615d35ef6bb34b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Hálózati forgalom egy útválasztási táblázathoz, az Azure parancssori felület használatával

Az Azure automatikusan útvonalak forgalom alapértelmezés szerint egy virtuális hálózatban található alhálózatok között. A saját Azure felülbírálására útvonalak létrehozása alapértelmezett útválasztási. Képes létrehozni az egyéni útvonalak akkor hasznos, ha például azt szeretné, a hálózati virtuális készülék (NVA) keresztül alhálózatok közötti forgalom irányítására. A cikkben megismerheti, hogyan:

> [!div class="checklist"]
> * Hozzon létre egy útválasztási táblázatot
> * Útvonal létrehozása
> * Hozzon létre egy virtuális hálózatot, több alhálózattal
> * Társítson egy útválasztási táblázatot az alhálózathoz
> * Hozzon létre, amely irányítja a forgalmat NVA
> * Virtuális gépek (VM) üzembe helyezés különböző alhálózatokon
> * Irányíthatja a forgalmat a egyik alhálózatról a másikra NVA keresztül

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Rendszererőforrásokra telepíti, és a parancssori felület helyileg, a gyors üzembe helyezés megköveteli, hogy futnak-e az Azure parancssori felület 2.0.28 verzió vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Hozzon létre egy útválasztási táblázatot

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

Hozzon létre egy útvonalat az útvonaltáblában a [az hálózati útvonaltábla útvonal létrehozása](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

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

Társítsa a *myRouteTablePublic* útválasztási táblázatot, hogy a *nyilvános* alhálózat [az hálózati vnet alhálózati frissítés](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>NVA létrehozása

NVA egy virtuális Gépet egy hálózati funkció, például az útválasztást, firewalling vagy WAN-optimalizálást végző.

Hozzon létre az NVA a *DMZ* alhálózat [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Amikor létrehoz egy virtuális Gépet, az Azure hoz létre, és egy nyilvános IP-címet rendel a virtuális gép alapértelmezés szerint. A `--public-ip-address ""` paraméter arra utasítja az Azure nem hozhat létre, és egy nyilvános IP-cím hozzárendelése a virtuális gép, mert a virtuális Gépet nem kell kapcsolódnia kell az internetről. Ha SSH-kulcsok még nem léteznek a kulcs alapértelmezett helye, a parancs létrehozza azokat. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

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

A virtuális gép létrehozásához néhány percet vesz igénybe. Ne folytassa a következő lépéssel mindaddig, amíg az Azure létrehozza a virtuális Gépet, és a virtuális Géphez tudnivalók-kimenet visszaadása. 

A hálózati adaptert továbbküldhetik hálózati forgalmat küldeni, nem a saját IP-cím, IP-továbbítás engedélyezni kell a hálózati adapter. Engedélyezze a hálózati illesztő – az IP-továbbítást [az hálózati nic frissítés](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

A virtuális Gépen belül az operációs rendszer és az alkalmazások, a virtuális gépen belül is kell tudni továbbítja a hálózati forgalmat. A virtuális gép operációs rendszerben az IP-továbbítás engedélyezése [az virtuálisgép-bővítmény készlet](/cli/azure/vm/extension#az_vm_extension_set):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
A parancs is tarthat egy percre hajtható végre.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépek a virtuális hálózat, így ellenőrizheti, hogy a forgalom a *nyilvános* alhálózati annak biztosítására, hogy a *titkos* keresztül az NVA egy későbbi lépésben. 

A virtuális gép létrehozása a *nyilvános* alhálózat [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). A `--no-wait` paraméter lehetővé teszi, hogy az Azure a parancs végrehajtása a háttérben, így a következő paranccsal is. Ez a cikk egyszerűsítésére, a jelszó szolgál. Kulcsok általában használják az éles környezetekben. Ha a kulcsok használatához SSH-ügynöktovábbítást is konfigurálnia kell. További információkért az SSH-ügyfél a dokumentációban. Cserélje le `<replace-with-your-password>` az alábbi parancs a jelszóval.

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

A virtuális gép létrehozása a *titkos* alhálózat.

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

A virtuális gép létrehozásához néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure parancssori felület információkat jeleníti meg az alábbi példához hasonló: 

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
Vegye figyelembe a **publicIpAddress**. Ez a cím a virtuális gép egy későbbi lépésben az internetről való eléréséhez használt.

## <a name="route-traffic-through-an-nva"></a>NVA-útvonal forgalmát

Az SSH-munkamenetet létrehozni, az alábbi parancs segítségével a *myVmPrivate* virtuális gép. Cserélje le *<publicIpAddress>* a virtuális gép a nyilvános IP-címmel. A fenti példában az IP-cím van *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Amikor a rendszer kéri a jelszót, írja be a jelszót, a kiválasztott [virtuális gépek létrehozása](#create-virtual-machines).

A következő paranccsal telepíthető traceroute a *myVmPrivate* VM:

```bash 
sudo apt-get install traceroute
```

Ellenőrizze a hálózati forgalmat és az Útválasztás a következő parancs segítségével a *myVmPublic* virtuális gép a *myVmPrivate* virtuális gép.

```bash
traceroute myVmPublic
```

A rendszer a választ az alábbi példához hasonló:

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Láthatja, hogy továbbítódik közvetlenül a *myVmPrivate* virtuális Gépet a *myVmPublic* virtuális gép. Azure alapértelmezett útvonalak, közvetlen alhálózatba irányíthatja a forgalmat. 

A következő parancs segítségével SSH-kapcsolatot a *myVmPublic* virtuális gép a *myVmPrivate* VM:

```bash 
ssh azureuser@myVmPublic
```

A következő paranccsal telepíthető traceroute a *myVmPublic* VM:

```bash 
sudo apt-get install traceroute
```

Ellenőrizze a hálózati forgalmat és az Útválasztás a következő parancs segítségével a *myVmPrivate* virtuális gép a *myVmPublic* virtuális gép.

```bash
traceroute myVmPrivate
```

A rendszer a választ az alábbi példához hasonló:

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Láthatja, hogy az első ugrásnál 10.0.2.4, amely az NVA magánhálózati IP-cím-e. A kétugrásos 10.0.1.4, a magánhálózati IP-címe a *myVmPrivate* virtuális gép. Az útvonal hozzá a *myRouteTablePublic* útvonaltábla és társított a *nyilvános* alhálózati, hogy a forgalmat az NVA keresztül, és nem közvetlenül az Azure a *saját* alhálózat.

Zárja be az SSH-munkamenet, mind a *myVmPublic* és *myVmPrivate* virtuális gépeket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [az csoport törlése](/cli/azure/group#az_group_delete) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy útválasztási táblázatot, és az alhálózathoz társított. Létrehozott egy egyszerű NVA portleképezéseit titkos alhálózathoz nyilvános alhálózatból származó forgalmat. Számos előre konfigurált hálózati funkciókat, például a tűzfal és a WAN-optimalizálást végző NVAs telepítése a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Az útvonaltáblák üzemi használatra való telepítése előtt javasoljuk, hogy alaposan feltérképezése [az Azure útválasztási](virtual-networks-udr-overview.md), [kezelése az útvonaltáblák](manage-route-table.md), és [Azure korlátozza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

A virtuális hálózaton belül számos Azure-erőforrások telepítése során egyes Azure PaaS szolgáltatások erőforrás nem telepíthető virtuális hálózatba. Továbbra is korlátozzuk néhány Azure PaaS-szolgáltatást csak a virtuális hálózati alhálózat forgalom erőforrásaihoz, ha. A következő oktatóanyag áttekintésével megismerheti, hogyan Azure PaaS erőforrásokhoz való hálózati hozzáférés korlátozása továbblépés.

> [!div class="nextstepaction"]
> [Hálózati hozzáférés korlátozása PaaS erőforrások](tutorial-restrict-network-access-to-resources-cli.md)
