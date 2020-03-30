---
title: Belső DNS használata a virtuális gép névfeloldásához az Azure CLI-vel
description: Virtuális hálózati csatolókártyák létrehozása és belső DNS használata a virtuális gép névfeloldásához az Azure-ban az Azure CLI-vel
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: acfdfd4edf90b90998a913fa0c6479bedf0028b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034745"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Hozzon létre virtuális hálózati csatolókártyákat, és használja a belső DNS-t a virtuális gép névfeloldásához az Azure-ban

Ez a cikk bemutatja, hogyan állíthat be statikus belső DNS-neveket a Linux-alapú virtuális gépek virtuális hálózati csatolókártyák (vNics) és dns-címke nevek et az Azure CLI használatával. A statikus DNS-nevek olyan állandó infrastruktúra-szolgáltatásokhoz használatosak, mint például a dokumentumhoz használt Jenkins buildkiszolgáló vagy a Git-kiszolgáló.

Követelmények:

* [egy Azure-fiók](https://azure.microsoft.com/pricing/free-trial/)
* [SSH nyilvános- és titkoskulcs-fájlok](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Gyors parancsok
Ha gyorsan el kell végeznie a feladatot, a következő szakasz részletezi a szükséges parancsokat. Az egyes lépésekkel kapcsolatos részletesebb információk és kontextusok a dokumentum többi részében találhatók, [itt kezdődően.](#detailed-walkthrough) A lépések végrehajtásához a legújabb [Azure CLI-t](/cli/azure/install-az-cli2) kell telepítenie, és be kell jelentkeznie egy Azure-fiókba [az a bejelentkezéssel.](/cli/azure/reference-index)

Előkövetelmények: Erőforráscsoport, virtuális hálózat és alhálózat, Hálózati biztonsági csoport SSH bejövő.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Virtuális hálózati kártya létrehozása statikus belső DNS-névvel
Hozza létre a vNic [az hálózati nic létrehozása](/cli/azure/network/nic). A `--internal-dns-name` CLI-jelző a DNS-címke beállítására való, amely a virtuális hálózati csatolókártya (vNic) statikus DNS-nevét adja meg. A következő példa létrehoz `myNic`egy vNic nevű `myVnet` , csatlakozik a virtuális hálózathoz, és létrehoz egy belső DNS-névrekordot nevű `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Virtuális gép üzembe helyezése és a virtuális hálózati adapter csatlakoztatása
Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. A `--nics` jelző a virtuális nic a virtuális gép a központi telepítés során az Azure-hoz. A következő példa létrehoz `myVM` egy Azure felügyelt lemezekkel elnevezett `myNic` virtuális gép, és csatolja a vNic nevű az előző lépésben:

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

A teljes folyamatos integráció és a folyamatos üzembe helyezési (CiCd) infrastruktúra az Azure-ban megköveteli, hogy bizonyos kiszolgálók statikus vagy hosszú élettartamú kiszolgálók. Javasoljuk, hogy az Azure-eszközök, például a virtuális hálózatok és a hálózati biztonsági csoportok statikus és hosszú élettartamú erőforrások, amelyek ritkán üzembe helyezett. A virtuális hálózat üzembe helyezése után az új telepítések újra felhasználhatók anélkül, hogy az infrastruktúra bármilyen hátrányos anamtalánk lennének. Később hozzáadhat egy Git-tárház-kiszolgálót, vagy egy Jenkins-automatizálási kiszolgáló biztosítja a CiCd-t erre a virtuális hálózatra a fejlesztési vagy tesztelési környezetekben.  

A belső DNS-nevek csak egy Azure virtuális hálózaton belül feloldhatók. Mivel a DNS-nevek belsőek, nem oldhatók fel a külső internettel, ami további biztonságot nyújt az infrastruktúrának.

A következő példákban cserélje le a példaparaméterneveket a saját értékeire. A példaparaméternevei a következők: `myResourceGroup`a , `myNic`és `myVM`a .

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása
Először hozza létre az erőforráscsoportot az [a csoport létrehozásával.](/cli/azure/group) A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

A következő lépés egy virtuális hálózat létrehozása a virtuális gépek indításához. A virtuális hálózat egy alhálózatot tartalmaz ehhez a forgatókönyvhöz. Az Azure virtuális hálózatokkal kapcsolatos további információkért [lásd: Virtuális hálózat létrehozása.](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) 

Hozza létre a virtuális hálózatot az [hálózati virtuális hálózat létrehozása.](/cli/azure/network/vnet) A következő példa létrehoz `myVnet` egy virtuális `mySubnet`hálózat ot és alhálózatot, melynek neve:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>A hálózati biztonsági csoport létrehozása
Az Azure network security groups egyenértékű a hálózati rétegen lévő tűzfallal. A hálózati biztonsági csoportokról további információt az [NSG-k létrehozása az Azure CLI-ben](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című témakörben talál. 

Hozza létre a hálózati biztonsági csoportot az [az network nsg create](/cli/azure/network/nsg)segítségével. A következő példa létrehoz egy `myNetworkSecurityGroup`hálózati biztonsági csoportot, amelynek neve:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Bejövő szabály hozzáadása az SSH engedélyezéséhez
Bejövő szabály hozzáadása a hálózati biztonsági csoporthoz az [hálózati nsg szabállyal létrehozva.](/cli/azure/network/nsg/rule) A következő példa létrehoz `myRuleAllowSSH`egy nevű szabályt:

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Az alhálózat társítása a hálózati biztonsági csoporthoz
Az alhálózat hálózati biztonsági csoporthoz való társításához használja [az az hálózati virtuális hálózat alhálózati frissítését.](/cli/azure/network/vnet/subnet) A következő példa az `mySubnet` alhálózat nevét társítja a hálózati biztonsági csoport nevéhez: `myNetworkSecurityGroup`

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>A virtuális hálózati csatolókártya és a statikus DNS-nevek létrehozása
Az Azure nagyon rugalmas, de a virtuális gép névfeloldásához DNS-nevek használatához létre kell hoznia a dns-címkét tartalmazó virtuális hálózati csatolókártyákat (vNics). a vNics fontos, mivel újra felhasználhatja őket, ha az infrastruktúra életciklusa során különböző virtuális gépekhez csatlakoztatja őket. Ez a megközelítés a virtuális nnic statikus erőforrásként marad, míg a virtuális gépek ideiglenesek lehetnek. A virtuális hálózaton a DNS-címkézés használatával egyszerű névfeloldást tudunk engedélyezni a virtuális hálózat más virtuális gépeitől. A feloldható nevek használata lehetővé teszi, hogy `Jenkins` más virtuális gépek `gitrepo`is hozzáférjenek az automatizálási kiszolgálóhoz a DNS-név vagy a Git-kiszolgáló alapján.  

Hozza létre a vNic [az hálózati nic létrehozása](/cli/azure/network/nic). A következő példa létrehoz `myNic`egy vNic nevű `myVnet` , `myVnet`összeköti azt a virtuális hálózat `jenkins`neve , és létrehoz egy belső DNS-névrekord nevű :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>A virtuális gép üzembe helyezése a virtuális hálózati infrastruktúrába
Most már van egy virtuális hálózat és alhálózat, a hálózati biztonsági csoport működik, mint egy tűzfal, hogy megvédje az alhálózat blokkolja az összes bejövő forgalmat, kivéve a 22-es port sSH, és a vNic. Most már üzembe helyezhet egy virtuális gép a meglévő hálózati infrastruktúra belül.

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. A következő példa létrehoz `myVM` egy Azure felügyelt lemezekkel elnevezett `myNic` virtuális gép, és csatolja a vNic nevű az előző lépésben:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

A CLI-jelzők használatával hívja ki a meglévő erőforrásokat, utasítjuk az Azure-t, hogy a virtuális gép a meglévő hálózaton belül. Megismételni, ha egy virtuális hálózat és alhálózat üzembe helyezése után, azok statikus vagy állandó erőforrások az Azure-régióban maradhatnak.  

## <a name="next-steps"></a>További lépések
* [Saját egyéni környezet létrehozása Linux virtuális gép számára közvetlenül Azure parancssori felület parancsait használva](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linuxos virtuális gép létrehozása az Azure-ban sablonok használatával](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
