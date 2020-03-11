---
title: Linux rendszerű virtuális gép átméretezése az Azure CLI-vel
description: Linux rendszerű virtuális gépek vertikális felskálázása vagy leskálázása a virtuális gép méretének módosításával.
author: mikewasson
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: mwasson
ms.openlocfilehash: 20e7db80b55347c4a4a76b7c95d4d8bec368abda
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969265"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Linux rendszerű virtuális gép átméretezése az Azure CLI-vel 

Miután kiépített egy virtuális gépet (VM), a virtuális gép [méretének][vm-sizes]módosításával felfelé vagy lefelé méretezheti. Bizonyos esetekben először fel kell szabadítania a virtuális gépet. Ha a kívánt méret nem érhető el a virtuális gépet futtató hardveres fürtön, fel kell szabadítania a virtuális gépet. Ez a cikk a Linux rendszerű virtuális gépek Azure CLI-vel való átméretezésének részleteit ismerteti. 

## <a name="resize-a-vm"></a>Virtuális gép átméretezése
A virtuális gépek átméretezéséhez a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t kell telepítenie, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

1. Tekintse meg az elérhető virtuálisgép-méretek listáját azon a hardveres fürtön, ahol a virtuális gép az az [VM List-VM-Resize-Options](/cli/azure/vm). Az alábbi példa felsorolja a `myVM` nevű virtuális gép virtuálisgép-méreteit az erőforráscsoport `myResourceGroup` régióban:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Ha a kívánt virtuális gép mérete megjelenik, méretezze át a virtuális gépet az [az VM átméretezésével](/cli/azure/vm). Az alábbi példa átméretezi `myVM` nevű virtuális gépet a `Standard_DS3_v2` méretre:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    A virtuális gép újraindul a folyamat során. Az újraindítás után a rendszer újra leképezi a meglévő operációs rendszert és az adatlemezeket. Az ideiglenes lemezen lévő minden adat elvész.

3. Ha a kívánt virtuálisgép-méret nem szerepel a listáján, először fel kell szabadítania a virtuális gépet az [az VM felszabadításával](/cli/azure/vm). Ez a folyamat lehetővé teszi, hogy a virtuális gép átméretezi a régió által támogatott, majd megkezdett méretre. A következő lépésekkel felszabadíthatja, átméretezheti, majd elindíthatja `myVM` nevű virtuális gépet az `myResourceGroup`nevű erőforráscsoport:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > A virtuális gép felszabadítása a virtuális géphez hozzárendelt dinamikus IP-címeket is felszabadítja. Az operációs rendszer és az adatlemezek nem érintettek.

## <a name="next-steps"></a>Következő lépések
További méretezhetőség érdekében futtasson több virtuálisgép-példányt és horizontális felskálázást. További információ: [Linux rendszerű gépek automatikus méretezése virtuálisgép-méretezési csoportokban][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
