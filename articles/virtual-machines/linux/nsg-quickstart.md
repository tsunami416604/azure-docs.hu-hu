---
title: "Nyisson meg portokat a Linux virtuális gép Azure CLI 2.0 |} Microsoft Docs"
description: "Nyisson meg egy portot / hozzon létre egy végpontot a Linux virtuális gép az Azure resource manager üzembe helyezési modellben és az Azure CLI 2.0 útmutató"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 91908b03522788d470fdb93121f620bfcdef9085
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Nyisson meg portokat és a Linux virtuális gép végpontokat az Azure parancssori felület
Nyissa meg a portot, vagy hozzon létre egy végpontot a virtuális gép (VM), az Azure-ban egy alhálózatot vagy a virtuális gép hálózati illesztő hálózati szűrő létrehozásával. Ezek a szűrők, amely szabályozza a bejövő és kimenő forgalmat, a hálózati biztonsági csoport az erőforrás a forgalmat fogadó csatolva helyezze el. Ilyenek például a webes forgalom most használja a 80-as porton. Ez a cikk bemutatja, hogyan nyisson meg egy portot a virtuális gépre az Azure CLI 2.0. Az [Azure CLI 1.0-s](nsg-quickstart-nodejs.md) verziójával is elvégezheti ezeket a lépéseket.

Hálózati biztonsági csoport és a legújabb szükséges szabályok létrehozásához [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/#az_login).

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők *myResourceGroup*, *myNetworkSecurityGroup*, és *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Gyorsan nyisson meg egy portot a virtuális gépek
Ha gyorsan nyisson meg egy portot a virtuális gépek a fejlesztési és tesztelési célú forgatókönyvek van szüksége, használhatja a [az vm-port megnyitása](/cli/azure/vm#az_vm_open_port) parancsot. Ez a parancs hálózati biztonsági csoportot hoz létre, szabály hozzáadása, és alkalmazza azt a virtuális gép vagy alhálózat. Az alábbi példa portot nyit meg *80* nevű virtuális gépen *myVM* az erőforráscsoport neve *myResourceGroup*.

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Pontosabban a szabályokat, például egy forrás IP-címtartomány meghatározása további ebben a cikkben további lépéseket.


## <a name="create-a-network-security-group-and-rules"></a>Hálózati biztonsági csoport és a szabályok létrehozása
A hálózati biztonsági csoport létrehozása [az hálózati nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create). Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű *myNetworkSecurityGroup* a a *eastus* helye:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Vegye fel a szabályt [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) engedélyezi a HTTP-forgalom számára a webkiszolgáló (vagy a saját forgatókönyvben például SSH hozzáférés vagy az adatbázis-kapcsolat beállítása). Az alábbi példa létrehoz egy nevű szabályt *myNetworkSecurityGroupRule* a TCP-forgalmat engedélyezi a 80-as port:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Hálózati biztonsági csoport alkalmazása a virtuális gép
A hálózati biztonsági csoport társítani a virtuális gép hálózati illesztőt (NIC) a [az hálózati nic frissítés](/cli/azure/network/nic#az_network_nic_update). A következő példa egy olyan meglévő hálózati adapter nevű társítja *myNic* együtt a hálózati biztonsági csoport nevű *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Azt is megteheti, hogy társíthasson a hálózati biztonsági csoport virtuális hálózati alhálózat [az hálózati vnet alhálózati frissítés](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) helyett csak egy virtuális hálózati adapteréhez. A következő példa egy létező alhálózatot nevű társítja *mySubnet* a a *myVnet* a hálózati biztonsági csoport nevű virtuális hálózat *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>További információ a hálózati biztonsági csoportok
A gyors parancsok lehetővé teszik, amelyekből megismerheti a forgalom halad a virtuális Gépet. Hálózati biztonsági csoportok számos különleges szolgáltatásait és az erőforrásokhoz való hozzáférés szabályozása részletességgel adja meg. További tudnivalók [itt szabályok létrehozása a hálózati biztonsági csoport és a hozzáférés-vezérlési lista](tutorial-virtual-network.md#secure-network-traffic).

Magas rendelkezésre állású webes alkalmazásokhoz helyezze a virtuális gépek az Azure terheléselosztó mögött. A load balancer osztja el a forgalmat a virtuális gépekhez, a hálózati biztonsági csoport, amely biztosítja a forgalomszűrést végez. További információkért lásd: [betöltése Linux virtuális gépek magas rendelkezésre állású alkalmazás létrehozása az Azure-ban egyenleg](tutorial-load-balancer.md).

## <a name="next-steps"></a>További lépések
Ebben a példában létrehozott egy egyszerű szabályt, amely engedélyezi a HTTP-forgalmat. További részletes környezetek létrehozásáról a következő cikkekben találhat:

* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
* [Mi az a hálózati biztonsági csoport (NSG)?](../../virtual-network/virtual-networks-nsg.md)
