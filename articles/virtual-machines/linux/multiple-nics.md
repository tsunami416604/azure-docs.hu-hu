---
title: A több hálózati adapterrel rendelkező Azure Linux virtuális gép létrehozása |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Linux virtuális gép több hálózati adapter nem csatlakoztatható az Azure CLI 2.0 vagy az erőforrás-kezelő sablonok használatával.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: d981ffc9a0053ed8bf2d49f386f7c1c82d50c907
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Hogyan Linux virtuális gép létrehozása az Azure-ban a több hálózati kártyák
Létrehozhat egy virtuális gép (VM), amelyen több virtuális hálózati adapterek (NIC) nem csatlakoztatható az Azure-ban. Egy gyakori forgatókönyv, hogy az előtér- és kapcsolat, vagy a hálózaton, figyelési vagy biztonsági mentési megoldásra dedikált különböző alhálózatokon. Ez a cikk részletesen több hálózati adapter nem csatlakoztatható a virtuális gép létrehozása és hozzáadása vagy eltávolítása a hálózati adapter egy meglévő virtuális gépről. Különböző [Virtuálisgép-méretek](sizes.md) több hálózati adapter támogatja, így méretezés ennek megfelelően a virtuális Gépet.

Ez a cikk részletezi az Azure CLI 2.0 több hálózati adapterrel rendelkező virtuális gép létrehozása. Az [Azure CLI 1.0-s](multiple-nics-nodejs.md) verziójával is elvégezheti ezeket a lépéseket.


## <a name="create-supporting-resources"></a>Kapcsolódó erőforrások létrehozása
Telepítse a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) és való bejelentkezéshez az Azure fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevekre *myResourceGroup*, *mystorageaccount*, és *myVM*.

Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

