---
title: Lapozófájl-kapacitás az operációs rendszer lemez egy Azure virtuális gép parancssori felülettel |} Microsoft Docs
description: Módosítsa a parancssori felület használatával Azure virtuális gép által használt operációsrendszer-lemeze.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 1732b60ee135b765cdeea43f981bcef9575ff32c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Módosítsa a parancssori felület használatával, az Azure virtuális gép által használt operációsrendszer-lemez


Ha egy meglévő virtuális gép rendelkezik, de szeretne váltani a lemezt a biztonsági mentés lemezre vagy egy másik operációsrendszer-lemez, az Azure parancssori felület használatával az operációs rendszer lemezek. Törölje és hozza létre a virtuális gép nincs. Akkor is használhatja egy felügyelt lemezes egy másik erőforráscsoportban, mindaddig, amíg már nincs használatban.

A virtuális gép stopped\deallocated kell, majd az erőforrás-azonosítója a kezelt lemez az erőforrás-azonosítója egy másik felügyelt lemez lehet cserélni. 

Győződjön meg arról, hogy a virtuális gép mérete és a tárolási típus kompatibilisek a csatlakoztatni kívánt lemezt. Például ha a lemez szeretné használni a prémium szintű Storage, majd a virtuális gép kell lennie (például a DS sorozatnak megfelelő méretűre) prémium szintű Storage képes.

Ez a cikk igényel az Azure parancssori felület 2.0.25 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 


Használjon [az Lemezlista](/cli/azure/disk#list) listájának lemezt az erőforráscsoportban.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Használjon [az vm leállítása](/cli/azure/vm#stop) stop\deallocate előtt csere a lemezt a virtuális gép számára.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Használjon [az vm frissítése](/cli/azure/vm#az-vm-update) a teljes erőforrás-azonosító az új lemez a `--osdisk` paraméter 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Indítsa újra a virtuális gép használatával [az vm indítása](/cli/azure/vm#start).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Következő lépések**

A lemez egy példányát létrehozásához lásd: [pillanatkép-lemez](snapshot-copy-managed-disk.md).