---
title: A több hálózati adapterrel rendelkező Azure Linux virtuális gép létrehozása |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Linux virtuális gép több hálózati adapter nem csatlakoztatható az Azure parancssori felületen vagy a Resource Manager sablonok használatával.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 20e3a65c28e95849822d81076b6780e05a2aebbf
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-linux-virtual-machine-with-multiple-nics-using-the-azure-cli-10"></a>Az Azure CLI 1.0 használatával több hálózati adapterrel rendelkező Linux virtuális gép létrehozása
Létrehozhat egy virtuális gép (VM), amelyen több virtuális hálózati adapterek (NIC) nem csatlakoztatható az Azure-ban. Egy gyakori forgatókönyv, hogy az előtér- és kapcsolat, vagy a hálózaton, figyelési vagy biztonsági mentési megoldásra dedikált különböző alhálózatokon. A cikkben gyors parancsok futtatásával hozzon létre egy virtuális gép több hálózati adapter nem csatlakoztatható. Különböző [Virtuálisgép-méretek](sizes.md) több hálózati adapter támogatja, így méretezés ennek megfelelően a virtuális Gépet.

> [!WARNING]
> Ha a virtuális gép létrehozása – az Azure CLI 1.0 a meglévő virtuális hálózati adapter nem adhat hozzá kell rendelni több hálózati adapter. Is [hálózati adapterek hozzáadása egy meglévő virtuális gépre az Azure CLI 2.0](multiple-nics.md). Emellett [hozzon létre egy virtuális Gépet az eredeti virtuális lemez(ek) alapján](copy-vm.md) , és hozzon létre több hálózati adaptert a virtuális gép központi telepítésekor.


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#create-supporting-resources) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](multiple-nics.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.


## <a name="create-supporting-resources"></a>Kapcsolódó erőforrások létrehozása
Győződjön meg arról, hogy rendelkezik a [Azure CLI](../../cli-install-nodejs.md) jelentkezett be, és a Resource Manager módot használ:

```azurecli
azure config mode arm
```

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevekre *myResourceGroup*, *mystorageaccount*, és *myVM*.

Először hozzon létre egy erőforráscsoportot. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
azure group create myResourceGroup --location eastus
```

Hozzon létre egy tárfiókot, a virtuális gépek tárolására. Az alábbi példa létrehoz egy nevű tárfiók *mystorageaccount*:

```azurecli
azure storage account create mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --kind Storage \
    --sku-name PLRS
```

Csatlakozás a virtuális gép virtuális hálózat létrehozása. Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVnet* rendelkező egy címelőtagot *192.168.0.0/16*:

```azurecli
azure network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefixes 192.168.0.0/16
```

Hozzon létre két virtuális hálózati alhálózat - egy előtér-forgalmat, egy, a háttér-forgalmat. Az alábbi példa létrehoz két alhálózat, nevű *mySubnetFrontEnd* és *mySubnetBackEnd*:

```azurecli
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetFrontEnd \
    --address-prefix 192.168.1.0/24
azure network vnet subnet create \
    --resource-group myResourceGroup \
    --location myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Létrehozhat és konfigurálhat több hálózati adapter
További részleteket olvashat [üzembe helyezése az Azure parancssori felület használatával több hálózati adapter](../../virtual-machines/linux/multiple-nics.md), beleértve a hálózati adapterek létrehozása ismétlése folyamata parancsfájlokat.

Az alábbi példa létrehoz két hálózati adapterrel, nevű *myNic1* és *myNic2*, egyetlen hálózati adapterrel, minden egyes alhálózathoz csatlakozik:

```azurecli
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic1 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetFrontEnd
azure network nic create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNic2 \
    --subnet-vnet-name myVnet \
    --subnet-name mySubnetBackEnd
```

Általában akkor is létrehozhat egy [hálózati biztonsági csoport](../../virtual-network/virtual-networks-nsg.md) vagy [terheléselosztó](../../load-balancer/load-balancer-overview.md) segítségével kezelhetők és eloszthatók a forgalmat a virtuális gépek között. Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű *myNetworkSecurityGroup*:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

A hálózati adapter a hálózati biztonsági csoport történő kötés `azure network nic set`. Az alábbi példa köti *myNic1* és *myNic2* rendelkező *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set \
    --resource-group myResourceGroup \
    --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Hozzon létre egy virtuális Gépet, és csatlakoztassa a hálózati adapterek
A virtuális gép létrehozásakor most több hálózati adaptert adjon meg. Ahelyett, hogy használatával `--nic-name` egyetlen hálózati adapter biztosít, Ehelyett használhatja `--nic-names` , és adja meg a hálózati adapterek vesszővel tagolt listája. Is kell vigyázni, ha a Virtuálisgép-méretet választja. Nincsenek korlátai, adhat hozzá egy virtuális hálózati adapterrel teljes száma. Tudjon meg többet az [Linux Virtuálisgép-méretek](sizes.md). A következő példa bemutatja, hogyan adhatja meg a több hálózati adapterrel és egy virtuális gép méretét, amely támogatja a több hálózati adapter (*Standard_DS2_v2*):

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

Több hálózati adapter a Linux virtuális gépek hozzáadásakor útválasztási szabályok létrehozásához szükséges. Ezek a szabályok lehetővé teszik a virtuális Gépet, amelyhez tartozik egy adott hálózati adatforgalmat Ellenkező esetben forgalom tartozó eth1, például nem tudja feldolgozni megfelelően a meghatározott alapértelmezett útvonalat. Útválasztási a probléma megoldásához tekintse meg a [konfigurálása vendég operációs rendszer számára több hálózati adapter](multiple-nics.md#configure-guest-os-for-multiple-nics).

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

Több hálózati adapter a Linux virtuális gépek hozzáadásakor útválasztási szabályok létrehozásához szükséges. Ezek a szabályok lehetővé teszik a virtuális Gépet, amelyhez tartozik egy adott hálózati adatforgalmat Ellenkező esetben forgalom tartozó eth1, például nem tudja feldolgozni megfelelően a meghatározott alapértelmezett útvonalat. Útválasztási a probléma megoldásához tekintse meg a [konfigurálása vendég operációs rendszer számára több hálózati adapter](multiple-nics.md#configure-guest-os-for-multiple-nics).

## <a name="next-steps"></a>További lépések
Mindenképpen tekintse át [Linux Virtuálisgép-méretek](sizes.md) több hálózati adapterrel rendelkező virtuális gép létrehozása közben. Nagy figyelmet fordítani az egyes Virtuálisgép-méretet támogatja a hálózati adapterek maximális száma. 

Ne feledje, hogy nem adható hozzá a további hálózati adapterek egy meglévő virtuális gépre, a virtuális gép telepítésekor létre kell hoznia a hálózati adaptert. Győződjön meg arról, hogy rendelkezik-e a kezdettől szükséges hálózati kapcsolatot a központi telepítések tervezése során kezeli.

