---
title: Azure-beli virtuális gép operációsrendszer-lemezének felcserélése a CLI használatával | Microsoft Docs "
description: Módosítsa az Azure-beli virtuális gépek által használt operációsrendszer-lemezt a parancssori felület használatával.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 7b5f8a47b2b9c3692698b2ec6a7e5bc470b86a18
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091673"
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


Használja az az [VM Update](/cli/azure/vm#az-vm-update) -t a `--osdisk` paraméter új lemezének teljes erőforrás-azonosítójával. 

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
