---
title: Linuxos virtuális gép létrehozása az Azure-ban több hálózati adapterrel
description: Ismerje meg, hogyan hozhat létre egy Linux virtuális gép több hálózati adapterek csatolták az Azure CLI vagy resource manager sablonok használatával.
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: ecbff4beadd9d10a8489c89cc322c0bb67ec5f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267182"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Linuxos virtuális gép létrehozása az Azure-ban több hálózati csatolókártyával


Ez a cikk ismerteti, hogyan hozhat létre egy virtuális gép több hálózati adapterek az Azure CLI használatával.

## <a name="create-supporting-resources"></a>Támogató erőforrások létrehozása
Telepítse a legújabb [Azure CLI-t,](/cli/azure/install-az-cli2) és jelentkezzen be egy Azure-fiókba [az az login](/cli/azure/reference-index)használatával.

A következő példákban cserélje le a példaparaméterneveket a saját értékeire. Példa paraméter nevek közé *myResourceGroup*, *mystorageaccount*, és *myVM*.

Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozza létre a virtuális hálózatot az [hálózati virtuális hálózat létrehozása.](/cli/azure/network/vnet) A következő példa létrehoz egy *myVnet* és alhálózat nevű virtuális hálózat *ot, a mySubnetFrontEnd*nevet:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Hozzon létre egy alhálózatot a háttérforgalomhoz az [az hálózati virtuális hálózat alhálózatának létrehozása segítségével.](/cli/azure/network/vnet/subnet) A következő példa létrehoz egy *nASubnetBackEnd*nevű alhálózatot:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Hozzon létre egy hálózati biztonsági csoportot [az az network nsg create](/cli/azure/network/nsg)segítségével. A következő példa a *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot hozza létre:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Több hálózati adapter létrehozása és konfigurálása
Hozzon létre két hálózati adaptert [az az network nic create](/cli/azure/network/nic)segítségével. A következő példa két hálózati adaptert hoz létre, *a myNic1* és a *myNic2*nevet, amelyek a hálózati biztonsági csoportot csatlakoztatják, és minden alhálózathoz egy hálózati adapter csatlakozik:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Virtuális gép létrehozása és hálózati adapterek csatolása
A virtuális gép létrehozásakor adja meg a. `--nics` Emellett a virtuális gép méretének kiválasztásakor is óvatosnak kell lennie. A virtuális gépekhez hozzáadható hálózati adapterek teljes száma korlátokat állapít meg. További információ a [Linux virtuális gép méreteiről.](sizes.md)

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. A következő példa létrehoz egy *myVM*nevű virtuális gép:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Útválasztási táblák hozzáadása a vendég operációs rendszerhez a [Vendég operációs rendszer konfigurálása több hálózati adapterhez](#configure-guest-os-for-multiple-nics)című lépés lépéseinek végrehajtásával.

## <a name="add-a-nic-to-a-vm"></a>Hálózati adapter hozzáadása virtuális géphez
Az előző lépések létrehozott egy virtuális gép több hálózati adapterek. Hálózati adapterek is hozzáadhategy meglévő virtuális gép az Azure CLI. A különböző [virtuálisgép-méretek](sizes.md) különböző számú hálózati adaptert támogatnak, így a virtuális gép ennek megfelelően méretezze. Szükség esetén [átméretezheti a virtuális gépeket.](change-vm-size.md)

Hozzon létre egy másik hálózati adaptert [az az network nic create](/cli/azure/network/nic)segítségével. A következő példa létrehoz egy *myNic3* nevű hálózati adaptert, amely az előző lépésekben létrehozott háttér-alhálózathoz és hálózati biztonsági csoporthoz csatlakozik:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Ha hálózati adaptert szeretne hozzáadni egy meglévő virtuális géphez, először szabadítsa fel a virtuális gép az [virtuális gép felszabadításával.](/cli/azure/vm) A következő példa felszabadítja a *myVM*nevű virtuális gép:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adja hozzá a hálózati [adaptert az az vm nic add .](/cli/azure/vm/nic) A következő példa hozzáadja *a myNic3-ot* a *myVM-hez:*

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Indítsa el a virtuális gép [az vm start:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Útválasztási táblák hozzáadása a vendég operációs rendszerhez a [Vendég operációs rendszer konfigurálása több hálózati adapterhez](#configure-guest-os-for-multiple-nics)című lépés lépéseinek végrehajtásával.

## <a name="remove-a-nic-from-a-vm"></a>Hálózati adapter eltávolítása a virtuális gépről
Ha el szeretne távolítani egy hálózati adaptert egy meglévő virtuális gépről, először szabadítsa fel a virtuális gép az [virtuális gép felszabadításával.](/cli/azure/vm) A következő példa felszabadítja a *myVM*nevű virtuális gép:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Távolítsa el a hálózati [adaptert az az vm nic eltávolításával.](/cli/azure/vm/nic) A következő példa eltávolítja *a myNic3-ot* a *myVM-ből:*

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Indítsa el a virtuális gép [az vm start:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Több hálózati adapter létrehozása erőforrás-kezelősablonok használatával
Az Azure Resource Manager-sablonok deklaratív JSON-fájlokat használnak a környezet definiálásához. Elolvashatja [az Azure Resource Manager áttekintését.](../../azure-resource-manager/management/overview.md) Az Erőforrás-kezelő sablonjai lehetővé teszik egy erőforrás több példányának létrehozását a telepítés során, például több hálózati adapter ek létrehozását. A *másolás* segítségével adhatja meg a létrehozandó példányok számát:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

További információ a [több példány *másolással*történő létrehozásáról.](../../resource-group-create-multiple.md) 

Az a segítségével `copyIndex()` is hozzáfűzhet egy számot egy erőforrásnévhez, amely lehetővé teszi `myNic1`a , `myNic2`, stb. Az alábbi példa az indexérték hozzáfűzése:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Elolvashatja a teljes példát [arra, hogy több hálózati adaptert hozzon létre az Erőforrás-kezelő sablonjaival.](../../virtual-network/template-samples.md)

Útválasztási táblák hozzáadása a vendég operációs rendszerhez a [Vendég operációs rendszer konfigurálása több hálózati adapterhez](#configure-guest-os-for-multiple-nics)című lépés lépéseinek végrehajtásával.

## <a name="configure-guest-os-for-multiple-nics"></a>Vendég operációs rendszer konfigurálása több hálózati adapterhez

Az előző lépések létrehozott egy virtuális hálózat ot és alhálózatot, csatolt hálózati adaptereket, majd létrehozott egy virtuális gépet. Nem hoztak létre nyilvános IP-címet és hálózati biztonsági csoportszabályokat, amelyek engedélyezik az SSH-forgalmat. A vendég operációs rendszer több hálózati adapterek konfigurálásához engedélyeznie kell a távoli kapcsolatokat, és helyileg kell futtatnia a parancsokat a virtuális számítógépen.

Az SSH-forgalom engedélyezéséhez hozzon létre egy hálózati biztonsági csoportszabályt [az az hálózati nsg szabállyal](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) az alábbiak szerint:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Hozzon létre egy nyilvános [IP-címet az az hálózati nyilvános ip létrehozása](/cli/azure/network/public-ip#az-network-public-ip-create) és hozzárendeli az első NIC az hálózati nic [ip-config frissítés](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

A virtuális gép nyilvános IP-címének megtekintéséhez használja az [vm show-t](/cli/azure/vm#az-vm-show) az alábbiak szerint::

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Most SSH a virtuális gép nyilvános IP-címét. Az előző lépésben megadott alapértelmezett felhasználónév *az azureuser*volt. Adja meg saját felhasználónevét és nyilvános IP-címét:

```bash
ssh azureuser@137.117.58.232
```

A másodlagos hálózati adapterre vagy onnan történő küldéshez manuálisan kell állandó útvonalakat hozzáadnia az operációs rendszerhez minden másodlagos hálózati adapterhez. Ebben a cikkben az *eth1* a másodlagos felület. Az állandó útvonalak operációs rendszerhez való hozzáadására vonatkozó utasítások a kivezetéstől függően változnak. Az utasításokat a distro dokumentációjában találja.

Amikor hozzáadja az útvonalat az operációs rendszerhez, az átjáró címe *.1* lesz a hálózati adapter bármelyik alhálózatához. Ha például a hálózati adapterhez a *10.0.2.4*cím van rendelve, az útvonalhoz megadott átjáró *10.0.2.1*. Megadhat egy adott hálózatot az útvonal céljához, vagy megadhat egy *0.0.0.0-s*célt, ha azt szeretné, hogy az összeköttetés összes forgalma a megadott átjárón keresztül haladjon. Az egyes alhálózatok átjáróját a virtuális hálózat kezeli.

Miután hozzáadta az útvonalat egy másodlagos kapcsolathoz, ellenőrizze, `route -n`hogy az útvonal szerepel-e az útvonaltáblában a segítségével. A következő példa kimenete az útvonaltáblához, amely a két hálózati adaptert hozzá adta a virtuális géphez ebben a cikkben:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Ellenőrizze, hogy a hozzáadott útvonal továbbra is fennáll-e az újraindítások során, ha újraindítás után újra ellenőrzi az útvonaltáblát. A kapcsolat teszteléséhez adja meg a következő parancsot, például ahol az *eth1* egy másodlagos hálózati adapter neve:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>További lépések
Tekintse át [a Linux virtuális gép méreteit,](sizes.md) amikor több hálózati adapterrel próbál létrehozni egy virtuális gép. Ügyeljen arra, hogy az egyes virtuális gép méretetámogatja a hálózati adapterek maximális számát.

A virtuális gépek további védelme érdekében, just in time vm access. Ez a szolgáltatás megnyitja a hálózati biztonsági csoport szabályait az SSH-forgalomhoz, ha szükséges, és egy meghatározott ideig. További információk: [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáférés használata esetén).
