---
title: Bontsa ki a Linux virtuális gép az Azure CLI 1.0 az operációsrendszer-lemez |} Microsoft Docs
description: Megtudhatja, hogyan bontsa ki az operációs rendszer virtuális lemez a Linux virtuális gépet az Azure CLI 1.0 és a Resource Manager üzembe helyezési modellel
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: f81054727bb1f0e8ffa752783e866a72d573589d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30905333"
---
# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Bontsa ki a Linux virtuális gép az Azure CLI használata az Azure CLI 1.0 operációsrendszer-lemez
Az alapértelmezett virtuális merevlemezt az operációs rendszer mérete általában 30 GB Linux virtuális gépre (VM) az Azure-ban. Is [adatok lemezek hozzáadása a](add-disk.md) további tárhelyet, de így is célszerű bontsa ki az operációsrendszer-lemezképet. Ez a cikk részletesen bontsa ki az operációsrendszer-lemezképet a Linux virtuális gépek nem felügyelt lemezek használata az Azure CLI 1.0 módjáról.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#prerequisites) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](expand-disks.md) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

## <a name="prerequisites"></a>Előfeltételek
Van szüksége a [Azure CLI legújabb 1.0](../../cli-install-nodejs.md) telepítve, és bejelentkezett egy [Azure-fiók](https://azure.microsoft.com/pricing/free-trial/) használatával a Resource Manager módra az alábbiak szerint:

```azurecli
azure config mode arm
```

A következő mintában cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők *myResourceGroup* és *myVM*.

## <a name="expand-os-disk"></a>Operációsrendszer-lemez kibontása

1. A virtuális merevlemezeken műveletek nem hajtható végre FUTÓ virtuális. Az alábbi példa leáll, és felszabadítja a nevű virtuális gép *myVM* az erőforráscsoport neve *myResourceGroup*:

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` fel a számítási erőforrásokat. A számítási erőforrásokat kiadási használja `azure vm deallocate`. A virtuális gép felszabadítása kell a virtuális merevlemez bővítéséhez.

2. Frissítse a nem felügyelt OS lemez használatával méretét a `azure vm set` parancsot. Az alábbi példa frissíti a virtuális gép nevű *myVM* az erőforráscsoport neve *myResourceGroup* kell *50* GB:

    ```azurecli
    azure vm set \
        --resource-group myResourceGroup \
        --name myVM \
        --new-os-disk-size 50
    ```

3. Indítsa el a virtuális Gépet az alábbiak szerint:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH-kapcsolatot a virtuális Gépet a megfelelő hitelesítő adatokkal. Az operációs rendszer lemezének át lett méretezve, amellyel `df -h`. A következő példa az alábbiakat mutatja be az elsődleges partíció (*/dev/sda1*) 50 GB-os áll:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>További lépések
Ha szeretne további tárhely, akkor is [adatok lemezek hozzáadása a Linux virtuális gép](add-disk.md). Lemez titkosításával kapcsolatos további információkért lásd: [titkosítani az Azure parancssori felület használatával Linux virtuális gép lemezeinek](encrypt-disks.md).
