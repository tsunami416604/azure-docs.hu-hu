---
title: OPERÁCIÓSRENDSZER-lemezek cseréje a CLI használatával
description: Módosítsa az Azure-beli virtuális gépek által használt operációsrendszer-lemezt a parancssori felület használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: adcbc5b0ba0056c79576e8d4c8ded2150b206513
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87371872"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Azure-beli virtuális gép által használt operációsrendszer-lemez módosítása a parancssori felület használatával


Ha rendelkezik meglévő virtuális géppel, de a lemezt egy biztonsági mentési lemez vagy egy másik operációsrendszer-lemez esetében szeretné cserélni, az Azure CLI használatával cserélheti le az operációsrendszer-lemezeket. Nem kell törölnie és újból létrehoznia a virtuális gépet. Akár felügyelt lemezt is használhat egy másik erőforráscsoporthoz, ha még nincs használatban.

A virtuális gépnek stopped\deallocated kell lennie, a felügyelt lemez erőforrás-AZONOSÍTÓját lecserélheti egy másik felügyelt lemez erőforrás-azonosítójával. 

Győződjön meg arról, hogy a virtuális gép mérete és a tároló típusa kompatibilis-e a csatolni kívánt lemezzel. Ha például a használni kívánt lemez Premium Storage, akkor a virtuális gépnek képesnek kell lennie Premium Storage (például egy DS-sorozat méretének).

Ehhez a cikkhez az Azure CLI 2.0.25 vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 


Az az [Disk List](/cli/azure/disk) paranccsal kérheti le az erőforráscsoport lemezeinek listáját.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Az az [VM stop](/cli/azure/vm) paranccsal stop\deallocate a virtuális gépet a lemezek cseréje előtt.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Használja az az [VM Update](/cli/azure/vm#az-vm-update) -t a paraméter új lemezének teljes erőforrás-azonosítójával. `--osdisk` 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Indítsa újra a virtuális gépet az [az VM Start](/cli/azure/vm)paranccsal.

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Következő lépések**

Lemez másolatának létrehozásához tekintse meg a [lemez pillanatképe](snapshot-copy-managed-disk.md)című témakört.
