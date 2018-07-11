---
title: Linux rendszerű virtuális gép létrehozása az Azure-ban több hálózati adapterrel rendelkező |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Linux rendszerű virtuális gép több hálózati adapter csatlakozik az Azure CLI 2.0-s vagy a Resource Manager-sablonok használatával.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 257b80c30823be41893be8659845d4fcbc922da3
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932272"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Hogyan hozhat létre Linux rendszerű virtuális gép az Azure-ban több hálózati kártyák
Létrehozhat egy virtuális gépet (VM) az Azure-ban, amely rendelkezik csatlakoztatott több virtuális hálózati adapter (NIC). Gyakran előfordul, hogy az előtér- és háttér-kapcsolat vagy egy figyelési vagy biztonsági mentési megoldás számára kijelölt hálózat különböző alhálózatokon. Ez a cikk részletesen csatlakozik, több hálózati adapterrel rendelkező virtuális gép létrehozása és hozzáadása, vagy távolítsa el a hálózati adapterek meglévő virtuális gépről. Különböző [Virtuálisgép-méretek](sizes.md) támogatja a hálózati adapterek különböző számú, tehát ennek megfelelően az a virtuális gép méretezéséhez.

Ez a cikk részletesen bemutatja az Azure CLI 2.0 használatával több hálózati adapterrel rendelkező virtuális gép létrehozásához. 


