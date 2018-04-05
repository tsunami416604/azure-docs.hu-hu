---
title: Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony - Azure parancssori Felülettel |} Microsoft Docs
description: Ebből a cikkből megtanulhatja a virtuális hálózatok kapcsolódni a virtuális hálózati társviszony-létesítést, az Azure parancssori felület használatával.
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
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony-létesítést az Azure parancssori felület használatával

Kapcsolódás virtuális hálózatok egymástól a virtuális hálózati társviszony-létesítés. Virtuális hálózatok vannak társviszonyban, ha mindkét virtuális hálózat erőforrásainak képesek kommunikálnak egymással, ugyanahhoz késés és a sávszélesség, mintha az erőforrásokat ugyanabban a virtuális hálózatban. Ebből a cikkből megismerheti, hogyan:

* Két virtuális hálózatok létrehozása
* A virtuális hálózati társviszony-létesítés két virtuális hálózatok csatlakoztatása
* Virtuális gép (VM) telepítése minden virtuális hálózathoz
* Virtuális gépek közötti kommunikáció

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ez a cikk számára szükséges, hogy futnak-e az Azure parancssori felület 2.0.28 verzió vagy újabb. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

Virtuális hálózat létrehozása előtt hozzon létre egy erőforráscsoportot a virtuális hálózat, és ez a cikk létrehozott összes többi erőforrása van. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVirtualNetwork1* a címelőtaggal rendelkező *10.0.0.0/16*.

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

## <a name="peer-virtual-networks"></a>Társ virtuális hálózatok

Társviszony létrehozása történik, virtuális hálózat azonosítója, ezért be kell szereznie az egyes virtuális hálózati Azonosítóját közötti [az hálózati vnet show](/cli/azure/network/vnet#az_network_vnet_show) és az azonosító tárolható egy változóban.

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

Hozzon létre a társviszony *myVirtualNetwork1* való *myVirtualNetwork2* rendelkező [létrehozása az hálózati vnetben társviszony-létesítés](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Ha a `--allow-vnet-access` paraméter nincs megadva, a társviszony-létesítés létrejött, de nem érkeztek áramolhasson rajta.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

Adott vissza a korábbi parancs futtatása után a kimenetben megjelenik az **peeringState** van *kezdeményezett*. A társviszony-létesítési marad a *kezdeményezett* állapot, amíg nem hoz létre a társviszony *myVirtualNetwork2* való *myVirtualNetwork1*. Hozzon létre a társviszony *myVirtualNetwork2* való *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

Adott vissza a korábbi parancs futtatása után a kimenetben megjelenik az **peeringState** van *csatlakoztatva*. Azure társviszony-létesítési állapota megváltozik a *myVirtualNetwork1-myVirtualNetwork2* társviszony-létesítési való *csatlakoztatva*. Ellenőrizze, hogy a társviszony-létesítési állapota a *myVirtualNetwork1-myVirtualNetwork2* társviszony-létesítés változott *csatlakoztatva* rendelkező [az hálózati vnetben társviszony-létesítési megjelenítése](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Amíg a többi virtuális hálózatán lévő erőforrásokat az egyik nem tud kommunikálni a virtuális hálózati erőforrásokhoz a **peeringState** mindkét virtuális hálózatok esetében: *csatlakoztatva*. 

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Virtuális gép létrehozása minden egyes virtuális hálózatban, hogy egy későbbi lépésben közötti kommunikációt.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Az alábbi példakód létrehozza a virtuális gépek nevű *myVm1* a a *myVirtualNetwork1* virtuális hálózat. Ha SSH-kulcsok még nem léteznek a kulcs alapértelmezett helye, a parancs létrehozza azokat. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A `--no-wait` beállítás hoz létre a virtuális Gépet a háttérben, így továbbra is a következő lépéssel.

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

A virtuális gép létrehozása a *myVirtualNetwork2* virtuális hálózat.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

A virtuális gép létrehozásához néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure parancssori felület információkat jeleníti meg az alábbi példához hasonló: 

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

Vegye figyelembe a **publicIpAddress**. Ez a cím a virtuális gép egy későbbi lépésben az internetről való eléréséhez használt.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

Az SSH-munkamenetet létrehozni, az alábbi parancs segítségével a *myVm2* virtuális gép. Cserélje le `<publicIpAddress>` a virtuális gép a nyilvános IP-címmel. Az előző példában a nyilvános IP-cím van *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Pingelje meg a virtuális gép *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Négy választ kap. 

Zárja be az SSH-munkamenetet a *myVm2* virtuális gép. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [az csoport törlése](/cli/azure/group#az_group_delete) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, azonos Azure-régióban, két hálózat kapcsolódás a virtuális hálózati társviszony-létesítés. Virtuális hálózatok, a másik partnert is meg is [támogató régiók](virtual-network-manage-peering.md#cross-region) és a [különböző Azure-előfizetések](create-peering-different-subscriptions.md#cli), valamint létrehozása [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) rendelkező társviszony-létesítés. Virtuális hálózati társviszony-létesítés kapcsolatos további információkért lásd: [virtuális hálózati társviszony-létesítési áttekintése](virtual-network-peering-overview.md) és [kezelheti a virtuális hálózati társviszony](virtual-network-manage-peering.md).

Is [saját számítógép csatlakoztatása egy virtuális hálózati](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) egy VPN-en keresztül és a virtuális hálózatot, vagy nincsenek társviszonyban, virtuális hálózatok erőforrások. Tekintse meg a virtuális hálózati cikkekben ismertetett feladatok végrehajtásához, újrafelhasználható parancsfájlok [parancsfájl-mintában](cli-samples.md).