---
title: Nyisson meg portokat a Linux virtuális gép Azure CLI 1.0 |} Microsoft Docs
description: Nyisson meg egy portot / hozzon létre egy végpontot a Linux virtuális gép az Azure resource manager üzembe helyezési modellben és az Azure CLI 1.0 útmutató
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: 9520f76ed2ed1d9953f887bc27003e3e640341ba
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-using-the-azure-cli-10"></a>Az Azure-ban az Azure CLI 1.0 portok és a Linux virtuális gép végpontok megnyitása
Nyissa meg a portot, vagy hozzon létre egy végpontot a virtuális gép (VM), az Azure-ban egy alhálózatot vagy a virtuális gép hálózati illesztő hálózati szűrő létrehozásával. Ezek a szűrők, amely szabályozza a bejövő és kimenő forgalmat, a hálózati biztonsági csoport az erőforrás a forgalmat fogadó csatolva helyezze el. Ilyenek például a webes forgalom most használja a 80-as porton. Ez a cikk bemutatja, hogyan nyisson meg egy portot a virtuális gépre az Azure CLI 1.0 használatával.


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#quick-commands) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](nsg-quickstart.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.


## <a name="quick-commands"></a>Gyors parancsok
Hálózati biztonsági csoport és a szükséges szabályok létrehozásához [az Azure CLI 1.0](../../cli-install-nodejs.md) telepített és a Resource Manager módra:

```azurecli
azure config mode arm
```

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevekre *myResourceGroup*, *myNetworkSecurityGroup*, és *myVnet*.

Hozzon létre a hálózati biztonsági csoport, írja be megfelelően a saját nevét és helyét. Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű *myNetworkSecurityGroup* a a *eastus* helye:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adjon hozzá egy szabályt, amely lehetővé teszi a webkiszolgáló a HTTP-forgalom (vagy a saját forgatókönyvben például SSH hozzáférés vagy az adatbázis-kapcsolat beállítása). Az alábbi példa létrehoz egy nevű szabályt *myNetworkSecurityGroupRule* a TCP-forgalmat engedélyezi a 80-as port:

```azurecli
azure network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --destination-port-range 80 \
    --access allow
```

A hálózati biztonsági csoport társítani a virtuális gép hálózati illesztőt (NIC). A következő példa egy olyan meglévő hálózati adapter nevű társítja *myNic* együtt a hálózati biztonsági csoport nevű *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --name myNic
```

Azt is megteheti a hálózati biztonsági csoport lehet társítani a virtuális hálózati alhálózat, nem pedig csak egy virtuális hálózati adapteréhez. A következő példa egy létező alhálózatot nevű társítja *mySubnet* a a *myVnet* a hálózati biztonsági csoport nevű virtuális hálózat *myNetworkSecurityGroup*:

```azurecli
azure network vnet subnet set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>További információ a hálózati biztonsági csoportok
A gyors parancsok lehetővé teszik, amelyekből megismerheti a forgalom halad a virtuális Gépet. Hálózati biztonsági csoportok számos különleges szolgáltatásait és az erőforrásokhoz való hozzáférés szabályozása részletességgel adja meg. További tudnivalók [itt szabályok létrehozása a hálózati biztonsági csoport és a hozzáférés-vezérlési lista](../../virtual-network/tutorial-filter-network-traffic-cli.md).

Hálózati biztonsági csoportok és ACL-szabályok Azure Resource Manager sablonokban definiálhat. Tudjon meg többet az [hálózati biztonsági csoportok létrehozása a sablonok](../../virtual-network/template-samples.md).

Ha kell használnia port-továbbító egy egyedi külső port hozzárendelését a virtuális gép egyik belső portjához, használja a terheléselosztó és a hálózati címfordítás (NAT) szabályok. Érdemes lehet például 8080-as TCP-porton kívülről, és rendelkezik a 80-as TCP-portot a virtuális gép felé irányuló forgalmat. Többet is megtudhat [egy internetre irányuló terheléselosztói](../../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>További lépések
Ebben a példában létrehozott egy egyszerű szabályt, amely engedélyezi a HTTP-forgalmat. További részletes környezetek létrehozásáról a következő cikkekben találhat:

* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
* [Mi az a hálózati biztonsági csoport (NSG)?](../../virtual-network/virtual-networks-nsg.md)
* [Terheléselosztók Azure Resource Manager áttekintése](../../load-balancer/load-balancer-arm.md)

