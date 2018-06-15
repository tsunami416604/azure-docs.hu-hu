---
title: Belső DNS-sel névfeloldás VM az Azure-on |} Microsoft Docs
description: Belső DNS-sel Azure VM névfeloldáshoz.
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
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
ms.openlocfilehash: 73ecbb5bfcc99eb4507cac24b0539a04c3d8f521
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30917278"
---
# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Névfeloldás VM Azure belső DNS-sel

Ez a cikk bemutatja, hogyan állítsa be a statikus belső DNS-nevek Linux virtuális gépek virtuális hálózati kártyák (VNic) és a DNS-címke nevek használatával. Statikus DNS-nevek használhatók állandó infrastruktúra-szolgáltatásokat, mint egy Jenkins build, ez a dokumentum használt, vagy egy Git kiszolgálóhoz.

Követelmények:

* [egy Azure-fiók](https://azure.microsoft.com/pricing/free-trial/)
* [SSH nyilvános- és titkoskulcs-fájlok](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#quick-commands) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.


## <a name="quick-commands"></a>Gyors parancsok

Ha gyorsan feladatnak van szüksége, az alábbi szakasz részletesen a szükséges parancsokat. Részletes információkat és a környezetben az egyes lépések a dokumentum többi részén található [itt indítása](#detailed-walkthrough).  

Előfeltételek: A SSH erőforráscsoportot, a virtuális hálózat, NSG bejövő, alhálózati.

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>Hozzon létre egy virtuális hálózati statikus belső DNS-név

A `-r` cli jelző van szeretné beállítani a DNS-címke, amely a virtuális hálózati statikus DNS-nevét.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>A virtuális gép üzembe helyezés a VNet NSG-t, és csatlakozzon a virtuális hálózati

A `-N` az új virtuális Gépet az Azure-bA a telepítés során a virtuális hálózati csatlakozik.

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>Részletes bemutató

A teljes folyamatos integrációt és a folyamatos üzembe helyezés (CiCd) Azure infrastruktúra megköveteli az egyes kiszolgálók statikus vagy hosszú élettartamú kiszolgálók.  Javasoljuk, hogy a virtuális hálózatokon (Vnetek) és a hálózati biztonsági csoportokkal (NSG-k), például az Azure eszközök statikusnak kell lennie, és hosszabb élettartamú ritkán rendszerbe telepítendő erőforrásokat.  Miután a virtuális hálózaton van telepítve, azt bármely kedvezőtlen hatással van az infrastruktúra nélkül új központi telepítések során felhasználhatók.  A fejlesztési és tesztelési környezetek hozzáadása a statikus hálózathoz egy Git-tárház kiszolgáló és egy Jenkins fürtautomatizálási kiszolgáló CiCd továbbítja.  

Belső DNS-nevek csak feloldható egy Azure virtuális hálózaton belül.  Mivel a DNS-nevek belső, azok nem oldható fel, az internetet, az infrastruktúra egyéb biztonsági lépések.

_Olyan cserélje le a saját elnevezési._

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Erőforráscsoport minden ebben a bemutatóban létrehozhatunk rendszerezéséhez van szükség.  További információ az Azure erőforráscsoportokkal: [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>A virtuális hálózat létrehozása

Az első lépés egy VNet és indítsa el a virtuális gépek történő létrehozásához.  A virtuális hálózat külön alhálózatokon üzemeltetni az Ez az útmutató tartalmazza.  Az Azure Vnetekhez további információkért lásd: [virtuális hálózat létrehozása](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Az NSG-t létrehozni

Az alhálózat épül mögött egy meglévő hálózati biztonsági csoporthoz, azt az alhálózati előtt NSG létrehozása.  Az Azure NSG-ket a hálózati rétegben tűzfal egyenértékűek.  Az NSG-k Azure további információkért lásd: [NSG-k létrehozása az Azure parancssori felület](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Egy bejövő SSH engedélyezési szabály hozzáadása

A Linux virtuális gép kell elérnie az internetről, ezért egy szabályt, hogy 22-es portot a bejövő forgalmat 22-es portot a hálózaton keresztül átadhatja a Linux virtuális Gépre van szükség.

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix * \
--source-port-range * \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>Adjon hozzá egy alhálózatot a virtuális hálózat

Virtuális gépek a Vneten belül egy alhálózaton kell lennie.  Minden egyes virtuális hálózat több alhálózattal is rendelkezik.  Hozza létre az alhálózatot, és az alhálózatot hozzátársíthat a tűzfal hozzáadása az alhálózat NSG.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

Az alhálózat most már hozzá a virtuális hálózaton belül, és az NSG és az NSG-szabály.

## <a name="creating-static-dns-names"></a>Statikus DNS-nevek létrehozása

Azure a rendkívül rugalmas, de szeretné használni a virtuális gépek név feloldása DNS-neveit, hozza létre a címzetteket, virtuális hálózati adapterek (VNics) DNS-címkék használatával kell.  VNics, újra felhasználhatja azokat csatlakoztatja őket másik virtuális gépek, így a virtuális hálózati statikus erőforrásként a virtuális gépek ideiglenes lehetnek fontosak.  A virtuális címkézés DNS használatával azt is más virtuális gépek virtuális egyszerű névfeloldás engedélyezése.  Feloldható nevek használata lehetővé teszi, hogy a DNS-neve alapján az automation-kiszolgáló eléréséhez más virtuális gépek `Jenkins` vagy a Git kiszolgálóra `gitrepo`.  Hozzon létre egy virtuális hálózati, és rendelje hozzá azt az előző lépésben létrehozott alhálózati.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>A virtuális hálózat és az NSG-t a virtuális gép üzembe helyezése

Most már van egy Vnetre, egy alhálózaton belül, hogy a virtuális hálózaton, egy NSG-t tűzfal működött védelméhez és az alhálózati 22-es port kivételével az összes bejövő forgalmát blokkolja az SSH.  A virtuális gép most már telepíthető a meglévő hálózati infrastruktúra belül.

Az Azure CLI-vel, és a `azure vm create` parancs, a Linux virtuális Gépet a rendszer a meglévő Azure-erőforráscsoportot, hálózatok, alhálózat és virtuális hálózati.  A parancssori felület használatával teljes virtuális gép telepítése További információkért lásd: [hozzon létre egy teljes Linux környezetet az Azure parancssori felület használatával](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

A parancssori felület jelzők használatával hívásához a meglévő erőforrásokat, azt kérje meg a virtuális Gépet a meglévő hálózaton telepítendő Azure.  Megújítja, miután egy VNet és alhálózat van telepítve, hogy azok maradhatnak, statikus vagy állandó erőforrásként belül az Azure-régiót.  

## <a name="next-steps"></a>További lépések
* [Saját egyéni környezet létrehozása Linux virtuális gép számára közvetlenül Azure parancssori felület parancsait használva](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux virtuális gép létrehozása az Azure-ban sablonok használatával](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
