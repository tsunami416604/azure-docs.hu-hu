---
title: Az Azure-beli Linuxos virtuális gép átalakítása nem felügyeltről felügyelt managed disksbe – az Azure Managed Disks |} A Microsoft Docs
description: Hogyan Linux virtuális gép átalakítása nem felügyeltről felügyelt a managed Disks szolgáltatásba Azure parancssori felület használatával a Resource Manager-alapú üzemi modellben
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
ms.subservice: disks
ms.openlocfilehash: 692de0e18ac279174f1227a7c25913fc59e626eb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470059"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Linux rendszerű virtuális gép átalakítása nem felügyeltről felügyelt a managed Disks szolgáltatásba

Ha rendelkezik meglévő Linux rendszerű virtuális gépek (VM), amely a nem felügyelt lemezeket használ, a virtuális gépek használata alakíthatja [Azure Managed Disks](../linux/managed-disks-overview.md). Ez a folyamat az operációsrendszer-lemez és a bármely más csatolt lemez alakítja.

Ez a cikk bemutatja, hogyan átalakítása a virtuális gépek az Azure parancssori felület használatával. Ha szeretné telepíteni, vagy frissít a csomagon belül, lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Előkészületek
* Felülvizsgálat [a migrálás a Managed Disks – gyakori kérdések](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Egypéldányos virtuális gépek konvertálása
Ez a szakasz bemutatja, hogyan átalakítása nem felügyeltről felügyelt egypéldányos Azure virtuális gépek felügyelt lemezeket. (Ha a virtuális gépek rendelkezésre állási csoportban, lásd a következő szakaszban.) Ez a folyamat segítségével a virtuális gépek konvertálása a standard szintű felügyelt lemezek nem felügyelt prémium (SSD) nem felügyelt lemezek prémium szintű felügyelt lemezek, illetve a standard szintű (HDD) lemezek.

1. Szabadítsa fel a virtuális gép használatával [az vm deallocate](/cli/azure/vm). Az alábbi példa felszabadítja a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. A virtuális gép konvertálása a felügyelt lemezek használatával [az virtuális gép konvertálása](/cli/azure/vm#az_vm_convert). A következő folyamat alakítja át a virtuális gép nevű `myVM`, többek között az operációsrendszer-lemez és bármely adatlemez:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. A virtuális gép elindítása után a felügyelt lemezekre való konvertálást használatával [az vm start](/cli/azure/vm#az_vm_start). A következő példa elindítja a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Konvertálja a virtuális gépeket egy rendelkezésre állási csoportban

Ha az átalakítani kívánt virtuális gépek felügyelt lemezeket egy rendelkezésre állási csoportban, először konvertálnia kell a rendelkezésre állási felügyelt rendelkezésre állási csoportban.

A rendelkezésre állási csoportban lévő összes virtuális gép konvertálása a rendelkezésre állási csoport előtt fel kell szabadítani. Tervezze meg az összes virtuális gép konvertálása a felügyelt lemezek után a rendelkezésre állási csoportot magát egy felügyelt rendelkezésre állási csoport lett konvertálva. Ezután indítsa el a virtuális gépeket, és továbbra is a szokott módon működik.

1. Minden virtuális gép egy rendelkezésre állási csoport használatával listájában [az a virtuális gépek rendelkezésre állási csoport listáját](/cli/azure/vm/availability-set#az_vm_availability_set_list). Az alábbi példa felsorolja az összes virtuális gépeket a rendelkezésre állási csoportban elnevezett `myAvailabilitySet` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Az összes virtuális gép felszabadítása [az vm deallocate](/cli/azure/vm). Az alábbi példa felszabadítja a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. A rendelkezésre állási csoportot az átalakítás [az virtuális gép rendelkezésre állási csoport konvertálása](/cli/azure/vm/availability-set#az_vm_availability_set_convert). Az alábbi példa alakítja át a rendelkezésre állási csoportot elnevezett `myAvailabilitySet` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. A virtuális gépek konvertálása a felügyelt lemezek használatával [az virtuális gép konvertálása](/cli/azure/vm#az_vm_convert). A következő folyamat alakítja át a virtuális gép nevű `myVM`, többek között az operációsrendszer-lemez és bármely adatlemez:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. A virtuális gépek elindítása után a felügyelt lemezekre való konvertálást használatával [az vm start](/cli/azure/vm#az_vm_start). A következő példa elindítja a virtuális gép nevű `myVM` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>További lépések
Tárolási lehetőségekkel kapcsolatos további információkért lásd: [Azure Managed Disks – áttekintés](../windows/managed-disks-overview.md).
