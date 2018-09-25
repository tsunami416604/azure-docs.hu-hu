---
title: Az Azure CLI-vel Linux virtuális gép átméretezése |} A Microsoft Docs
description: Hogyan növelheti vagy csökkentheti a Linuxos virtuális gépek, a virtuális gép méretének módosításával.
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
ms.openlocfilehash: 0b8802c91ceb59d391dc27a71da905de9c15a1dc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993228"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Azure CLI-vel Linux virtuális gépek átméretezése 

Miután egy virtuális gépet (VM) üzembe helyez, méretezheti a virtuális gép felfelé vagy lefelé módosításával a [Virtuálisgép-méret][vm-sizes]. Bizonyos esetekben kell először felszabadítja a virtuális Gépet. Szabadítsa fel a virtuális Gépet, ha a kívánt méret nem érhető el a hardver fürtön, amelyen a virtuális Gépet kell. Ez a cikk részletesen bemutatja az Azure CLI-vel Linux rendszerű virtuális gépek átméretezése. 

## <a name="resize-a-vm"></a>Virtuális gép átméretezése
Virtuális gép átméretezése, a legújabb kell [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index#az_login).

1. A virtuális Gépet üzemeltető a hardver-fürtön elérhető Virtuálisgép-méretek listájának megtekintéséhez [az vm list-vm-resize-options](/cli/azure/vm#az_vm_list_vm_resize_options). Az alábbi példa felsorolja a Virtuálisgép-méretek nevű virtuális gép `myVM` erőforráscsoportban `myResourceGroup` régió:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Ha a virtuális gép kívánt méretét szerepel a listán, méretezze át a virtuális Gépet a [az virtuális gép átméretezése](/cli/azure/vm#az_vm_resize). Az alábbi példában a virtuális gép nevű átméretezi `myVM` , a `Standard_DS3_v2` mérete:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    A virtuális gép újraindul, a folyamat során. Az újraindítás után a meglévő operációs rendszer és az adatlemezek állapotba kerülnek. Az ideiglenes lemez semmit sem elvész.

3. Ha a kívánt VM-méret nem szerepel, akkor először szabadítsa fel a virtuális Gépet a [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Ez a folyamat lehetővé teszi, hogy a virtuális Gépen, majd át lehet méretezni, tetszőleges méretű érhető el, hogy a régió támogatja, és ezután elindult. Az alábbi lépéseket felszabadítása, átméretezése és indítsa el a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > A virtuális gép felszabadítása is bármely dinamikus IP-címek a virtuális géphez hozzárendelt ad ki. Az operációs rendszer és az adatlemezek nem érinti.

## <a name="next-steps"></a>További lépések
A további skálázhatóság érdekében több Virtuálisgép-példány futtatása és a horizontális felskálázás. További információkért lásd: [Linux-gépeinek automatikus skálázása a Virtual Machine Scale Set][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
