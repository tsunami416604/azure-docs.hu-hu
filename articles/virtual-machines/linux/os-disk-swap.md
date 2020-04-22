---
title: Csere az operációs rendszer lemezei között a CLI ' használatával
description: Módosítsa az Azure virtuális gép által használt operációsrendszer-lemezt a CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: a8c879ae56eb22d9817841667d33e37109a4f63d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759406"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Az Azure-gép által használt operációs rendszer lemezének módosítása a CLI használatával


Ha rendelkezik egy meglévő virtuális gép, de szeretné cserélni a lemezt egy biztonsági másolat lemez vagy egy másik operációs rendszer lemez, használhatja az Azure CLI az operációs rendszer lemezei felcserélése. Nem kell törölnie, és újra létre kell hoznia a virtuális gép. Egy felügyelt lemezt is használhat egy másik erőforráscsoportban, feltéve, hogy az még nincs használatban.

A virtuális gép nem kell állítani\felszabadított, majd az erőforrás-azonosító a felügyelt lemez lehet cserélni az erőforrás-azonosító egy másik felügyelt lemez. 

Győződjön meg arról, hogy a virtuális gép mérete és a tároló típusa kompatibilis a csatlakoztatni kívánt lemezzel. Ha például a használni kívánt lemez a prémium szintű storage-ban van, akkor a virtuális gépnek képesnek kell lennie a prémium szintű storage-ra (például egy DS-sorozat méretére).

Ez a cikk az Azure CLI 2.0.25-ös vagy újabb verzióját igényli. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 


Az [az lemezlista segítségével](/cli/azure/disk) leszeretné az erőforráscsoport lemezeinek listáját.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Használja [az vm stop](/cli/azure/vm) leállításához\felszabadítása a virtuális gép a lemezek cseréje előtt.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Az [vm-frissítés](/cli/azure/vm#az-vm-update) használata az új lemez teljes `--osdisk` erőforrásazonosítójával a paraméterhez 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Indítsa újra a virtuális gép segítségével [az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**További lépések**

Lemez másolatának létrehozásához olvassa el [a Lemez pillanatképe](snapshot-copy-managed-disk.md)című témakört.
