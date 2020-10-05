---
title: Virtuális hálózat létrehozása – rövid útmutató – Azure CLI
titlesuffix: Azure Virtual Network
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatot az Azure CLI használatával. A virtuális hálózatok lehetővé teszik az Azure-erőforrások kommunikációját egymással és az internettel.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2019
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1feae201738a560c4cdb56f703c4af9a38af86d1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88056788"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Gyors útmutató: Virtuális hálózat létrehozása az Azure CLI-vel

A virtuális hálózatok lehetővé teszik az Azure-erőforrások, például a virtuális gépek (VM-EK) számára, hogy privát módon kommunikáljanak egymással és az internettel. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután kapcsolódhat a virtuális gépekhez az internetről, és magánjellegű módon kommunikálhat az új virtuális hálózaton keresztül.
## <a name="prerequisites"></a>Előfeltételek
Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verzióját kell használnia. A telepített verziójának megkereséséhez futtassa a parancsot `az --version` . További információ: az [Azure CLI telepítése](/cli/azure/install-azure-cli) a telepítéshez vagy a frissítéshez.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Erőforráscsoport és virtuális hálózat létrehozása

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Ez a példa létrehoz egy *myVirtualNetwork* nevű alapértelmezett virtuális hálózatot egy *alapértelmezett*nevű alhálózattal:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Ha az SSH-kulcsok még nem találhatók meg az alapértelmezett kulcs helyén, a parancs létrehozza azokat. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. Az `--no-wait` kapcsoló a háttérben hozza létre a virtuális gépet, így Ön eközben folytathatja a következő lépéssel. Ez a példa egy *myVm1*nevű virtuális gépet hoz létre:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Mivel az `--no-wait` előző lépésben használt beállítást használta, a *myVm2*nevű második virtuális gépet is létrehozhatja.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Azure CLI kimeneti üzenet

A virtuális gépek létrehozása néhány percet vesz igénybe. Miután az Azure létrehozta a virtuális gépeket, az Azure CLI a következőhöz hasonló kimenetet ad vissza:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
  "zones": ""
}
```

Jegyezze fel a **publicIpAddress** értékét. Ezt a lakcímet fogja használni a virtuális géphez az internetről a következő lépésben való kapcsolódáshoz.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Ebben a parancsban cserélje le a parancsot a `<publicIpAddress>` *myVm2* virtuális gép nyilvános IP-címére:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

A *myVm2* és a *myVm1* virtuális gépek közötti privát kommunikáció megerősítéséhez írja be a következő parancsot:

```bash
ping myVm1 -c 4
```

Négy választ fog kapni a *10.0.0.4*.

Lépjen ki a *myVm2* virtuális géppel folytatott SSH-munkamenetből.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az Group delete](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot és a hozzá tartozó összes erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Egy virtuális géphez kapcsolódott az internetről, és a két virtuális gép között magántulajdonban kommunikál.
Az Azure lehetővé teszi, hogy korlátlan privát kommunikációt biztosítson a virtuális gépek között. Alapértelmezés szerint az Azure csak a Windows rendszerű virtuális gépekről bejövő távoli asztali kapcsolatokat teszi lehetővé az internetről. A következő cikkből megtudhatja, hogyan konfigurálhat különböző típusú virtuálisgép-hálózati kommunikációt:
> [!div class="nextstepaction"]
> [Hálózati forgalom szűrése](tutorial-filter-network-traffic.md)
