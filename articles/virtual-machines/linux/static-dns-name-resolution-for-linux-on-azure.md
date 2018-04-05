---
title: Belső DNS használata az Azure CLI 2.0-s VM névfeloldás |} Microsoft Docs
description: Hozzon létre a virtuális hálózati adapterek, és a belső DNS használata az Azure CLI 2.0 Azure VM-névfeloldás
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
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
ms.openlocfilehash: a17a82aa9bc31997d52aa41f387f95d7a8ae4ae2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Virtuális hálózati adapterek létrehozása és használata a belső DNS Azure VM-névfeloldás
Ez a cikk bemutatja, hogyan állítsa be a statikus belső DNS-nevek Linux virtuális gépek virtuális hálózati kártyák (vNics) és a DNS-címke nevek használata az Azure CLI 2.0. Az [Azure CLI 1.0-s](static-dns-name-resolution-for-linux-on-azure-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) verziójával is elvégezheti ezeket a lépéseket. Statikus DNS-nevek használhatók állandó infrastruktúra-szolgáltatásokat, mint egy Jenkins build, ez a dokumentum használt, vagy egy Git kiszolgálóhoz.

Követelmények:

* [egy Azure-fiók](https://azure.microsoft.com/pricing/free-trial/)
* [SSH nyilvános- és titkoskulcs-fájlok](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Gyors parancsok
Ha gyorsan feladatnak van szüksége, az alábbi szakasz részletesen a szükséges parancsokat. Részletes információkat és a környezetben az egyes lépések a dokumentum többi részén található [itt indítása](#detailed-walkthrough). A következő lépésekkel lesz szüksége a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

Előfeltételek: Erőforráscsoport, virtuális hálózati és alhálózati, hálózati biztonsági csoport SSH bejövő.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Hozzon létre egy virtuális hálózati adaptert statikus belső DNS-név
Hozzon létre a virtuális hálózati rendelkező [az hálózat összevont hálózati létrehozása](/cli/azure/network/nic#az_network_nic_create). A `--internal-dns-name` CLI jelző van szeretné beállítani a DNS-címke, amely a virtuális hálózati kártya (vNic) statikus DNS-nevét. Az alábbi példakód létrehozza a virtuális hálózati nevű `myNic`, úgy, hogy csatlakozik a `myVnet` virtuális hálózaton, és létrehoz egy belső DNS-név rekordot nevű `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>A virtuális gép üzembe helyezése, és csatlakozzon a virtuális hálózati
Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. A `--nics` jelző kapcsolódik a virtuális hálózati a virtuális Gépet az Azure-bA a telepítés során. Az alábbi példakód létrehozza a virtuális gépek nevű `myVM` Azure felügyelt lemezek és a virtuális hálózati nevű rendeli `myNic` az előző lépésben:

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

A teljes folyamatos integrációt és a folyamatos üzembe helyezés (CiCd) Azure infrastruktúra megköveteli az egyes kiszolgálók statikus vagy hosszú élettartamú kiszolgálók. Ajánlott, például a virtuális hálózatok és a hálózati biztonsági csoportok Azure eszközök statikus, és hosszabb élettartamú ritkán rendszerbe telepítendő erőforrásokat. Ha egy virtuális hálózathoz van telepítve, azt bármely kedvezőtlen hatással van az infrastruktúra nélkül új központi telepítések során felhasználhatók. Később hozzáadhat egy Git-tárház kiszolgáló, vagy egy Jenkins fürtautomatizálási kiszolgáló CiCd továbbítja a fejlesztési vagy tesztelési környezetben a virtuális hálózaton.  

Belső DNS-nevek csak feloldható egy Azure virtuális hálózaton belül. Mivel a DNS-nevek belső, azok nem oldható fel, az internetet, az infrastruktúra egyéb biztonsági lépések.

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők `myResourceGroup`, `myNic`, és `myVM`.

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása
Először hozza létre az erőforráscsoport [az csoport létrehozása](/cli/azure/group#az_group_create). Az alábbi példa létrehoz egy erőforráscsoportot `myResourceGroup` a a `westus` helye:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

A következő lépésre elindíthatja a virtuális gépeket a virtuális hálózat létrehozásához. A virtuális hálózat külön alhálózatokon üzemeltetni az Ez az útmutató tartalmazza. Egy Azure virtuális hálózatot további információkért lásd: [hozzon létre egy virtuális hálózatot](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

A virtuális hálózat létrehozása [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create). Az alábbi példa létrehoz egy virtuális hálózatot nevű `myVnet` és nevű alhálózat `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>A hálózati biztonsági csoport létrehozása
Azure hálózati biztonsági csoportokat a hálózati rétegben tűzfal egyenértékűek. Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [NSG-k létrehozása az Azure parancssori felület a](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

A hálózati biztonsági csoport létrehozása [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create). Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Egy bejövő szabályt, amely engedélyezi az SSH hozzáadása
A hálózati biztonsági csoport a bejövő szabály felvétele [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create). Az alábbi példa létrehoz egy nevű szabályt `myRuleAllowSSH`:

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Az alhálózatot a hálózati biztonsági csoporthoz társítandó
Az alhálózatot a hálózati biztonsági csoporthoz társítandó, használja a [az hálózati vnet alhálózati frissítés](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Az alábbi példa társítja az alhálózati név `mySubnet` együtt a hálózati biztonsági csoport nevű `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>A virtuális hálózati kártya és a statikus DNS-nevek létrehozása
Azure a rendkívül rugalmas, de szeretné használni a Virtuálisgép-név feloldása DNS-neveit, hozzon létre a virtuális hálózati kártyák (vNics), amely tartalmazza a DNS-címke kell. vNics fontosak, újra felhasználhatja azokat keresztül az infrastruktúra-életciklus különböző virtuális gépek csatlakoztatja őket. Ezt a módszert használja a virtuális hálózati tartja a statikus erőforrásként a virtuális gépek ideiglenes lehetnek. A virtuális címkézés DNS használatával azt is más virtuális gépek virtuális egyszerű névfeloldás engedélyezése. Feloldható nevek használata lehetővé teszi, hogy a DNS-neve alapján az automation-kiszolgáló eléréséhez más virtuális gépek `Jenkins` vagy a Git kiszolgálóra `gitrepo`.  

Hozzon létre a virtuális hálózati rendelkező [az hálózat összevont hálózati létrehozása](/cli/azure/network/nic#az_network_nic_create). Az alábbi példakód létrehozza a virtuális hálózati nevű `myNic`, úgy, hogy csatlakozik a `myVnet` nevű virtuális hálózati `myVnet`, és létrehoz egy belső DNS-név rekordot nevű `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>A virtuális gép üzembe helyezés a virtuális hálózati infrastruktúra
Most már rendelkezik egy virtuális hálózat és a hálózati biztonsági csoport működött a tűzfal védi az alhálózati 22-es port kivételével minden bejövő forgalom blokkolása az SSH és a virtuális hálózati alhálózat. Most már telepítheti a virtuális gépek a meglévő hálózati infrastruktúra belül.

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példakód létrehozza a virtuális gépek nevű `myVM` Azure felügyelt lemezek és a virtuális hálózati nevű rendeli `myNic` az előző lépésben:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

A parancssori felület jelzők használatával hívásához a meglévő erőforrásokat, azt kérje meg a virtuális Gépet a meglévő hálózaton telepítendő Azure. Megújítja, miután egy VNet és alhálózat van telepítve, hogy azok maradhatnak, statikus vagy állandó erőforrásként belül az Azure-régiót.  

## <a name="next-steps"></a>További lépések
* [Saját egyéni környezet létrehozása Linux virtuális gép számára közvetlenül Azure parancssori felület parancsait használva](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux virtuális gép létrehozása az Azure-ban sablonok használatával](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
