---
title: Linuxos virtuális gép átméretezése az Azure CLI-vel
description: Linuxos virtuális gép méretezése vagy leskálázása a virtuális gép méretének módosításával.
author: mikewasson
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: mwasson
ms.openlocfilehash: 20e7db80b55347c4a4a76b7c95d4d8bec368abda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969265"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Linuxos virtuális gép újraméretezése Azure CLI használatával 

Miután üzembe helyezett egy virtuális gépet (VM), a virtuális gép fel- vagy leskálázásával skálázhatja a [virtuális gép méretét.][vm-sizes] Bizonyos esetekben először fel kell osztania a virtuális gép. A virtuális gép felszabadítása szükséges, ha a kívánt méret nem érhető el a virtuális gép üzemeltető hardverfürtjével. Ez a cikk ismerteti, hogyan méretezheti át a Linux virtuális gépek az Azure CLI. 

## <a name="resize-a-vm"></a>Virtuális gép átméretezése
Virtuális gép átméretezéséhez a legújabb [Azure CLI-t](/cli/azure/install-az-cli2) kell telepítenie, és be kell jelentkeznie egy Azure-fiókba [az a bejelentkezéssel.](/cli/azure/reference-index)

1. Tekintse meg az elérhető virtuálisgép-méretek listáját azon a hardverfürtön, ahol a virtuális gép az [vm list-vm-resize-options](/cli/azure/vm)található. A következő példa az erőforráscsoport-régióban `myVM` `myResourceGroup` megnevezett virtuális gép virtuális gépének méretét sorolja fel:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Ha a kívánt virtuális gép mérete szerepel, méretezze át a virtuális gép [az vm átméretezése.](/cli/azure/vm) A következő példa átméretezi `myVM` a `Standard_DS3_v2` névvel ellátott virtuális gép a méret:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    A virtuális gép a folyamat során újraindul. Az újraindítás után a meglévő operációs rendszer és az adatlemezek újraleképezésre kerülnek. Az ideiglenes lemezen minden elveszik.

3. Ha a kívánt virtuális gép mérete nem szerepel a listában, először fel kell osztania a virtuális gép [az virtuális gép felszabadításával.](/cli/azure/vm) Ez a folyamat lehetővé teszi, hogy a virtuális gép, majd átméretezhető bármilyen méretben elérhető, hogy a régió támogatja, majd el kell indítani. A következő lépések felszabadítják, átméretezik, `myVM` majd elindítják `myResourceGroup`a névvel ellátott erőforráscsoportban megnevezett virtuális gépét:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > A virtuális gép a virtuális géphez rendelt dinamikus IP-címeket is felszabadítja. Az operációs rendszer és az adatlemezek nem érintettek.

## <a name="next-steps"></a>További lépések
További méretezhetőség érdekében futtasson több virtuálisgép-példányt, és horizontális felskálázás. További információ: [A Linux-gépek automatikus méretezése virtuálisgép-méretezési csoportban][scale-set]című témakörben talál. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
