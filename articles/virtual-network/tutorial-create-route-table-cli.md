---
title: Útvonalhálózati forgalom - Azure CLI | Microsoft dokumentumok
description: Ebben a cikkben megtudhatja, hogyan irányíthat hálózati forgalmat egy útvonaltábla az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 5fa94b93e081ab6334c39b848068f50682f5f1f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235054"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Hálózati forgalom útvonal-forgalmat az Azure CLI használatával útvonaltáblával

Egy adott virtuális hálózaton belül az Azure alapértelmezés szerint automatikusan elosztja a forgalmat az összes alhálózat között. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak létrehozása akkor lehet hasznos, hálózati virtuális berendezésen (NVA) keresztül kívánja irányítani az alhálózatok közötti forgalmat. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Útválasztási táblázat létrehozása
* Útvonal létrehozása
* Több alhálózattal rendelkező virtuális hálózat létrehozása
* Útválasztási táblázat társítása alhálózattal
* Forgalmat irányító hálózati virtuális berendezés létrehozása
* Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
* Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Úttábla létrehozása előtt hozzon létre egy erőforráscsoportot [az csoport létrehozásával](/cli/azure/group) a cikkben létrehozott összes erőforráshoz. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Útvonaltábla létrehozása [az hálózati útvonaltábla létrehozása esetén.](/cli/azure/network/route-table#az-network-route-table-create) A következő példa létrehoz egy *myRouteTablePublic*nevű útvonaltáblát. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Útvonal létrehozása

Hozzon létre egy útvonalat az [útvonaltáblában az az hálózati útvonaltábla-útvonal létrehozása esetén.](/cli/azure/network/route-table/route#az-network-route-table-route-create) 

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

Mielőtt egy útvonaltáblát egy alhálózathoz társíthatna, létre kell hoznia egy virtuális hálózatot és alhálózatot. Hozzon létre egy virtuális hálózatot egy alhálózattal [az az hálózati virtuális hálózat létrehozása .](/cli/azure/network/vnet)

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Hozzon létre két további alhálózatot [az az hálózati virtuálishálózat-alhálózat](/cli/azure/network/vnet/subnet)létrehozásával.

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

Társítsa a *myRouteTablePublic* útvonaltáblát a *nyilvános* alhálózathoz az [az hálózati virtuális hálózat alhálózati frissítésével.](/cli/azure/network/vnet/subnet)

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>NVA létrehozása

Az NVA egy olyan virtuális gép, amely hálózati funkciót tölt be, például útválasztóként, tűzfalként vagy WAN-optimalizálóként működik.

Hozzon létre egy NVA-t a *DMZ* alhálózatban [az vm create](/cli/azure/vm)segítségével. Virtuális gép létrehozásakor az Azure alapértelmezés szerint létrehoz és hozzárendel egy nyilvános IP-címet a virtuális géphez. A `--public-ip-address ""` paraméter arra utasítja az Azure-t, hogy ne hozzon létre és rendeljen hozzá egy nyilvános IP-címet a virtuális géphez, mivel a virtuális géphez nem kell csatlakoznia az internetről. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a következő lépéssel, amíg az Azure befejezi a virtuális gép létrehozását, és a virtuális gép kimenetét adja vissza. 

Ahhoz, hogy egy hálózati adapter továbbíthassa a neki küldött, de nem a saját IP-címére címzett forgalmat, engedélyeznie kell az IP-továbbítást a hálózati adapteren. Engedélyezze az IP-továbbítást a hálózati adapterhez [az az hálózati nic frissítéssel.](/cli/azure/network/nic)

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

A virtuális gépen, az operációs rendszeren, vagy egy a virtuális gépen futó alkalmazáson belül szintén működnie kell a hálózati forgalom továbbításának. Engedélyezze az IP-továbbítást a virtuális gép operációs rendszerén belül [az vm bővítménykészlettel:](/cli/azure/vm/extension)

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

A parancs végrehajtása akár egy percet is igénybe vehet.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózatban, így ellenőrizheti, hogy a *nyilvános* alhálózatból érkező forgalom egy későbbi lépésben a *magánalhálózathoz* van irányítva az NVA-n keresztül. 

Hozzon létre egy virtuális gép a *nyilvános* alhálózatban [az vm létrehozása.](/cli/azure/vm) A `--no-wait` paraméter lehetővé teszi, hogy az Azure a háttérben végrehajtsa a parancsot, így folytathatja a következő parancsot. A cikk egyszerűsítéséhez jelszót használ. A kulcsokat általában éles környezetekben használják. Ha kulcsokat használ, konfigurálnia kell az SSH ügynök továbbítását is. További információt az SSH-ügyfél dokumentációjában talál. Cserélje `<replace-with-your-password>` le a következő parancsot egy ön által választott jelszóra.

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

Hozzon létre egy virtuális gép a *privát* alhálózatban.

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

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI a következő példához hasonló információkat jelenít meg: 

```output
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

Jegyezze fel a **publicIpAddress** értékét. Ez a cím egy későbbi lépésben az internetről való eléréséhez használható.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

A következő paranccsal ssh-munkamenetet hozhat létre a *myVmPrivate* virtuális géppel. Cserélje le * \<a nyilvánosIpAddress>* a virtuális gép nyilvános IP-címére. A fenti példában az IP-cím *13.90.242.231*.

```bash
ssh azureuser@<publicIpAddress>
```

Amikor jelszót kér, írja be a [virtuális gépek létrehozása mezőbe](#create-virtual-machines)a kiválasztott jelszót.

A következő paranccsal telepítheti a nyomkövetési útvonalat a *myVmPrivate* virtuális gépre:

```bash
sudo apt-get install traceroute
```

A következő paranccsal tesztelje a *myVmPublic* virtuális gép hálózati forgalmát a *myVmPrivate* virtuális gépről.

```bash
traceroute myVmPublic
```

A válasz a következő példához hasonló:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Láthatja, hogy a rendszer a *myVmPrivate* virtuális gépről közvetlenül a *myVmPublic* virtuális gépre irányítja a forgalmat. Az Azure alapértelmezett útvonalai, a forgalom közvetlen irányítása az alhálózatok között. 

Használja a következő parancsot az SSH-hoz a *myVmPublic* virtuális géphez a *myVmPrivate* virtuális gépről:

```bash
ssh azureuser@myVmPublic
```

A következő paranccsal telepítheti a nyomkövetési útvonalat a *myVmPublic* virtuális gépre:

```bash
sudo apt-get install traceroute
```

A következő paranccsal tesztelje a *myVmPrivate* virtuális gép hálózati forgalmát a *myVmPublic* virtuális gépről.

```bash
traceroute myVmPrivate
```

A válasz a következő példához hasonló:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Láthatja, hogy az első ugrás a 10.0.2.4 cím, amely az NVA magánhálózati IP-címe. A második ugrás a 10.0.1.4 cím – ez a *myVmPrivate* virtuális gép magánhálózati IP-címe. A*myRouteTablePublic* útválasztási táblázathoz hozzáadott és a *Magánjellegű* alhálózathoz rendelt útvonal miatt az Azure az NVA-n keresztül továbbította a forgalmat ahelyett, hogy közvetlenül a *Privát* alhálózatra továbbította volna.

Zárja be az SSH-munkameneteket a *myVmPublic* és a *myVmPrivate* virtuális gépekhez is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az csoport törlésével](/cli/azure/group) távolítsa el az erőforráscsoportot és az összes benne lévő erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy útvonaltáblát, és egy alhálózathoz társította. Létrehozott egy egyszerű NVA-t, amely átirányította a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. Az [Azure Marketplace-ről](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) számos előre konfigurált NVA-t helyezhet üzembe, amelyek olyan hálózati funkciókat végeznek, mint például a tűzfal és a WAN-optimalizálás. További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Egy virtuális hálózaton belül több Azure-erőforrást helyezhet üzembe, azonban egyes Azure PaaS-szolgáltatások erőforrásai nem helyezhetők üzembe virtuális hálózatban. Ennek ellenére korlátozhatja az egyes Azure PaaS-szolgáltatások erőforrásaihoz való hozzáférést, hogy csak egyetlen virtuális hálózati alhálózatról legyenek elérhetők. Ebből, hogy miként, olvassa [el a Hálózati hozzáférés korlátozása a PaaS-erőforrásokhoz](tutorial-restrict-network-access-to-resources-cli.md).