## <a name="create-supporting-resources"></a>Hozzon létre a támogató erőforrások
Telepítse a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) , és jelentkezzen be az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa paraméterneveket foglalt *myResourceGroup*, *mystorageaccount*, és *myVM*.

Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre a virtuális hálózatba a [az network vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create). A következő példában létrehozunk egy nevű virtuális hálózatot *myVnet* és nevű alhálózatot *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Hozzon létre egy alhálózatot a háttér-forgalom [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). A következő példában létrehozunk egy nevű alhálózatot *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Hozzon létre egy hálózati biztonsági csoport [az network nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create). A következő példa a *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot hozza létre:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Létrehozhat és konfigurálhat több hálózati adapter
Hozzon létre két hálózati adapterrel [az network nic létrehozása](/cli/azure/network/nic#az_network_nic_create). Az alábbi példa létrehoz két hálózati adapterrel, nevű *myNic1* és *myNic2*, a hálózati biztonsági csoport csatlakoztatott minden alhálózathoz csatlakozik, több hálózati Adapterrel rendelkező:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Hozzon létre egy virtuális Gépet, és a hálózati adapter csatolása
A virtuális gép létrehozásakor adja meg a hálózati adapterek segítségével létrehozott `--nics`. Is kell körültekintően járjon el a virtuális gép méretének kiválasztásakor. A hálózati adapterrel is hozzáadhat egy virtuális gép teljes száma korlátozva van. Tudjon meg többet [Linux Virtuálisgép-méretek](sizes.md). 

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre:

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

Útválasztási táblázatok hozzáadása a vendég operációs rendszer által ismertetett lépéseket követve [a vendég operációs rendszer konfigurálása több hálózati adapterrel](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>A hálózati adapter hozzáadása egy virtuális Géphez
Az előző lépésekben létrehozott egy virtuális Gépet több hálózati adapterrel. Hálózati adapter is egy meglévő virtuális Gépet az Azure CLI 2.0 használatával adhat hozzá. Különböző [Virtuálisgép-méretek](sizes.md) támogatja a hálózati adapterek különböző számú, tehát ennek megfelelően az a virtuális gép méretezéséhez. Ha szükséges, [virtuális gép átméretezése](change-vm-size.md).

Hozzon létre egy másik hálózati Adaptert a [az network nic létrehozása](/cli/azure/network/nic#az_network_nic_create). A következő példában létrehozunk egy hálózati Adaptert *myNic3* a háttérbeli alhálózat és hálózati biztonsági csoport az előző lépésekben létrehozott csatlakozik:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

A hálózati adapter hozzáadása egy meglévő virtuális Gépet, először szabadítsa fel a virtuális Gépet a [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Az alábbi példa felszabadítja a virtuális gép nevű *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adja hozzá a NIC- [hozzáadása az vm nic](/cli/azure/vm/nic#az_vm_nic_add). A következő példakóddal *myNic3* való *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Indítsa el a virtuális Gépet a [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Útválasztási táblázatok hozzáadása a vendég operációs rendszer által ismertetett lépéseket követve [a vendég operációs rendszer konfigurálása több hálózati adapterrel](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Távolítsa el a hálózati adapter egy virtuális gépről
Meglévő virtuális gépről a hálózati adapter eltávolításához először szabadítsa fel a virtuális Gépet a [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Az alábbi példa felszabadítja a virtuális gép nevű *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Távolítsa el a hálózati Adaptert a [eltávolítása az vm nic](/cli/azure/vm/nic#az_vm_nic_remove). A következő példa eltávolítja *myNic3* a *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Indítsa el a virtuális Gépet a [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Resource Manager-sablonokkal több hálózati adapter létrehozása
Az Azure Resource Manager-sablonok deklaratív JSON-fájlok használatával adja meg a környezetben. Tudjon meg egy [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md). Resource Manager-sablonok lehetővé teszik egy erőforrás több példányának létrehozása az üzembe helyezés, például a több hálózati adapter létrehozása során. Használhat *másolási* adható meg a példányok létrehozásához:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Tudjon meg többet [használatával több példány létrehozása *másolási*](../../resource-group-create-multiple.md). 

Használhatja egy `copyIndex()` majd hozzáfűzni a kívánt erőforrás nevét, amely lehetővé teszi, hogy hozzon létre több `myNic1`, `myNic2`stb. Az alábbiakban látható egy példa az index érték hozzáfűzése:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Teljes példát olvashat [létrehozása a Resource Manager-sablonokkal több hálózati adapterrel](../../virtual-network/template-samples.md).

Útválasztási táblázatok hozzáadása a vendég operációs rendszer által ismertetett lépéseket követve [a vendég operációs rendszer konfigurálása több hálózati adapterrel](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Több hálózati adapterrel a vendég operációs rendszer konfigurálása
Linux virtuális géphez több hálózati adapter hozzáadásakor meg kell hozzon létre útválasztási szabályokat. Ezek a szabályok lehetővé teszik a virtuális gép küldjön és fogadjon forgalmat, amelyhez tartozik egy adott hálózati adapterhez. Egyébként, forgalom, amelyhez tartozik *eth1*, például nem tudja feldolgozni a megfelelően definiált alapértelmezett útvonalát.

Útválasztási probléma kijavításához először adjon hozzá két útválasztási táblázatokat */etc/iproute2/rt_tables* módon:

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

A módosításnak állandó és alkalmazott hálózati verem aktiválása során, a Szerkesztés */etc/sysconfig/network-scripts/ifcfg-eth0* és */etc/sysconfig/network-scripts/ifcfg-eth1*. A sor ALTER *"NM_CONTROLLED = yes"* való *"NM_CONTROLLED = nem"*. Ebben a lépésben nélkül további szabályok/útválasztását rendszer nem alkalmazza automatikusan.
 
Ezután kiterjesztheti az útválasztási táblázatok. Tegyük fel, a következő beállítás van érvényben:

*Útválasztás*

```bash
default via 10.0.1.1 dev eth0 proto static metric 100
10.0.1.0/24 dev eth0 proto kernel scope link src 10.0.1.4 metric 100
10.0.1.0/24 dev eth1 proto kernel scope link src 10.0.1.5 metric 101
168.63.129.16 via 10.0.1.1 dev eth0 proto dhcp metric 100
169.254.169.254 via 10.0.1.1 dev eth0 proto dhcp metric 100
```

*Felületek*

```bash
lo: inet 127.0.0.1/8 scope host lo
eth0: inet 10.0.1.4/24 brd 10.0.1.255 scope global eth0    
eth1: inet 10.0.1.5/24 brd 10.0.1.255 scope global eth1
```

Meg szeretné majd hozza létre a következő fájlokat, és adja hozzá a megfelelő szabályok és útvonalak minden egyes:

- */etc/sysconfig/network-scripts/rule-eth0*

    ```bash
    from 10.0.1.4/32 table eth0-rt
    to 10.0.1.4/32 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/route-eth0*

    ```bash
    10.0.1.0/24 dev eth0 table eth0-rt
    default via 10.0.1.1 dev eth0 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/rule-eth1*

    ```bash
    from 10.0.1.5/32 table eth1-rt
    to 10.0.1.5/32 table eth1-rt
    ```

- */etc/sysconfig/network-scripts/route-eth1*

    ```bash
    10.0.1.0/24 dev eth1 table eth1-rt
    default via 10.0.1.1 dev eth1 table eth1-rt
    ```

A módosítások életbe léptetéséhez indítsa újra a *hálózati* szolgáltatást az alábbiak szerint:

```bash
systemctl restart network
```

Az útválasztási szabályokat, most már megfelelően és léphet vagy felület igény szerint.


## <a name="next-steps"></a>További lépések
Felülvizsgálat [Linux Virtuálisgép-méretek](sizes.md) több hálózati adapterrel rendelkező virtuális gép létrehozása közben. Az egyes Virtuálisgép-méretet támogatja a hálózati adapterek maximális száma figyelmet fordítania. 
