---
title: Bontsa ki a virtuális merevlemezek, a Linux virtuális gép az Azure-ban |} Microsoft Docs
description: Megtudhatja, hogyan bontsa ki a virtuális merevlemezek, a Linux virtuális gép az Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: rogarana
ms.openlocfilehash: 8b3a4d7feccc3af55415f54473ae1a2588ad5672
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936887"
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>A Linux virtuális gép és az Azure parancssori felület virtuális merevlemezek kibontása
Az alapértelmezett virtuális merevlemezt az operációs rendszer mérete általában 30 GB Linux virtuális gépre (VM) az Azure-ban. Is [adatok lemezek hozzáadása a](add-disk.md) további tárhelyet, de így is célszerű bontsa ki a meglévő adatok lemez. Ez a cikk a Linux virtuális gépek az Azure CLI 2.0-s verziójával kezelt lemezek kibontása részletezi. 

> [!WARNING]
> Mindig győződjön meg arról, hogy készítsen biztonsági másolatot az adatokat, mielőtt elvégezné a lemez átméretezése műveletek. További információkért lásd: [készítsen biztonsági másolatot a Linux virtuális gépek Azure-ban](tutorial-backup-vms.md).

## <a name="expand-azure-managed-disk"></a>Bontsa ki az Azure felügyelt lemezes
Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

Ez a cikk egy meglévő Azure-ban az legalább egy adatlemezt csatolni, és előkészített van szükség. Ha még nem rendelkezik a virtuális gépek közül választhat, tekintse meg [létrehozása és készítse elő a virtuális gép és adatlemezek](tutorial-manage-disks.md#create-and-attach-disks).

A következő mintában cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők *myResourceGroup* és *myVM*.

1. A virtuális merevlemezeken műveletek nem hajtható végre FUTÓ virtuális. Az a virtuális gép felszabadítása [az virtuális gép felszabadítása](/cli/azure/vm#az_vm_deallocate). Az alábbi példa felszabadítja a nevű virtuális gép *myVM* az erőforráscsoport neve *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > A virtuális gép felszabadítása kell a virtuális merevlemez bővítéséhez. `az vm stop` fel a számítási erőforrásokat. A számítási erőforrásokat kiadási használja `az vm deallocate`.

2. Felügyelt lemezek listájának megtekintése a erőforráscsoportban [az Lemezlista](/cli/azure/disk#az_disk_list). Az alábbi példa felügyelt lemezek listáját jeleníti meg az erőforráscsoportot *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Bontsa ki a szükséges lemez [az lemez frissítés](/cli/azure/disk#az_disk_update). Az alábbi példa kibontja a felügyelt lemezes nevű *myDataDisk* kell *200*Gb-nál:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Amikor kibővít egy felügyelt lemezes, a frissített mérete a legközelebbi felügyelt lemezméretet van leképezve. Az egy táblázatot a felügyelt szabad méretek és a rétegek, [Azure felügyelt lemezekhez – áttekintés – árak és számlázás](../windows/managed-disks-overview.md#pricing-and-billing).

3. Indítsa el a virtuális Gépet a [az vm indítása](/cli/azure/vm#az_vm_start). A következő példában elindul nevű virtuális gép *myVM* az erőforráscsoport neve *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>Bontsa ki a kívánt lemezpartíciót és fájlrendszer
A kibontott lemezt használ, meg kell bontsa ki az alapul szolgáló partíció és fájlrendszer.

1. SSH-kapcsolatot a virtuális Gépet a megfelelő hitelesítő adatokkal. Ezt úgy szerezheti be a nyilvános IP-címet a virtuális gép a [az vm megjelenítése](/cli/azure/vm#az_vm_show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

2. A kibontott lemezt használ, meg kell bontsa ki az alapul szolgáló partíció és fájlrendszer.

    a. Ha már csatlakoztatva van, leválasztani a lemezt:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Használjon `parted` lemez adatainak megtekintéséhez, és a partíció átméretezése:

    ```bash
    sudo parted /dev/sdc
    ```

    Tekintse meg a meglévő partíció elrendezése `print`. A kimeneti hasonlít a következő példának, amely azt mutatja, az alapul szolgáló lemez 215Gb-nál:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Bontsa ki a partíció `resizepart`. Adja meg a partíció, *1*, és az új partíció méretét:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Lépjen ki, írja be a következőt `quit`

3. Átméretezték, ezért a partíció, ellenőrizze a partíció konzisztenciájának `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

4. A fájlrendszer most átméretezése `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

5. A partíció a kívánt helyre, például a csatlakoztatási `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

6. Az operációs rendszer lemezének át lett méretezve, amellyel `df -h`. Az alábbi példa kimenetben látható az adatmeghajtó */dev/sdc1*, 200 GB-os áll:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>További lépések
Ha szeretne további tárhely, akkor is [adatok lemezek hozzáadása a Linux virtuális gép](add-disk.md). Lemez titkosításával kapcsolatos további információkért lásd: [titkosítani az Azure parancssori felület használatával Linux virtuális gép lemezeinek](encrypt-disks.md).
