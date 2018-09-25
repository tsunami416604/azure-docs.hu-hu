---
title: Nyissa meg a portokat a Linux rendszerű virtuális gép az Azure CLI-vel |} A Microsoft Docs
description: Ismerje meg, hogyan nyisson meg egy portot / hozzon létre egy végpontot a Linux rendszerű virtuális géphez az Azure resource manager üzemi modell és az Azure CLI használatával
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 4244520fa30fece53e1d01e50044cfeca496066c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973337"
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Nyitott portok és végpontok egy Linux rendszerű virtuális géphez az Azure CLI-vel

Nyisson meg egy portot, vagy hozzon létre egy végpontot a virtuális géphez (VM) az Azure-ban egy alhálózatot vagy virtuális hálózati adapter hálózati szűrő létrehozásával. Ezeket a szűrőket, amely a bejövő és kimenő adatforgalom vezérlésére, helyezze el az erőforrás a forgalmat fogadó csatolt hálózati biztonsági csoport. Használjuk a webes forgalom ilyenek például a 80-as porton. Ez a cikk bemutatja, hogyan virtuális port megnyitásához az Azure CLI használatával. 


A hálózati biztonsági csoport és a legújabb szükséges szabályok létrehozásához [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index#az_login).

A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa a paraméter nevek a következők *myResourceGroup*, *myNetworkSecurityGroup*, és *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Gyorsan megnyit egy portot a virtuális gép
Ha szeretne gyorsan megnyit egy portot a virtuális gépek fejlesztési és tesztelési forgatókönyvek esetében, használhatja a [az vm open-port](/cli/azure/vm#az_vm_open_port) parancsot. Ez a parancs létrehoz egy hálózati biztonsági csoportot, szabály hozzáadása és alkalmazza azt a virtuális gép vagy az alhálózat. Az alábbi példa portot nyitja meg *80-as* nevű virtuális gépre *myVM* az erőforráscsoport neve *myResourceGroup*.

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

A szabályok definiálása egy forrás IP-címtartomány, például jobban kézben továbbra is a további lépésekről ebben a cikkben.


## <a name="create-a-network-security-group-and-rules"></a>A hálózati biztonsági csoport és a szabályok létrehozása
A hálózati biztonsági csoport létrehozása [az network nsg létrehozása](/cli/azure/network/nsg#az_network_nsg_create). A következő példában létrehozunk egy hálózati biztonsági csoport nevű *myNetworkSecurityGroup* a a *eastus* helye:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adjon hozzá egy szabályt az [az network nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) , a webkiszolgáló HTTP-forgalom engedélyezése (vagy módosítsa a saját igényeinek megfelelően, például az SSH-hozzáférést, vagy az adatbázis-kapcsolatok esetében). A következő példában létrehozunk egy nevű szabályt *myNetworkSecurityGroupRule* a TCP-forgalmat a 80-as port engedélyezése:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Hálózati biztonsági csoport alkalmazása a virtuális géphez
A virtuális gép hálózati adapteréhez (NIC) a hálózati biztonsági csoport társítása [az network nic update](/cli/azure/network/nic#az_network_nic_update). Ez a példa hozzárendeli egy meglévő hálózati Adaptert *myNic* nevű hálózati biztonsági csoporttal *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Azt is megteheti, hozzárendelheti a hálózati biztonsági csoportot a virtuális hálózat alhálózatának [az hálózati virtuális hálózat alhálózati frissítés](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) helyett csak a hálózati adapter egyetlen virtuális géphez. Az alábbi példa egy meglévő nevű alhálózatot társítja *mySubnet* a a *myVnet* nevű hálózati biztonsági csoportot a virtuális hálózat *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>További információ a hálózati biztonsági csoportok
A gyors parancsok lehetővé teszik elsajátíthatja a használatát az adatforgalom a virtuális géphez. Hálózati biztonsági csoportok számos nagyszerű funkciókkal és az erőforrásokhoz való hozzáférés szabályozása részletességgel adja meg. További információ [létrehozása egy hálózati biztonsági csoportok és ACL-szabályok itt](tutorial-virtual-network.md#secure-network-traffic).

A magas rendelkezésre állású webes alkalmazásokhoz helyezze a virtuális gépek az Azure Load Balancer mögött. A terheléselosztó elosztja a forgalmat, amely biztosítja a forgalom szűrése hálózati biztonsági csoport rendelkező virtuális gépek között. További információkért lásd: [betöltés Linux rendszerű virtuális gépek terheléselosztása az Azure-ban magas rendelkezésre állású alkalmazások létrehozásához](tutorial-load-balancer.md).

## <a name="next-steps"></a>További lépések
Ebben a példában létrehozott egy egyszerű HTTP-forgalmat engedélyező szabállyal. Részletesebb környezetek létrehozásáról a következő cikkekben talál:

* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
* [Mi az a hálózati biztonsági csoport (NSG)?](../../virtual-network/security-overview.md)
