---
title: Közelségelhelyezési csoportok használata Linuxos virtuális gépekhez
description: Ismerje meg a közelségelhelyezési csoportok létrehozását és használatát Linuxos virtuális gépekhez az Azure-ban.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 353a266b647c299515c15889c302ba4409aa511b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73171056"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Virtuális gépek telepítése közelségi elhelyezési csoportokba az Azure CLI használatával

Ahhoz, hogy a virtuális gépek a lehető legközelebb legyenek, és a lehető legalacsonyabb késést érjeel el, telepítenie kell őket egy [közelségelhelyezési csoporton](co-location.md#proximity-placement-groups)belül.

A közelségelhelyezési csoport egy logikai csoportosítás, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag egymáshoz közel helyezkednek el. A közelségelhelyezési csoportok olyan számítási feladatokhoz hasznosak, ahol az alacsony késés követelmény.


## <a name="create-the-proximity-placement-group"></a>A közelségelhelyezési csoport létrehozása
Közelségelhelyezési csoport [`az ppg create`](/cli/azure/ppg#az-ppg-create)létrehozása a használatával. 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Közelségi elhelyezési csoportok listázása

Az [az ppg list segítségével](/cli/azure/ppg#az-ppg-list)az összes közelségi elhelyezési csoportot listázhatja.

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gép a közelség elhelyezési csoporton belül [az új az vm](/cli/azure/vm#az-vm-create)használatával.

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

A virtuális gép a közelségelhelyezési csoportban az [ppg show](/cli/azure/ppg#az-ppg-show)használatával látható.

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
A közelségi elhelyezési csoportban is létrehozhat egy rendelkezésre állási csoportot. Használja ugyanazt `--ppg` a paramétert [az az vm rendelkezésre állási készlet létrehozása](/cli/azure/vm/availability-set#az-vm-availability-set-create) egy rendelkezésre állási csoport létrehozása, és az összes virtuális gép a rendelkezésre állási csoportban is létre jön ugyanabban a közelség elhelyezési csoportban.

## <a name="scale-sets"></a>Méretezési csoportok

Méretezési csoportot is létrehozhat a közelségi elhelyezési csoportban. Használja ugyanazt `--ppg` a paramétert [az az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) egy méretezési csoport létrehozásához, és az összes példány jön létre ugyanabban a közelség elhelyezési csoportban.

## <a name="next-steps"></a>További lépések

További információ az [Azure CLI-parancsok](/cli/azure/ppg) közelségi elhelyezési csoportok.
