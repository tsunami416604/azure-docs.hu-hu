---
title: Alakítsa át a Linux virtuális gépek Azure-ban nem felügyelt lemezekből felügyelt - Azure felügyelt lemezek |} Microsoft Docs
description: Alakítsa át a Linux virtuális gép nem felügyelt lemezekből felügyelt Azure CLI 2.0 használatával a Resource Manager üzembe helyezési modellel hogyan
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: rogarana
ms.openlocfilehash: a3a2bbc15dd94ef09755d34a20e69c97854416b3
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
ms.locfileid: "30289260"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Alakítsa át a Linux virtuális gépek nem felügyelt lemezekből felügyelt

Ha meglévő Linux virtuális gépek (VM), a nem felügyelt lemezeket használó, a virtuális gépek használatához átválthat [Azure felügyelt lemezek](../linux/managed-disks-overview.md). Ez a folyamat az operációsrendszer-lemez és a mellékelt adatok lemezzel alakítja át.

Ez a cikk bemutatja, hogyan alakítsa át a virtuális gépek az Azure parancssori felület használatával. Ha szeretné telepíteni vagy frissíteni, lásd: [Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Előkészületek
* Felülvizsgálati [felügyelt lemezekre áttelepítésével kapcsolatos gyakori kérdések](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Egypéldányos virtuális gépek átalakítása
Ez a szakasz bemutatja, hogyan adhat alakítsa át a egypéldányos Azure virtuális gépek nem felügyelt lemezekből felügyelt. (Ha a virtuális gépek rendelkezésre állási beállítása, a következő szakaszban talál.) Ez a folyamat használatával alakítsa át a virtuális gépek nem felügyelt prémium (SSD) lemezeket – kezelt premium lemezek vagy (HDD) szabvány nem felügyelt lemezekből szabványos felügyelt.

1. A virtuális gép felszabadítása használatával [az virtuális gép felszabadítása](/cli/azure/vm#az_vm_deallocate). Az alábbi példa felszabadítja a nevű virtuális gép `myVM` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Alakítsa át a virtuális Gépet felügyelt használatával [az virtuális gép átalakítása](/cli/azure/vm#az_vm_convert). A következő folyamat alakítja át a virtuális gép nevű `myVM`, többek között az operációsrendszer-lemezképet, és bármely adatlemezek:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Indítsa el a virtuális Gépet felügyelt lemezekre átalakítás után [az vm indítása](/cli/azure/vm#az_vm_start). A következő példában elindul nevű virtuális gép `myVM` az erőforráscsoport neve `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Alakítsa át a virtuális gépek rendelkezésre állási csoportba

Ha az átalakítani kívánt virtuális gépek lemezek rendelkezésre állási csoportba, először alakítsa át a rendelkezésre állási csoportot egy felügyelt rendelkezésre állási csoportba.

A rendelkezésre állási csoportot az összes virtuális gép felszabadítása kell, mielőtt a rendelkezésre állási csoport. Tervezze meg az összes virtuális gép átalakítása felügyelt lemezek után a rendelkezésre állási csoportot magát egy felügyelt rendelkezésre állási csoport lett konvertálva. Ezután indítsa el a virtuális gépeket, és folytatja a normál.

1. Minden virtuális gép rendelkezésre állási készlet használatával listájában [az virtuális gép rendelkezésre állási-készlet lista](/cli/azure/vm/availability-set#az_vm_availability_set_list). Az alábbi példa felsorolja az összes virtuális gépek rendelkezésre állási csoportban elnevezett `myAvailabilitySet` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. A virtuális gépek felszabadítani használatával [az virtuális gép felszabadítása](/cli/azure/vm#az_vm_deallocate). Az alábbi példa felszabadítja a nevű virtuális gép `myVM` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Alakítsa át a rendelkezésre állási csoportot használatával [az a virtuális gép rendelkezésre állási-készlet konvertálás](/cli/azure/vm/availability-set#az_vm_availability_set_convert). Az alábbi példa alakítja át a rendelkezésre állási csoportot elnevezett `myAvailabilitySet` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Alakítsa át a virtuális gépek felügyelt használatával [az virtuális gép átalakítása](/cli/azure/vm#az_vm_convert). A következő folyamat alakítja át a virtuális gép nevű `myVM`, többek között az operációsrendszer-lemezképet, és bármely adatlemezek:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Indítsa el a virtuális gépek segítségével felügyelt lemezekre átalakítás után [az vm indítása](/cli/azure/vm#az_vm_start). A következő példában elindul nevű virtuális gép `myVM` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>További lépések
További információ a tárolási lehetőségek közül választhat: [Azure felügyelt lemezekhez – áttekintés](../windows/managed-disks-overview.md).
