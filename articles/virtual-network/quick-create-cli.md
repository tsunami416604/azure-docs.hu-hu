---
title: "Hozzon létre egy Azure virtuális hálózatra – az Azure CLI |} Microsoft Docs"
description: "Gyorsan bemutatják, hogyan hozzon létre egy virtuális hálózatot az Azure parancssori felület használatával. A virtuális hálózati lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépeket, közvetlenül kommunikálnak egymással, és az internetes."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 46fec48720c817072ce838dd2e4c07725be5a7fe
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Hozzon létre egy virtuális hálózatot az Azure parancssori felület használatával

Virtuális hálózat közvetlenül kommunikálnak egymással, és az internetes Azure-erőforrások, például a virtuális gépek (VM), lehetővé. Ebből a cikkből megismerheti, hogyan hozhat létre virtuális hálózatot. Virtuális hálózat létrehozása után a két virtuális gépeket létrehozni a virtuális hálózatban telepít. Ezután csatlakoztassa egy virtuális Gépet az internetről, és közvetlenül kommunikálnak a virtuális Gépet.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ez a cikk számára szükséges, hogy futnak-e az Azure parancssori felület 2.0.28 verzió vagy újabb. A telepített verzió megkereséséhez futtassa `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 


## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat tartalmaz az. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. Az alábbi példa létrehoz egy alapértelmezett nevű virtuális hálózatot *myVirtualNetwork* nevű egyetlen alhálózattal *alapértelmezett*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépek a virtuális hálózat:

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Ha SSH-kulcsok még nem léteznek a kulcs alapértelmezett helye, a parancs létrehozza azokat. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A `--no-wait` beállítás hoz létre a virtuális gép a háttérben, hogy továbbra is a következő lépéssel. Az alábbi példakód létrehozza a virtuális gépek nevű *myVm1*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A virtuális gép létrehozásához néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI-kimenet visszaadása a következőhöz hasonló: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Vegye figyelembe a **publicIpAddress**. Ez a cím segítségével csatlakoztassa a virtuális Gépet a következő lépésben az internetről.

## <a name="connect-to-a-vm-from-the-internet"></a>Csatlakoztassa a virtuális Gépet az internetről

Cserélje le `<publicIpAddress>` a nyilvános IP-címével a *myVm2* VM a parancsban az alábbiak szerint, és írja be a következő parancsot:

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-privately-between-vms"></a>Magánjellegű virtuális gépek közötti kommunikáció

Személyes kommunikációját megerősítéséhez a *myVm2* és *myVm1* virtuális gépeket, adja meg a következő parancsot:

```bash
ping myVm1 -c 4
```

A négy választ kap *10.0.0.4*.

Az SSH-munkamenetet, a kilépéshez a *myVm2* virtuális gép.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [az csoport törlése](/cli/azure/group#az_group_delete) eltávolítása az erőforráscsoport és az összes olyan erőforrást tartalmaz:

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy alapértelmezett virtuális hálózat és két virtuális gépeket. Egy virtuális gép csatlakozik az interneten, és közvetlenül a Microsoftnak továbbítani a virtuális gép és egy másik virtuális gép között. Virtuális hálózati beállításaival kapcsolatos további tudnivalókért lásd: [egy virtuális hálózat kezeléséhez](manage-virtual-network.md). 

Alapértelmezés szerint az Azure lehetővé teszi, hogy a virtuális gépek korlátlan titkos kommunikációját, de csak lehetővé teszi a bejövő SSH-munkamenetet a Linux virtuális gépekhez az internetről. Annak megismerése, hogyan engedélyezésére vagy korlátozására a különböző hálózati kommunikációs felé és felől a virtuális gépek, a következő oktatóanyag továbblépés.

> [!div class="nextstepaction"]
> [Hálózati forgalom szűrésére](virtual-networks-create-nsg-arm-cli.md)
