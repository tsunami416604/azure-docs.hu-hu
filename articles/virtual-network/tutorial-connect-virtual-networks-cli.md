---
title: Virtuális hálózatok összekapcsolása virtuális hálózatok közötti társviszony létesítése – Azure CLI-vel |} A Microsoft Docs
description: Ebből a cikkből elsajátíthatja a virtuális hálózatok összekapcsolása virtuális hálózati társviszony-létesítés, az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 29ab957e97c6aa57be6192e6ee4d86fe642ae95d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38307856"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Virtuális hálózatok összekapcsolása virtuális hálózatok közötti társviszony az Azure CLI használatával

A virtuális hálózatok közötti társviszony létesítésével virtuális hálózatokat kapcsolhat össze egymással. Ha a társviszony létrejött, a két virtuális hálózaton található erőforrások ugyanolyan késés és sávszélesség mellett kommunikálhatnak egymással, mintha ugyanazon a virtuális hálózaton lennének. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Két virtuális gép létrehozása
* Két virtuális hálózat összekapcsolása virtuális hálózatok közötti társviszony létesítésével
* Virtuális gép üzembe helyezése mindkét virtuális hálózaton
* Virtuális gépek közötti kommunikáció

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület, ez a cikk megköveteli, hogy futnak-e az Azure CLI 2.0.28-as vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

Előtt egy virtuális hálózatot hoz létre, akkor hozzon létre egy erőforráscsoportot a virtuális hálózatot és más ebben a cikkben létrehozott összes erőforrást. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. A következő példában létrehozunk egy nevű virtuális hálózatot *myVirtualNetwork1* a címelőtaggal rendelkező *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Hozzon létre egy virtuális hálózatot nevű *myVirtualNetwork2* a címelőtaggal rendelkező *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Virtuális hálózatok közötti társviszony létesítése

Virtuális hálózati azonosítókat, ezért először kérje le az egyes virtuális hálózat azonosítója között jönnek létre társviszony-Létesítéseket [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) és Azonosítóját tárolja egy változóban.

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

A társviszony-létesítés *myVirtualNetwork1* való *myVirtualNetwork2* a [az hálózat virtuális hálózatok közötti társviszony létrehozása](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Ha a `--allow-vnet-access` paraméter nincs megadva, egy társviszony jön létre, de nem érkeztek is áthaladhat azt.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

Vissza az előző parancs végrehajtása után a kimenetben láthatja a **peeringState** van *kezdeményezve*. A társviszony-létesítési marad a *kezdeményezve* állapot, amíg nem hoz létre a társviszonyt *myVirtualNetwork2* való *myVirtualNetwork1*. A társviszony-létesítés *myVirtualNetwork2* való *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

Vissza az előző parancs végrehajtása után a kimenetben láthatja a **peeringState** van *csatlakoztatva*. Az Azure is módosította a társviszony-létesítés állapota a *myVirtualNetwork1 – myVirtualNetwork2* a társviszony-létesítési *csatlakoztatva*. Ellenőrizze, hogy a társviszony-létesítés állapota a *myVirtualNetwork1 – myVirtualNetwork2* társviszony-létesítés változott *csatlakoztatva* a [az network vnet peering show](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Amíg a többi virtuális hálózatban lévő erőforrásokra erőforrásaikat egy virtuális hálózatot nem lehet kommunikálni a **peeringState** tartozó mindkét virtuális hálózatok társviszony-létesítések *csatlakoztatva*. 

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre egy virtuális gépet az egyes virtuális hálózatokon, hogy kommunikációt létesíthessen közöttük egy későbbi lépésben.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. A következő példában létrehozunk egy nevű virtuális Gépet *myVm1* a a *myVirtualNetwork1* virtuális hálózatot. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A `--no-wait` lehetőség a háttérben létrehozza a virtuális Gépet, így a következő lépéssel is.

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

A virtuális gép létrehozása a *myVirtualNetwork2* virtuális hálózatot.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI információkat jelenít meg az alábbi példához hasonló: 

```azurecli 
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

Jegyezze fel a **publicIpAddress** értékét. Ezzel a címmel eléri a virtuális Gépet egy későbbi lépésben az internetről.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

A következő paranccsal hozhat létre az SSH-munkamenetből a *myVm2* virtuális Gépet. Cserélje le `<publicIpAddress>` a virtuális gép nyilvános IP-címét. Az előző példában a nyilvános IP-cím van *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

A virtuális gép pingelni *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Ekkor négy választ kap. 

Zárja be az SSH-munkamenetet, hogy a *myVm2* virtuális Gépet. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, [az csoport törlése](/cli/azure/group#az_group_delete) , távolítsa el az erőforráscsoportot és az összes benne található erőforrást.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan virtuális hálózatok közötti társviszony az azonos Azure-régióban, két hálózat kapcsolódni. Más [támogatott régiókban](virtual-network-manage-peering.md#cross-region) és [különböző Azure-előfizetésekben](create-peering-different-subscriptions.md#cli) található virtuális hálózatok között is létesíthet társviszonyt, illetve a társviszony létesítésével [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) is létrehozhat. További információ a virtuális hálózatok közötti társviszony létesítéséről: [Virtuális hálózatok közötti társviszony létesítésének áttekintése](virtual-network-peering-overview.md) és[Virtuális hálózatok közötti társviszonyok kezelése](virtual-network-manage-peering.md).

Is [csatlakoztatni szeretné saját számítógépét egy virtuális hálózathoz](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VPN,-kapcsolaton keresztül, és lépjen kapcsolatba az erőforrásokkal egy virtuális hálózaton, vagy a társviszonyban álló virtuális hálózatba. Lásd: a virtuális hálózat cikkekben ismertetett feladatok végrehajtásához újrafelhasználható szkripteket [mintaszkriptjeire](cli-samples.md).