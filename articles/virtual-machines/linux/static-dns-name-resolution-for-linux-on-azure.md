---
title: Belső DNS használata virtuális gépek névfeloldásához az Azure CLI-vel |} A Microsoft Docs
description: Virtuális hálózati adapterek létrehozása és a belső DNS használata virtuális gépek névfeloldásához az Azure-ban az Azure CLI-vel
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
ms.openlocfilehash: 5e893d597c2193676cb350fc80d7baa694ad6fd1
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55734122"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Virtuális hálózati adapterek létrehozása és a belső DNS használata Azure-beli virtuális gépek névfeloldásához

Ez a cikk bemutatja, hogyan állíthatja be a virtuális hálózati adapterrel (Vnic) használata Linux rendszerű virtuális gépek statikus belső DNS-nevek és címkenevek DNS az Azure CLI-vel. Az állandó infrastruktúra-szolgáltatások, például egy Jenkins létrehozási kiszolgálóra, amely ebben a dokumentumban használt, vagy egy Git-kiszolgáló statikus DNS-nevek használhatók.

Követelmények:

* [egy Azure-fiók](https://azure.microsoft.com/pricing/free-trial/)
* [SSH nyilvános- és titkoskulcs-fájlok](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Gyors parancsok
Ha szeretne gyorsan elvégezni a feladatot, a következő szakasz ismerteti a szükséges parancsokat. Részletes információkat és a környezet a dokumentum többi részén találja lépéseinek [itt indítása](#detailed-walkthrough). Ezeket a lépéseket a legújabb kell [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index).

Előfeltételek: A bejövő erőforráscsoportot, virtuális hálózatot és alhálózatot, a hálózati biztonsági csoport az ssh-val.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Hozzon létre egy virtuális hálózati adaptert statikus belső DNS-név
Az a virtuális hálózati adapter létrehozása [az network nic létrehozása](/cli/azure/network/nic). A `--internal-dns-name` Parancssorifelület-jelző van a DNS-címke, amely biztosítja a virtuális hálózati kártya (vNic) statikus DNS-nevét állítja. Az alábbi példa létrehoz egy virtuális hálózati adapter nevű `myNic`, csatlakoztatja a a `myVnet` a virtuális hálózathoz, és létrehoz egy belső DNS-név rekord nevű `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Virtuális gép üzembe helyezése, és csatlakozzon a virtuális hálózati adapter
Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. A `--nics` jelző csatlakozik a virtuális hálózati adapter a virtuális gép az Azure-ban az üzembe helyezés során. A következő példában létrehozunk egy nevű virtuális Gépet `myVM` az Azure Managed Disks és a virtuális hálózati adapter nevű rendeli `myNic` az előző lépésből:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Részletes bemutató

Teljes folyamatos integráció és készregyártás (CI/CD) Azure-beli infrastruktúra egyes kiszolgálókat statikus vagy hosszú élettartamú kiszolgálóknak lenniük igényel. Javasoljuk, hogy az Azure-eszközeivel, mint a virtuális hálózatok és a hálózati biztonsági csoportok statikus, és hosszabb élettartamú ritkán telepített erőforrásokhoz. Virtuális hálózat üzembe helyezése után azt bármely kedvezőtlen hatással van az infrastruktúra nélkül új központi telepítések során felhasználhatók. Később hozzáadhat egy Git-tárház kiszolgáló, vagy egy Jenkins-automatizáló kiszolgáló CI/CD kínál a fejlesztési és tesztelési környezetek a virtuális hálózaton.  

Belső DNS-nevük, csak egy Azure virtuális hálózaton belül feloldható. Mivel a DNS-nevek belső, azok nem oldható fel a külső internet, az infrastruktúra egyéb biztonsági lépések.

A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa a paraméter nevek a következők `myResourceGroup`, `myNic`, és `myVM`.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása
Először hozza létre az erőforráscsoportot [az csoport létrehozása](/cli/azure/group). A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

A következő lépés, hogy alakíthat ki virtuális hálózatot, a virtuális gépek elindítására. A virtuális hálózat egy alhálózat ebben a bemutatóban tartalmazza. Azure virtuális hálózataiban működő további információkért lásd: [hozzon létre egy virtuális hálózatot](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Hozzon létre a virtuális hálózatba a [az network vnet létrehozása](/cli/azure/network/vnet). A következő példában létrehozunk egy nevű virtuális hálózatot `myVnet` és nevű alhálózatot `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>A hálózati biztonsági csoport létrehozása
Az Azure hálózati biztonsági csoportok a hálózati rétegen tűzfal egyenértékűek. Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [NSG-k létrehozása az Azure CLI-ben](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

A hálózati biztonsági csoport létrehozása [az network nsg létrehozása](/cli/azure/network/nsg). A következő példában létrehozunk egy hálózati biztonsági csoport nevű `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Adjon hozzá egy bejövő szabályt, amely engedélyezi az SSH
Adjon hozzá egy bejövő szabályt a hálózati biztonsági csoport [az network nsg-szabály létrehozása](/cli/azure/network/nsg/rule). A következő példában létrehozunk egy nevű szabályt `myRuleAllowSSH`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Az alhálózat társítása a hálózati biztonsági csoport
Az alhálózat társítása a hálózati biztonsági csoport, használja a [az hálózati virtuális hálózat alhálózati frissítés](/cli/azure/network/vnet/subnet). Az alábbi példa hozzárendeli az alhálózat neve `mySubnet` nevű hálózati biztonsági csoporttal `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Hozza létre a virtuális hálózati kártya és a statikus DNS-nevek
Az Azure rendkívül rugalmas, de szeretné használni a virtuális gép névfeloldásához DNS-neveit, virtuális hálózati adapterrel (Vnic), amely tartalmazza a DNS-címke létrehozásához szüksége. Vnic olyan fontos, mert a kihasználásával a különböző virtuális gépek infrastruktúra életciklusa felhasználhatja őket. Ez a megközelítés biztosítja, hogy a virtuális hálózati adapter statikus erőforrásként közben a virtuális gépek ideiglenes lehet. A virtuális hálózati adapter a címkézés DNS használatával tudjuk más virtuális gépek által a virtuális hálózat egyszerű névfeloldás engedélyezése. Feloldható nevek használata lehetővé teszi, hogy az automation-kiszolgáló eléréséhez a DNS-név szerint más virtuális gépek `Jenkins` vagy a Git-kiszolgálón, a `gitrepo`.  

Az a virtuális hálózati adapter létrehozása [az network nic létrehozása](/cli/azure/network/nic). Az alábbi példa létrehoz egy virtuális hálózati adapter nevű `myNic`, csatlakozik, hogy a `myVnet` nevű virtuális hálózatot `myVnet`, és létrehoz egy belső DNS-név rekord nevű `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>A virtuális gép virtuális hálózati infrastruktúra üzembe helyezése
Most már rendelkezik egy virtuális hálózatot és alhálózatot, a hálózati biztonsági csoport tűzfalként védi meg az alhálózat blokkolja a 22-es port kivételével az összes bejövő forgalmat az SSH és a egy virtuális hálózati adaptert. Most már telepítheti a meglévő hálózati infrastruktúrában lévő virtuális Gépet.

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. A következő példában létrehozunk egy nevű virtuális Gépet `myVM` az Azure Managed Disks és a virtuális hálózati adapter nevű rendeli `myNic` az előző lépésből:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

A parancssori jelzők használatával hívja meg a meglévő erőforrások, hogy kérje meg a meglévő hálózaton belül a virtuális gép üzembe helyezéséhez Azure. Kifejezni, miután egy virtuális hálózatot és alhálózatot van telepítve, azokat is hagyható statikus vagy állandó erőforrásként az Azure-régión belül.  

## <a name="next-steps"></a>További lépések
* [Saját egyéni környezet létrehozása Linux virtuális gép számára közvetlenül Azure parancssori felület parancsait használva](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux rendszerű virtuális gép létrehozása az Azure-ban sablonok használatával](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
