---
title: Virtuális hálózatok csatlakoztatása virtuális hálózatok virtuális hálózatlétesítéssel – Azure CLI
description: Ebben a cikkben megtudhatja, hogyan csatlakozhat a virtuális hálózatok virtuális hálózati társviszony-létesítés, az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: aa2d75173b14e768a207336b54b3dc10a8c3ea5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235170"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Virtuális hálózatok csatlakoztatása virtuális hálózati társviszony-létesítéssel az Azure CLI használatával

A virtuális hálózatok közötti társviszony létesítésével virtuális hálózatokat kapcsolhat össze egymással. Ha a társviszony létrejött, a két virtuális hálózaton található erőforrások ugyanolyan késés és sávszélesség mellett kommunikálhatnak egymással, mintha ugyanazon a virtuális hálózaton lennének. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Két virtuális gép létrehozása
* Két virtuális hálózat összekapcsolása virtuális hálózatok közötti társviszony létesítésével
* Virtuális gép üzembe helyezése mindkét virtuális hálózaton
* Virtuális gépek közötti kommunikáció

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a cikk megköveteli, hogy az Azure CLI 2.0.28-as vagy újabb verzióját futassza. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

Virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózathoz és a cikkben létrehozott összes többi erőforráshoz. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. A következő példa létrehoz egy *myVirtualNetwork1* nevű virtuális hálózatot a *10.0.0.0/16*címelőtaggal.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Hozzon létre egy virtuális hálózat nevű *myVirtualNetwork2* a cím előtag *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Virtuális hálózatok közötti társviszony létesítése

Társviszony-létesítések között létrehozott virtuális hálózati azonosítók, így először be kell szereznie az azonosítót minden virtuális hálózat [az hálózati virtuális hálózat show](/cli/azure/network/vnet) és tárolja az azonosítót egy változóban.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Hozzon létre egy társviszony-létesítést *a myVirtualNetwork1* és *a myVirtualNetwork2* között [az hálózati virtuális társviszony-létesítési létrehozásával.](/cli/azure/network/vnet/peering) Ha `--allow-vnet-access` a paraméter nincs megadva, létrejön egy társviszony-létesítés, de nem folyhat át a kommunikáció.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

Az előző parancs végrehajtása után visszaadott kimenetben láthatja, hogy a **társviszony-létesítés** *kezdeményezése .* A társviszony-létesítés *a kezdeményezett* állapotban marad, amíg létre nem hozza a társviszony-létesítést a *myVirtualNetwork2* és *a myVirtualNetwork1*között. Hozzon létre egy társviszony-létesítést a *myVirtualNetwork2* és a *myVirtualNetwork1*között. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

Az előző parancs végrehajtása után visszaadott kimenetben láthatja, hogy a **peeringState** *csatlakoztatva*van. Az Azure módosította a *myVirtualNetwork1-myVirtualNetwork2* társviszony-létesítési állapotát *is.* Győződjön meg arról, hogy a *myVirtualNetwork1-myVirtualNetwork2* társviszony-létesítési állapota az [az hálózati vnet társviszony-létesítési show-ra](/cli/azure/network/vnet/peering)változott. *Connected*

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Az egyik virtuális hálózat erőforrásai nem tudnak kommunikálni a másik virtuális hálózat erőforrásaival, amíg a **társviszony-létesítési állapot** mindkét virtuális hálózatban *nincs csatlakoztatva.* 

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre egy virtuális gépet az egyes virtuális hálózatokon, hogy kommunikációt létesíthessen közöttük egy későbbi lépésben.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. A következő példa létrehoz egy *myVm1* nevű virtuális gépet a *myVirtualNetwork1* virtuális hálózatban. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A `--no-wait` beállítás létrehozza a virtuális gép a háttérben, így továbbra is a következő lépés.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Hozzon létre egy virtuális gépet a *myVirtualNetwork2* virtuális hálózatban.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI a következő példához hasonló információkat jelenít meg: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Jegyezze fel a **publicIpAddress** értékét. Ez a cím egy későbbi lépésben az internetről való eléréséhez használható.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

A következő paranccsal ssh munkamenetet hozhat létre a *myVm2* virtuális géppel. Cserélje `<publicIpAddress>` le a virtuális gép nyilvános IP-címét. Az előző példában a nyilvános IP-cím *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Pingelje a virtuális gépet a *myVirtualNetwork1-ben.*

```bash
ping 10.0.0.4 -c 4
```

Négy választ kap. 

Zárja be az SSH-munkamenetet a *myVm2* virtuális géphez. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az csoport törlésével](/cli/azure/group) távolítsa el az erőforráscsoportot és az összes benne lévő erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan csatlakoztathat két hálózatot ugyanabban az Azure-régióban, virtuális hálózati társviszony-létesítéssel. Más [támogatott régiókban](virtual-network-manage-peering.md#cross-region) és [különböző Azure-előfizetésekben](create-peering-different-subscriptions.md#cli) található virtuális hálózatok között is létesíthet társviszonyt, illetve a társviszony létesítésével [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) is létrehozhat. További információ a virtuális hálózatok közötti társviszony létesítéséről: [Virtuális hálózatok közötti társviszony létesítésének áttekintése](virtual-network-peering-overview.md) és[Virtuális hálózatok közötti társviszonyok kezelése](virtual-network-manage-peering.md).

A saját számítógépét VPN-en keresztül [csatlakoztathatja egy virtuális hálózathoz,](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és kommunikálhat a virtuális hálózat ban vagy társviszonyban lévő virtuális hálózatok erőforrásaival. Az újrafelhasználható parancsfájlok a virtuális hálózati cikkekben szereplő feladatok nagy részének elvégzéséhez lásd a [parancsfájlmintákat.](cli-samples.md)
