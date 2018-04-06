---
title: Linux virtuális gép és az Azure CLI 1.0 átméretezése |} Microsoft Docs
description: Hogyan növelheti vagy csökkentheti a Linux virtuális gépek, a Virtuálisgép-méretet módosításával.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7bb5ad082336c92a397f2590d9b06e94552c8744
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="resize-a-linux-vm-with-azure-cli-10"></a>Linux virtuális gép az Azure CLI 1.0 és átméretezése

## <a name="overview"></a>Áttekintés

(VM) virtuális gép kiépítése után méretezheti a virtuális gép felfelé vagy lefelé módosításával a [Virtuálisgép-méretet][vm-sizes]. Néhány esetben először a virtuális gép kell felszabadítani. Ez akkor fordulhat elő, ha új mérete nem érhető el a hardver fürtön, amelyen a virtuális Gépet.

Ez a cikk bemutatja, hogyan méretezze át a Linux virtuális gépet a [Azure CLI][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#resize-a-linux-vm) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.


## <a name="resize-a-linux-vm"></a>Linux virtuális gép átméretezése
Átméretezni egy virtuális Géphez, hajtsa végre az alábbi lépéseket.

1. A következő parancssori parancsot. Ez a parancs felsorolja a hardver fürt, ahol a virtuális gép tárolása elérhető Virtuálisgép-méretek.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. Ha a kívánt méretet, a következő parancsot a virtuális gép átméretezésével.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    A virtuális gép újraindul, a folyamat során. Az újraindítás után a meglévő operációs rendszer és az adatlemezek fogja képezni. Az ideiglenes lemezen semmit elvesznek.
   
    Használja a `--enable-boot-diagnostics` beállítás lehetővé teszi, hogy [rendszerindítási diagnosztika][boot-diagnostics], bejelentkezési indítási kapcsolatos hibákat.
3. Ha a kívánt méretet nem szerepel, a következő parancsokat a virtuális gép felszabadítása méretezze át, és indítsa újra a virtuális Gépet.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > A virtuális Géphez rendelt dinamikus IP-címek is a virtuális gép felszabadítása kiadását. Az operációsrendszer- és adatlemezek, nem érintettek.
   > 
   > 

## <a name="next-steps"></a>További lépések
További méretezhetőséget, a virtuális gép több példányának futtatása, és kiterjesztése. További információkért lásd: [automatikus méretezése a virtuálisgép-méretezési csoportban lévő Linux-gépek][scale-set]. 

<!-- links -->

[azure-cli]:../../cli-install-nodejs.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
