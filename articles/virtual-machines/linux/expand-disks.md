---
title: Bontsa ki a virtuális merevlemezek egy Linux rendszerű virtuális gépen az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan bontsa ki a virtuális merevlemezek egy Linux rendszerű virtuális gépen az Azure CLI 2.0 használatával
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
ms.openlocfilehash: 96d50260663f00f5ae2e9b2e0495c91ecb5da4b2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421188"
---
# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Hogyan bontsa ki a virtuális merevlemezek egy Linux rendszerű virtuális gépen az Azure CLI-vel
Az alapértelmezett virtuális merevlemezt az operációs rendszer (OS) mérete általában 30 GB egy Linux rendszerű virtuális gépre (VM) az Azure-ban. Is [adatlemezek hozzáadása](add-disk.md) nyújt a további tárterületre, de előfordulhat, hogy is szeretne egy már meglévő adatlemez kibontása. Ez a cikk részletesen bemutatja, bontsa ki a felügyelt lemezeket az Azure CLI 2.0 használatával Linux virtuális gép számára. 

> [!WARNING]
> Mindig győződjön meg arról, hogy biztonsági másolatot az adatokat, mielőtt végrehajtaná a lemez átméretezése operations. További információkért lásd: [az Azure-ban a Linux rendszerű virtuális gépek biztonsági mentése](tutorial-backup-vms.md).

## <a name="expand-azure-managed-disk"></a>Az Azure felügyelt lemez kibontása
Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index#az_login).

Ez a cikk az Azure-ban egy meglévő virtuális gép legalább egy adatlemezt csatolni, és előkészíti igényel. Ha Ön még nem rendelkezik egy virtuális Gépet, amelyet használhat, lásd: [létrehozás és adatlemezekkel rendelkező virtuális gép előkészítése](tutorial-manage-disks.md#create-and-attach-disks).

Cserélje le a következő minták példa a paraméter nevét a saját értékeire. Példa a paraméter nevek a következők *myResourceGroup* és *myVM*.

1. Futó virtuális gép nem hajtható végre műveleteket a virtuális merevlemezeket. A virtuális gép felszabadítása [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Az alábbi példa felszabadítja a virtuális gép nevű *myVM* az erőforráscsoport neve *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > A virtuális gép, bontsa ki a virtuális merevlemez fel kell szabadítani. `az vm stop` nem mentesíti a számítási erőforrásokat. A számítási erőforrások felszabadítása használja `az vm deallocate`.

1. A felügyelt lemezek listájának megtekintéséhez egy erőforráscsoportban [az Lemezlista](/cli/azure/disk#az_disk_list). Az alábbi példában a felügyelt lemezek listáját jeleníti meg a nevű erőforráscsoportban *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Bontsa ki a szükséges lemezt a [az disk update](/cli/azure/disk#az_disk_update). Az alábbi példa kibővíti a felügyelt lemez nevű *myDataDisk* kell *200*Gb méretű:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Amikor kibővít egy felügyelt lemezt, a legközelebbi felügyelt lemez méretét a frissített mérete lesz leképezve. Az egy táblázatot a rendelkezésre álló felügyelt lemez méretét és a réteg [Azure Managed Disks – áttekintés – árak és számlázás](../windows/managed-disks-overview.md#pricing-and-billing).

1. Indítsa el a virtuális Gépet a [az vm start](/cli/azure/vm#az_vm_start). A következő példa elindítja a virtuális gép nevű *myVM* az erőforráscsoport neve *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-disk-partition-and-filesystem"></a>Bontsa ki a lemezpartíciót és fájlrendszer
A kiterjesztett lemez használatához meg kell bontsa ki az alapul szolgáló partíció vagy fájlrendszer.

1. Ssh-KAPCSOLATOT a virtuális gép a megfelelő hitelesítő adatokkal. A virtuális gép nyilvános IP-címét is kaphat [az vm show](/cli/azure/vm#az_vm_show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. A kiterjesztett lemez használatához meg kell bontsa ki az alapul szolgáló partíció vagy fájlrendszer.

    a. Ha már csatlakoztatta, válassza le a lemezt:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Használat `parted` tárolólemez-információk megtekintéséhez, és méretezze át a partíció:

    ```bash
    sudo parted /dev/sdc
    ```

    A meglévő partíciók elrendezését az kapcsolatos információk megtekintéséhez `print`. A kimenet a következő példának, amely bemutatja az alapul szolgáló lemezének mérete 215Gb méretű hasonlít:

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

    c. Bontsa ki a partíció `resizepart`. Adja meg a partíciószám *1*, és az új partíció méretét:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Adja meg a kilépéshez `quit`

1. A partícióhoz, méretezhető, a partíció konzisztencia-ellenőrzése `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Most már méretezze át a fájlrendszer- `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Például csatlakoztassa a partíciót a kívánt helyre `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Az operációsrendszer-lemez át lett méretezve ellenőrzéséhez használja a `df -h`. Az alábbi példa kimenetében látható az adatmeghajtó */dev/sdc1*, 200 GB-os már:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>További lépések
Ha további tárhelyre van szüksége, is [adatlemezek hozzáadása Linux rendszerű virtuális gép](add-disk.md). Lemeztitkosítás kapcsolatos további információkért lásd: [egy Linux rendszerű virtuális gépen az Azure CLI-vel lemezek titkosítása](encrypt-disks.md).
