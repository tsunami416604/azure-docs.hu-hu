---
title: Linuxos virtuális gép konvertálása nem felügyelt lemezekről felügyelt lemezekké
description: Linuxos virtuális gép nem felügyelt lemezekről felügyelt lemezekké konvertálása az Azure CLI használatával.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2774dcbd5fc5b01627b965c2c02d870412c8bf77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969691"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Linuxos virtuális gép konvertálása nem felügyelt lemezekről felügyelt lemezekké

Ha meglévő, nem felügyelt lemezeket használó Linux virtuális gépekkel (VM-ekkel) rendelkezik, a virtuális gépeket [azure felügyelt lemezek használatára](../linux/managed-disks-overview.md)konvertálhatja. Ez a folyamat átalakítja az operációs rendszer lemezét és a csatlakoztatott adatlemezeket is.

Ez a cikk bemutatja, hogyan konvertálhatja a virtuális gépek et az Azure CLI használatával. Ha telepítenivagy frissíteni kell, olvassa el [az Azure CLI telepítése](/cli/azure/install-azure-cli)című témakört. 

## <a name="before-you-begin"></a>Előkészületek
* Tekintse át [a felügyelt lemezekre való áttelepítéssel kapcsolatos gyakori kérdéseket.](faq-for-disks.md#migrate-to-managed-disks)

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* A virtuális gép által az átalakítás előtt használt eredeti virtuális merevlemezeket és a tárfiókot nem törli a rendszer. A futtatásuk továbbra is költségekkel jár. Annak érdekében, hogy ezekért az összetevőkért a továbbiakban ne fizessen, törölje az eredeti virtuálismerevlemez-blobokat, miután meggyőződött arról, hogy az átalakítás befejeződött. Ha ezeknek a nem csatlakoztatott lemezeknek a törléséhez meg kell találnia ezeket a lemezeket, olvassa el a Nem csatolt Azure által felügyelt és nem felügyelt lemezek keresése és törlése című [cikkünket.](find-unattached-disks.md)

## <a name="convert-single-instance-vms"></a>Egypéldányos virtuális gépek konvertálása
Ez a szakasz bemutatja, hogyan konvertálhatja az egypéldányos Azure-virtuális gépeket nem felügyelt lemezekről felügyelt lemezekre. (Ha a virtuális gépek egy rendelkezésre állási csoportban vannak, tekintse meg a következő szakaszt.) Ezzel a folyamattal konvertálhatja a virtuális gépeket prémium szintű (SSD) nem felügyelt lemezekről prémium felügyelt lemezekké, vagy szabványos (HDD) nem felügyelt lemezekről szabványos felügyelt lemezekre.

1. A virtuális gép felszabadítása az [vm felszabadításával.](/cli/azure/vm) A következő példa felszabadítja `myVM` a megnevezett `myResourceGroup`erőforráscsoportban megnevezett virtuális gép felszabadítását:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. A virtuális gép konvertálása felügyelt lemezekké az [vm convert](/cli/azure/vm)használatával. A következő folyamat átalakítja `myVM`a virtuális gép nevű , beleértve az operációs rendszer lemeze és az adatlemezek:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Indítsa el a virtuális gép a felügyelt lemezekre való átalakítás után [az vm start](/cli/azure/vm)használatával. A következő példa elindítja `myVM` a névnek `myResourceGroup`nevezett erőforráscsoportban megnevezett virtuális gép.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Virtuális gépek konvertálása rendelkezésre állási csoportban

Ha a felügyelt lemezekre konvertálni kívánt virtuális gépek rendelkezésre állási csoportban vannak, először a rendelkezésre állási készletet felügyelt rendelkezésre állási csoportná kell konvertálnia.

A rendelkezésre állási csoportban lévő összes virtuális gépet fel kell osztani a rendelkezésre állási csoport konvertálása előtt. Tervezze meg, hogy az összes virtuális gépet felügyelt lemezekké konvertálja, miután maga a rendelkezésre állási készlet felügyelt rendelkezésre állási készletté lett konvertálva. Ezután indítsa el az összes virtuális gépet, és továbbra is a szokásos módon működjön.

1. Az összes virtuális gép listázása egy rendelkezésre állási csoportban [az virtuális gép rendelkezésre állási készletlistájának](/cli/azure/vm/availability-set)használatával. A következő példa a megnevezett erőforráscsoportban `myAvailabilitySet` megnevezett `myResourceGroup`rendelkezésre állási csoport összes virtuális gépét sorolja fel:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Az összes virtuális gép felszabadítása az [vm felszabadításával.](/cli/azure/vm) A következő példa felszabadítja `myVM` a megnevezett `myResourceGroup`erőforráscsoportban megnevezett virtuális gép felszabadítását:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Konvertálja a rendelkezésre állási készletet [az vm availability-set convert](/cli/azure/vm/availability-set)használatával. A következő példa átalakítja `myAvailabilitySet` a nevesített `myResourceGroup`erőforráscsoportban elnevezett rendelkezésre állási csoportot:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Konvertálja az összes virtuális gépet felügyelt lemezekké az [vm convert](/cli/azure/vm)használatával. A következő folyamat átalakítja `myVM`a virtuális gép nevű , beleértve az operációs rendszer lemeze és az adatlemezek:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Indítsa el az összes virtuális gépet a felügyelt lemezekre való átalakítás után az [vm start](/cli/azure/vm)használatával. A következő példa elindítja `myVM` a névvel `myResourceGroup`ellátott erőforráscsoportban megnevezett virtuális gépét:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Konvertálás az Azure Portal használatával

A nem felügyelt lemezeket felügyelt lemezekké is konvertálhatja az Azure Portalhasználatával.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza ki a virtuális gépet a portálon lévő virtuális gépek listájából.
3. A panela virtuális gép, válassza **a lemezek** a menüből.
4. A **Lemezek** panel tetején válassza az **Áttelepítés felügyelt lemezekre**lehetőséget.
5. Ha a virtuális gép egy rendelkezésre állási csoportban van, figyelmeztetést kap a **felügyelt lemezek áttelepítése** panelen, amely először a rendelkezésre állási készlet átalakításához szükséges. A figyelmeztetésnek rendelkeznie kell egy hivatkozással, amelyre kattintva konvertálhatja az elérhetőségi készletet. Miután a rendelkezésre állási csoport konvertált, vagy ha a virtuális gép nem egy rendelkezésre állási csoportban, kattintson az **Áttelepítés** gombra a lemezek felügyelt lemezekre való áttelepítése folyamatának elindításához.

A virtuális gép leáll, és az áttelepítés befejezése után újraindul.

## <a name="next-steps"></a>További lépések

A tárolási lehetőségekről az Azure Felügyelt lemezek – áttekintés című témakörben olvashat [bővebben.](../windows/managed-disks-overview.md)
