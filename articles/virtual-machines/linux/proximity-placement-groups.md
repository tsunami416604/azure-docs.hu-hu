---
title: Proximity elhelyezési csoportok használata
description: Ismerje meg az Azure-beli virtuális gépek közelségi elhelyezési csoportjainak létrehozását és használatát.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: ee172203d6aa54b4b539356835f8a6bf2d21bad3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288412"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Virtuális gépek telepítése közelségi elhelyezési csoportokba az Azure CLI használatával

Ha a lehető legközelebb szeretné lekérni a virtuális gépeket, a lehető legkevesebb késést kell megvalósítania, egy [közelségi elhelyezési csoporton](co-location.md#proximity-placement-groups)belül kell telepítenie.

A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. A közelségi csoportok olyan munkaterhelések esetén hasznosak, ahol az alacsony késés követelmény.


## <a name="create-the-proximity-placement-group"></a>A közelségi elhelyezési csoport létrehozása
Hozzon létre egy közelségi elhelyezési csoportot a használatával [`az ppg create`](/cli/azure/ppg#az-ppg-create) . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Proximity elhelyezési csoportok listázása

Az [az PPG List](/cli/azure/ppg#az-ppg-list)paranccsal listázhatja az összes közelségi elhelyezési csoportot.

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a közelségi elhelyezési csoporton belül az [új az VM](/cli/azure/vm#az-vm-create)használatával.

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

A virtuális gépet az [az PPG show](/cli/azure/ppg#az-ppg-show)paranccsal tekintheti meg a közelségi csoportban.

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
A közelségi elhelyezési csoportban is létrehozhat egy rendelkezésre állási csoportot. Használja ugyanazt a `--ppg` paramétert az [az VM rendelkezésre állása-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) paranccsal, és hozzon létre egy rendelkezésre állási csoportot, és a rendelkezésre állási csoportban lévő összes virtuális gép is ugyanabban a közelségi elhelyezési csoportban lesz létrehozva.

## <a name="scale-sets"></a>Méretezési csoportok

Létrehozhat egy méretezési csoportot is a közelségi elhelyezési csoportban. Használja ugyanazt a `--ppg` paramétert az az [vmss Create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) paranccsal egy méretezési csoport létrehozásához, és az összes példány ugyanabban a közelségi elhelyezési csoportban lesz létrehozva.

## <a name="next-steps"></a>További lépések

További információ az [Azure CLI](/cli/azure/ppg) -parancsokról a Proximity elhelyezési csoportokhoz.
