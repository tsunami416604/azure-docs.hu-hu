---
title: Hozzon létre egy virtuális hálózatot – rövid útmutató – Azure CLI-vel
titlesuffix: Azure Virtual Network
description: Ebben a rövid útmutatóban megismerheti az Azure CLI használatával egy virtuális hálózat létrehozásához. Virtuális hálózat lehetővé teszi, hogy az Azure-erőforrások, például virtuális gépeket, privát módon kommunikáljanak egymással, és az interneten.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: jdial
ms.openlocfilehash: 8e46058432743febd70a017ee5131081a052364b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195392"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Gyors útmutató: Hozzon létre egy virtuális hálózatot az Azure CLI használatával

Virtuális hálózat lehetővé teszi, hogy az Azure-erőforrások, például virtuális gépeket (VM), hogy privát módon kommunikáljanak egymással, és az internettel. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután csatlakozhat a virtuális gépekhez az internetről, és privát módon kommunikáljanak a új virtuális hálózaton keresztül.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, telepítése és használata az Azure CLI helyileg, ez a rövid útmutató előírja, hogy az Azure CLI 2.0.28-as vagy újabb. A telepített verzió megkereséséhez futtassa `az --version`. Lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli) telepítési vagy frissítési adatai.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Hozzon létre egy erőforráscsoportot és a egy virtuális hálózatot

Mielőtt létrehozhatna egy virtuális hálózatot, akkor hozzon létre egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Ez a példa létrehoz egy erőforráscsoportot, nevű *myResourceGroup* a a *eastus* helye:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Ez a példa létrehoz egy alapértelmezett nevű virtuális hálózatot *myVirtualNetwork* nevű alhálózattal rendelkező *alapértelmezett*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. Az `--no-wait` kapcsoló a háttérben hozza létre a virtuális gépet, így Ön eközben folytathatja a következő lépéssel. Ez a példa létrehoz egy virtuális gép nevű *myVm1*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Mivel követte a `--no-wait` lehetőséget az előző lépésben, lépjen tovább és hozhat létre a második virtuális gép nevű *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Az Azure CLI-kimenet üzenet

A virtuális gépek létrehozása néhány percet vesz igénybe. Miután az Azure létrehozza a virtuális gépek, az Azure CLI a következőhöz hasonló kimenetnek adja vissza:

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Jegyezze fel a **publicIpAddress** értékét. Ez a cím használatával fog csatlakozni a virtuális Géphez az internetről, a következő lépésben.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Ebben a parancsban cserélje le a `<publicIpAddress>` a nyilvános IP-címét a *myVm2* virtuális Géphez:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

Ellenőrizze a közötti privát kommunikációt a *myVm2* és *myVm1* virtuális gépek esetében adja meg ezt a parancsot:

```bash
ping myVm1 -c 4
```

A négy választ kap *10.0.0.4*.

Lépjen ki a *myVm2* virtuális géppel folytatott SSH-munkamenetből.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhat [az csoport törlése](/cli/azure/group) , távolítsa el az erőforráscsoportot és az összes rendelkezik:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Egy virtuális géphez az internetről csatlakozik, és privát kommunikációt a két virtuális gépet. További információ a virtuális hálózatok beállításairól: [Virtuális hálózat kezelése](manage-virtual-network.md).

Az Azure lehetővé teszi, hogy a virtuális gépek korlátlan privát kommunikációját. Alapértelmezés szerint az Azure csak lehetővé teszi a bejövő távoli asztali kapcsolatok Windows virtuális gépek az interneten. Virtuális gép hálózati kommunikációt a különböző típusú konfigurálásával kapcsolatos további tudnivalókért keresse fel a [hálózati forgalom szűrése](tutorial-filter-network-traffic.md) oktatóanyag.
