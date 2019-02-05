---
title: Lapozófájl-kapacitás az operációs rendszer lemez egy Azure virtuális gép CLI-vel |} A Microsoft Docs
description: Az Azure virtuális géphez a CLI-vel által használt operációsrendszer-lemez módosítása
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
ms.openlocfilehash: b17647a09c88491e2486046b1ca99ee277f0cc28
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700213"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>A parancssori felület használata Azure virtuális gép által használt operációsrendszer-lemez módosítása


Ha rendelkezik egy meglévő virtuális Gépet, de szeretne váltani a lemezt egy biztonsági mentési lemez vagy egy másik operációsrendszer-lemez, az Azure CLI használatával az operációsrendszer-lemez felcserélése. Nem kell törölnie kell, és hozza létre újra a virtuális Gépet. Akkor is használhatja egy felügyelt lemezt egy másik erőforráscsoportban, mindaddig, amíg még nincs használatban.

A virtuális gép stopped\deallocated kell, majd a felügyelt lemez az erőforrás-Azonosítójára kicserélhető a különböző felügyelt lemez az erőforrás-azonosítója. 

Győződjön meg arról, hogy a lemez csatolásához kompatibilisek legyenek-e a virtuális gép méretét és a tároló típusa. Például ha a használni kívánt lemezt Premium Storage-ban, majd a virtuális gép kell lennie (például a DS sorozatú méretét) prémium szintű tárolás képes.

Ehhez a cikkhez az Azure CLI 2.0.25-ös vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 


Használat [az Lemezlista](/cli/azure/disk) , a lemezek listájának lekérése az erőforráscsoportban.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Használat [az vm stop](/cli/azure/vm) való stop\deallocate előtt érvényesítheti a lemezeket a virtuális gép.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Használat [az vm update](/cli/azure/vm#az-vm-update) a teljes erőforrás-azonosító, az új lemezt a `--osdisk` paraméter 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Indítsa újra a virtuális Gépet az [az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Következő lépések**

Lemez másolatának létrehozásához lásd: [lemez pillanatképének elkészítése](snapshot-copy-managed-disk.md).
