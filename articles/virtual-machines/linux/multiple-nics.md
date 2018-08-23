---
title: Linux rendszerű virtuális gép létrehozása az Azure-ban több hálózati adapterrel rendelkező |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Linux rendszerű virtuális gép több hálózati adapter csatlakozik az Azure CLI 2.0-s vagy a Resource Manager-sablonok használatával.
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
ms.date: 06/07/2018
ms.author: iainfou
ms.openlocfilehash: 77feb52a4ba2013bd6ec0afcd30a20f05227031e
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054506"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Hogyan hozhat létre Linux rendszerű virtuális gép az Azure-ban több hálózati kártyák
Létrehozhat egy virtuális gépet (VM) az Azure-ban, amely rendelkezik csatlakoztatott több virtuális hálózati adapter (NIC). Gyakran előfordul, hogy az előtér- és háttér-kapcsolat vagy egy figyelési vagy biztonsági mentési megoldás számára kijelölt hálózat különböző alhálózatokon. Ez a cikk részletesen csatlakozik, több hálózati adapterrel rendelkező virtuális gép létrehozása és hozzáadása, vagy távolítsa el a hálózati adapterek meglévő virtuális gépről. Különböző [Virtuálisgép-méretek](sizes.md) támogatja a hálózati adapterek különböző számú, tehát ennek megfelelően az a virtuális gép méretezéséhez.

Ez a cikk részletesen bemutatja az Azure CLI 2.0 használatával több hálózati adapterrel rendelkező virtuális gép létrehozásához. Az [Azure CLI 1.0-s](multiple-nics-nodejs.md) verziójával is elvégezheti ezeket a lépéseket.


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
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Hozzon létre egy alhálózatot a háttér-forgalom [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). A következő példában létrehozunk egy nevű alhálózatot *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
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

Az előző lépésekben létrehozott egy virtuális hálózatot és alhálózatot, csatolt hálózati adapter, majd létrehozott egy virtuális Gépet. Egy nyilvános IP cím és hálózati biztonsági csoport szabályai, amelyek lehetővé teszik az SSH-forgalmat nem jöttek létre. A vendég operációs rendszer konfigurálása több hálózati adapterrel, meg kell távoli kapcsolatok engedélyezése, és helyileg futtasson parancsokat a virtuális gépen.

SSH-forgalom engedélyezéséhez hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az [az network nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) módon:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Hozzon létre egy nyilvános IP-címet [az network public-ip létrehozása](/cli/azure/network/public-ip#az-network-public-ip-create) és rendelje hozzá az első hálózati adapter [az network nic ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip-address create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip-addres myPublicIP
```

A virtuális gép nyilvános IP-cím megtekintéséhez használja [az vm show](/cli/azure/vm#az-vm-show) módon:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Most már ssh-KAPCSOLATOT a virtuális gép nyilvános IP-címét. Az alapértelmezett felhasználónév az előző lépésben megadott volt *azureuser*. Adja meg a saját felhasználónevét és a nyilvános IP-cím:

```bash
ssh azureuser@137.117.58.232
```

Küldjön, vagy a másodlagos hálózati adapterre, akkor manuálisan adja hozzá az állandó útvonalakat, az operációs rendszer minden egyes másodlagos hálózati adapter. Ebben a cikkben *eth1* a másodlagos felület. Állandó útvonalak hozzáadására az operációs rendszerre vonatkozó utasításokat disztribúció változhat. Útmutatás a disztribúció dokumentációjában talál.

Az útvonal hozzáadása az operációs rendszer, amikor az átjáró címe az *ikonra.1* bármelyik alhálózatot a hálózati adapter van. Például, ha a hálózati adapterhez van hozzárendelve a cím *10.0.2.4 cím*, az átjáró adja meg az útvonal *10.0.2.1*. Az útvonal célállomása egy adott hálózat meghatározása, vagy adja meg a cél *0.0.0.0*, ha azt szeretné, hogy minden forgalom a megadott-átjárón nyissa meg az interfész. Az átjáró az egyes alhálózatokon kezeli a virtuális hálózat.

Miután hozzáadott egy másodlagos felületen az útvonalat, győződjön meg arról, hogy az útvonal az útvonaltáblában a `route -n`. Az alábbi példa kimenetében szól az útvonaltábla, amely az ebben a cikkben a virtuális géphez hozzáadott két hálózati adapterrel rendelkezik:

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

Győződjön meg arról, hogy az útvonal hozzáadott továbbra is fennáll újraindítások között újraindítás után újra az útvonaltábla ellenőrzésével. Kapcsolat tesztelése adhatja meg a következő parancsot, például, ahol *eth1* egy másodlagos hálózati adapter neve:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>További lépések
Felülvizsgálat [Linux Virtuálisgép-méretek](sizes.md) több hálózati adapterrel rendelkező virtuális gép létrehozása közben. Az egyes Virtuálisgép-méretet támogatja a hálózati adapterek maximális száma figyelmet fordítania.

További biztonságos a virtuális gépek használatával csak a time VM access. Ez a funkció a hálózati biztonsági csoportszabályok SSH-forgalmat, ha szükséges, és a egy meghatározott ideig nyílik meg. További információk: [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáférés használata esetén).
