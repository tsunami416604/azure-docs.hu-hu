---
title: Linux virtuális gép és az Azure CLI 2.0 átméretezése |} Microsoft Docs
description: Hogyan növelheti vagy csökkentheti a Linux virtuális gépek, a Virtuálisgép-méretet módosításával.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5c396ad047567799b7541c1a125de0c1c51c39e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="resize-a-linux-virtual-machine-using-cli-20"></a>A Linux virtuális gépek CLI 2.0 átméretezése

(VM) virtuális gép kiépítése után méretezheti a virtuális gép felfelé vagy lefelé módosításával a [Virtuálisgép-méretet][vm-sizes]. Néhány esetben először a virtuális gép kell felszabadítani. Szeretné felszabadítani a virtuális gép, ha a kívánt méretet a hardver fürtön, amelyen a virtuális gép nem érhető el. Ez a cikk a Linux virtuális gép és az Azure CLI 2.0 átméretezése részletezi. Az [Azure CLI 1.0-s](change-vm-size-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) verziójával is elvégezheti ezeket a lépéseket.

## <a name="resize-a-vm"></a>Virtuális gép átméretezése
A legújabb kell átméretezni egy virtuális Gépet, [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

1. A rendelkezésre álló Virtuálisgép-méretek listáját megtekintheti a hardver fürt, ahol a virtuális gép tárolása a [az vm-vm-átméretezési-beállításai](/cli/azure/vm#az_vm_list_vm_resize_options). Az alábbi példa felsorolja a Virtuálisgép-méretek a virtuális gép nevű `myVM` erőforráscsoportban `myResourceGroup` régió:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Ha a kívánt virtuális gép mérete szerepel, méretezze át a virtuális Géphez a [az vm átméretezése](/cli/azure/vm#az_vm_resize). Az alábbi példa átméretezi nevű virtuális gép `myVM` számára a `Standard_DS3_v2` mérete:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    A virtuális gép újraindul, a folyamat során. Az újraindítás után a meglévő operációs rendszer és az adatlemezek újra társítani. Az ideiglenes lemezen semmit nem támogatott.

3. Ha a kívánt virtuális gép mérete nem szerepel, szeretné-e először felszabadítani a virtuális Géphez a [az virtuális gép felszabadítása](/cli/azure/vm#az_vm_deallocate). Ez a folyamat lehetővé teszi, hogy a virtuális Gépet, majd átméretezése az összes rendelkezésre álló terület az, hogy a régió támogatja, majd el. Az alábbi lépéseket felszabadítani, méretezze át, és indítsa el a nevű virtuális gép `myVM` az erőforráscsoport neve `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > A virtuális Géphez rendelt dinamikus IP-címek is a virtuális gép felszabadítása kiadását. Az operációsrendszer- és adatlemezek, nem érintettek.

## <a name="next-steps"></a>További lépések
További méretezhetőséget, a virtuális gép több példányának futtatása, és kiterjesztése. További információkért lásd: [automatikus méretezése a virtuálisgép-méretezési csoportban lévő Linux-gépek][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
