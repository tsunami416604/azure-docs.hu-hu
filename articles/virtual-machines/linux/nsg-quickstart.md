---
title: Portok megnyitása virtuális géphez az Azure CLI-vel
description: Ismerje meg, hogyan nyithat meg egy portot / hozzon létre egy végpontot a virtuális géphez az Azure CLI használatával.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: c29fb075fc2d8b512070d7a6cf3fef949def5894
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066632"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Portok és végpontok megnyitása virtuális géphez az Azure CLI-vel

Megnyit egy portot, vagy hozzon létre egy végpontot egy virtuális gép (VM) az Azure-ban egy hálózati szűrő létrehozása egy alhálózaton vagy virtuális gép hálózati adapteren. Ezeket a szűrőket, amelyek a bejövő és a kimenő forgalmat egyaránt vezérelik, a forgalmat fogadó erőforráshoz kapcsolódó hálózati biztonsági csoportra helyezi. Használjunk egy gyakori példát a 80-as port webes forgalmára. Ez a cikk bemutatja, hogyan nyithat meg egy portot egy virtuális gép hez az Azure CLI-vel. 


Hálózati biztonsági csoport és szabályok létrehozásához a legújabb [Azure CLI-t](/cli/azure/install-az-cli2) kell telepíteni, és az az bejelentkezéssel be kell jelentkeznie egy Azure-fiókba. [az login](/cli/azure/reference-index)

A következő példákban cserélje le a példaparaméterneveket a saját értékeire. Példa paraméter nevek közé *tartozik a myResourceGroup*, *myNetworkSecurityGroup*, és *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Gyorsan megnyithat egy portot virtuális géphez
Ha gyorsan meg kell nyitnia egy virtuális gép portját egy fejlesztési és tesztelési forgatókönyvben, használhatja az [az vm open-port](/cli/azure/vm) parancsot. Ez a parancs létrehoz egy hálózati biztonsági csoportot, hozzáad egy szabályt, és alkalmazza azt egy virtuális gépre vagy alhálózatra. A következő példa megnyitja a *80-as* portot a myResourceGroup nevű erőforráscsoport *myVM* nevű virtuális *gépén.*

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

A szabályok további szabályozása, például a forrás IP-címtartomány definiálása érdekében folytassa a cikkben ismertetett további lépésekkel.


## <a name="create-a-network-security-group-and-rules"></a>Hálózati biztonsági csoport és szabályok létrehozása
Hozza létre a hálózati biztonsági csoportot az [az network nsg create](/cli/azure/network/nsg)segítségével. A következő példa létrehoz egy *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot az *eastus* helyen:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adjon hozzá egy szabályt [az az hálózati nsg szabály létrehozása](/cli/azure/network/nsg/rule) http-forgalom a webkiszolgáló (vagy módosítani a saját forgatókönyv, például SSH-hozzáférés vagy adatbázis-kapcsolat). A következő példa létrehoz egy *myNetworkSecurityGroupRule* nevű szabályt, amely engedélyezi a TCP-forgalmat a 80-as porton:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Hálózati biztonsági csoport alkalmazása a virtuális gépre
Társítsa a hálózati biztonsági csoportot a virtuális gép hálózati adapteréhez (NIC) az [az hálózati nic frissítéssel.](/cli/azure/network/nic) A következő példa egy *myNic* nevű meglévő hálózati adaptert társít a *myNetworkSecurityGroup*nevű hálózati biztonsági csoporthoz:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Azt is megteheti, hogy a hálózati biztonsági csoportot egy virtuális hálózati alhálózathoz társítja [az az hálózati virtuális hálózat alhálózati frissítésével,](/cli/azure/network/vnet/subnet) nem csak a virtuális gép hálózati adapteréhez. A következő példa egy *myVnet* virtuális hálózatban lévő *mySubnet* nevű meglévő alhálózatot társít a *myNetworkSecurityGroup*nevű hálózati biztonsági csoporthoz:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>További információ a hálózati biztonsági csoportokról
A gyors parancsok itt lehetővé teszi, hogy a virtuális gépre áramló forgalommal való üzembe futást. A hálózati biztonsági csoportok számos nagyszerű szolgáltatást és részletességet biztosítanak az erőforrásokhoz való hozzáférés szabályozásához. A hálózati biztonsági csoport és az [ACL-szabályok létrehozásáról itt olvashat bővebben.](tutorial-virtual-network.md#secure-network-traffic)

A magas rendelkezésre állású webes alkalmazások esetén a virtuális gépeket egy Azure Load Balancer mögé kell helyeznie. A terheléselosztó forgalmat oszt ki a virtuális gépek, a hálózati biztonsági csoport, amely a forgalom szűrése. További információ: [Linux-alapú virtuális gépek terheléselosztása az Azure-ban egy magas rendelkezésre állású alkalmazás létrehozásához.](tutorial-load-balancer.md)

## <a name="next-steps"></a>További lépések
Ebben a példában létrehozott egy egyszerű szabályt, amely engedélyezi a HTTP-forgalmat. A részletesebb környezetek létrehozásáról az alábbi cikkekben talál információt:

* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md)
* [Mi az a hálózati biztonsági csoport (NSG)?](../../virtual-network/security-overview.md)
