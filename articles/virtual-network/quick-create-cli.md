---
title: Virtuális hálózat létrehozása - rövid útmutató - Azure CLI
titlesuffix: Azure Virtual Network
description: Ebben a rövid útmutatóban megtudhatja, hogy hozzon létre egy virtuális hálózatot az Azure CLI használatával. A virtuális hálózat lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek, kommunikáljanak egymással és az internettel.
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
ms.openlocfilehash: 2a4c63aeaa303692fa0f2d115a3df0d80cfab0b1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80235193"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Gyors útmutató: Virtuális hálózat létrehozása az Azure CLI-vel

A virtuális hálózat lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek (VM-ek), privát kommunikációt kommunikáljanak egymással és az internettel. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután csatlakozik a virtuális gépekhez az internetről, és privát módon kommunikálaz új virtuális hálózaton keresztül.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy az Azure CLI-t helyileg telepíti és használja, ez a rövid útmutató az Azure CLI 2.0.28-as vagy újabb verzióját kell használnia. A telepített verzió megkereséséhez futtassa a futtassa a futtassa a futtassa a futtassa a program `az --version` A telepítési vagy frissítési információkért tekintse meg az [Azure CLI telepítése](/cli/azure/install-azure-cli) című témakört.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Erőforráscsoport és virtuális hálózat létrehozása

Virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Ez a példa létrehoz egy alapértelmezett virtuális hálózatot *myVirtualNetwork* néven egy *alapértelmezett*nevű alhálózattal:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Ha az SSH-kulcsok még nem léteznek az alapértelmezett kulcshelyen, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. Az `--no-wait` kapcsoló a háttérben hozza létre a virtuális gépet, így Ön eközben folytathatja a következő lépéssel. Ez a példa létrehoz egy *myVm1*nevű virtuális gép:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Mivel az `--no-wait` előző lépésben használta a lehetőséget, előre léphet, és létrehozhatja a második virtuális gép *myVm2*nevű nevét.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Azure CLI kimeneti üzenet

A virtuális gépek létrehozása néhány percet vesz igénybe. Miután az Azure létrehozza a virtuális gépeket, az Azure CLI a következővel adja vissza a kimenetet:

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

Jegyezze fel a **publicIpAddress** értékét. Ezt a címet fogja használni, hogy csatlakozzon a virtuális géphez az internetről a következő lépésben.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Ebben a parancsban cserélje le `<publicIpAddress>` a *myVm2* virtuális gép nyilvános IP-címét:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

A *myVm2* és a *myVm1* virtuális gépek közötti privát kommunikáció megerősítéséhez írja be ezt a parancsot:

```bash
ping myVm1 -c 4
```

Négy választ kap a *10.0.0.4-től.*

Lépjen ki a *myVm2* virtuális géppel folytatott SSH-munkamenetből.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az csoport törlésével](/cli/azure/group) eltávolíthatja az erőforráscsoportot és az összes erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Ön az internetről egy virtuális géphez csatlakozott, és a két virtuális gép között privát módon kommunikált. További információ a virtuális hálózatok beállításairól: [Virtuális hálózat kezelése](manage-virtual-network.md).

Az Azure lehetővé teszi a virtuális gépek közötti korlátlan privát kommunikációt. Alapértelmezés szerint az Azure csak az internetről engedi be a Windows virtuális gépekhez érkező távoli asztali kapcsolatokat. Ha többet szeretne megtudni a különböző típusú virtuális gép hálózati kommunikáció konfigurálásáról, látogasson el a [Hálózati forgalom szűrése](tutorial-filter-network-traffic.md) oktatóanyag.