A virtuális hálózat létrehozása [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create). Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVnet* és nevű alhálózat *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Hozzon létre egy alhálózatot a háttér-forgalom [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). Az alábbi példakód létrehozza nevű alhálózat *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Hozzon létre egy hálózati biztonsági csoport [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create). A következő példa a *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot hozza létre:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Létrehozhat és konfigurálhat több hálózati adapter
Hozzon létre két hálózati adapterrel [az hálózat összevont hálózati létrehozása](/cli/azure/network/nic#az_network_nic_create). Az alábbi példa létrehoz két hálózati adapterrel, nevű *myNic1* és *myNic2*, a hálózati biztonsági csoport csatlakoztatva egy NIC kártyával, minden egyes alhálózathoz csatlakozik:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Hozzon létre egy virtuális Gépet, és csatlakoztassa a hálózati adapterek
A virtuális gép létrehozásakor adja meg a hálózati adapter segítségével létrehozott `--nics`. Is kell vigyázni, ha a Virtuálisgép-méretet választja. Nincsenek korlátai, adhat hozzá egy virtuális hálózati adapterrel teljes száma. Tudjon meg többet az [Linux Virtuálisgép-méretek](sizes.md). 

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

A lépések végrehajtásával adja hozzá útválasztási táblázataiba a vendég operációs rendszer [a vendég operációs rendszer konfigurálása több hálózati adapter](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Adja hozzá egy hálózati Adaptert egy virtuális géphez
Az előző lépésekben létrehozott egy virtuális gép több hálózati adapterrel rendelkező. Hálózati adapter egy meglévő virtuális gépre az Azure CLI 2.0 is hozzáadhat. Különböző [Virtuálisgép-méretek](sizes.md) több hálózati adapter támogatja, így méretezés ennek megfelelően a virtuális Gépet. Ha szükséges, akkor [méretezze át a virtuális gépek](change-vm-size.md).

Hozzon létre egy másik hálózati Adaptert a [az hálózat összevont hálózati létrehozása](/cli/azure/network/nic#az_network_nic_create). Az alábbi példa létrehoz egy hálózati adapter nevű *myNic3* a háttér-alhálózat és a hálózati biztonsági csoport az előző lépésekben létrehozott csatlakozik:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

A hálózati adapter hozzáadása egy meglévő virtuális Gépre, először a virtuális Géphez a felszabadítani [az virtuális gép felszabadítása](/cli/azure/vm#az_vm_deallocate). Az alábbi példa felszabadítja a nevű virtuális gép *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adja hozzá a hálózati adapter [az vm hálózati adapter hozzáadása](/cli/azure/vm/nic#az_vm_nic_add). A következő példakóddal felveheti *myNic3* való *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Indítsa el a virtuális Géphez a [az vm indítása](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

A lépések végrehajtásával adja hozzá útválasztási táblázataiba a vendég operációs rendszer [a vendég operációs rendszer konfigurálása több hálózati adapter](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>A virtuális gép egy hálózati adapter eltávolítása
Meglévő virtuális hálózati Adapterhez eltávolításához először a virtuális Géphez a felszabadítani [az virtuális gép felszabadítása](/cli/azure/vm#az_vm_deallocate). Az alábbi példa felszabadítja a nevű virtuális gép *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Távolítsa el a hálózati adapter [az vm hálózati adapter eltávolítása](/cli/azure/vm/nic#az_vm_nic_remove). A következő példában eltávolítjuk *myNic3* a *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Indítsa el a virtuális Géphez a [az vm indítása](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Resource Manager-sablonok segítségével több hálózati adapter létrehozása
Az Azure Resource Manager-sablonok deklaratív JSON-fájlok segítségével határozza meg a környezetben. Ha egy [áttekintése Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Resource Manager-sablonok segítségével hozzon létre egy erőforrás több példánya központi telepítést végez, például több hálózati adapter létrehozása során. Használhat *másolási* létrehozásához példányok száma:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Tudjon meg többet az [használatával több példány létrehozásával *másolási*](../../resource-group-create-multiple.md). 

Használhatja a `copyIndex()` majd hozzáfűzendő erőforrás nevét, amely lehetővé teszi, hogy hozzon létre több `myNic1`, `myNic2`stb. A következő hozzáfűzése a indexértéket példáját mutatja be:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Átfogó példát olvasható [létrehozása a Resource Manager-sablonok segítségével több hálózati adapter](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

A lépések végrehajtásával adja hozzá útválasztási táblázataiba a vendég operációs rendszer [a vendég operációs rendszer konfigurálása több hálózati adapter](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>A vendég operációs rendszer konfigurálása több hálózati adapter
Több hálózati adapter a Linux virtuális gépek hozzáadásakor útválasztási szabályok létrehozásához szükséges. Ezek a szabályok lehetővé teszik a virtuális Gépet, amelyhez tartozik egy adott hálózati adatforgalmat Ellenkező esetben a forgalom tartozó *eth1*, például nem tudja feldolgozni a helyes a beállított alapértelmezett útvonalat.

Ha útválasztási a probléma megoldásához először adjon hozzá két útválasztási táblázatokat */etc/iproute2/rt_tables* az alábbiak szerint:

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

A módosítás állandó és alkalmazott hálózati verem aktiválása során, szerkessze */etc/sysconfig/network-scripts/ifcfg-eth0* és */etc/sysconfig/network-scripts/ifcfg-eth1*. A sor ALTER *"NM_CONTROLLED = yes"* való *"NM_CONTROLLED nem ="*. Ebben a lépésben nélkül további szabályok/útválasztását nem automatikusan érvényesek.
 
A következő kiterjesztése a útvonaltáblát. Tegyük fel, a következő telepítő érvényben van:

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

Volna ezután hozza létre a következő fájlokat és a megfelelő szabályok és útvonalak hozzáadása az egyes:

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

A módosítások alkalmazásához, indítsa újra a *hálózati* szolgáltatást az alábbiak szerint:

```bash
systemctl restart network
```

Az útválasztási szabályokat megfelelő helyen, illetve kapcsolatba léphet vagy felület igény szerint.


## <a name="next-steps"></a>További lépések
Felülvizsgálati [Linux Virtuálisgép-méretek](sizes.md) több hálózati adapterrel rendelkező virtuális gép létrehozása közben. Nagy figyelmet fordítani az egyes Virtuálisgép-méretet támogatja a hálózati adapterek maximális száma. 
