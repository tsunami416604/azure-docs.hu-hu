---
title: Bontsa ki a virtuális merevlemezek egy Linux rendszerű virtuális gépen az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan bontsa ki a virtuális merevlemezek egy Linux rendszerű virtuális gépen az Azure CLI-vel
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
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: a55839550b57da2ae346e66f4908da39e78b76f3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328286"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Bontsa ki a virtuális merevlemezek egy Linux rendszerű virtuális gépen az Azure CLI-vel

Ez a cikk ismerteti, hogyan bővít ki egy Linux rendszerű virtuális gépek (VM) az Azure CLI használatával felügyelt lemezeket. Is [adatlemezek hozzáadása](add-disk.md) további tárterületet biztosít terület, ekkor is bővítheti már meglévő adatlemez. Az alapértelmezett virtuális merevlemezt az operációs rendszer (OS) mérete általában 30 GB-os az Azure-beli Linuxos virtuális gépre. 

> [!WARNING]
> Mindig győződjön meg arról, hogy biztonsági másolatot az adatokat, mielőtt végrehajtaná a lemez átméretezése operations. További információkért lásd: [az Azure-ban a Linux rendszerű virtuális gépek biztonsági mentése](tutorial-backup-vms.md).

## <a name="expand-an-azure-managed-disk"></a>Egy felügyelt Azure-lemez kibontása
Győződjön meg arról, hogy rendelkezik-e a legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az-login).

Ez a cikk az Azure-ban egy meglévő virtuális gép legalább egy adatlemezt csatolni, és előkészíti igényel. Ha Ön még nem rendelkezik egy virtuális Gépet, amelyet használhat, lásd: [létrehozás és adatlemezekkel rendelkező virtuális gép előkészítése](tutorial-manage-disks.md#create-and-attach-disks).

A következő minták cserélje le a példában a paraméterek nevei például *myResourceGroup* és *myVM* a saját értékeire.

1. Futó virtuális gép nem hajtható végre műveleteket a virtuális merevlemezeket. A virtuális gép felszabadítása [az vm deallocate](/cli/azure/vm#az-vm-deallocate). Az alábbi példa felszabadítja a virtuális gép nevű *myVM* az erőforráscsoport neve *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > A virtuális gép, bontsa ki a virtuális merevlemez fel kell szabadítani. A virtuális gép leállítása `az vm stop` nem mentesíti a számítási erőforrásokat. A számítási erőforrások felszabadítása használja `az vm deallocate`.

1. A felügyelt lemezek listájának megtekintéséhez egy erőforráscsoportban [az Lemezlista](/cli/azure/disk#az-disk-list). Az alábbi példában a felügyelt lemezek listáját jeleníti meg a nevű erőforráscsoportban *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Bontsa ki a szükséges lemezt a [az disk update](/cli/azure/disk#az-disk-update). Az alábbi példa kibővíti a felügyelt lemez nevű *myDataDisk* való *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Amikor kibővít egy felügyelt lemezt, a frissített mérete felfelé kerekíti a rendszer a legközelebbi felügyelt lemez méretét. Az egy táblázatot a rendelkezésre álló felügyelt lemez méretét és a réteg [Azure Managed Disks – áttekintés – árak és számlázás](../windows/managed-disks-overview.md).

1. Indítsa el a virtuális Gépet a [az vm start](/cli/azure/vm#az-vm-start). A következő példa elindítja a virtuális gép nevű *myVM* az erőforráscsoport neve *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Bontsa ki a lemezpartíciót és fájlrendszer
Egy kiterjesztett lemez használatára, bontsa ki az alapul szolgáló partíció vagy fájlrendszer.

1. Ssh-KAPCSOLATOT a virtuális gép a megfelelő hitelesítő adatokkal. Láthatja, hogy a virtuális gép nyilvános IP-címét [az vm show](/cli/azure/vm#az-vm-show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Bontsa ki az alapul szolgáló partíció vagy fájlrendszer.

    a. Ha a lemez már csatlakoztatva van, válassza le:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Használat `parted` tárolólemez-információk megtekintéséhez, és méretezze át a partíció:

    ```bash
    sudo parted /dev/sdc
    ```

    A meglévő partíciók elrendezését az kapcsolatos információk megtekintéséhez `print`. A kimenet a következő példának, amely bemutatja az alapul szolgáló lemezének mérete 215 GB hasonlít:

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

    d. Adja meg a kilépéshez `quit`.

1. A partícióhoz, méretezhető, a partíció konzisztencia-ellenőrzése `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Méretezze át a fájlrendszer- `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Például csatlakoztassa a partíciót a kívánt helyre `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Az operációsrendszer-lemez át lett méretezve ellenőrzéséhez használja a `df -h`. Az alábbi példa kimenetében látható az adatmeghajtó */dev/sdc1* 200 GB-os már:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>További lépések
* Ha további tárhelyre van szüksége, [adatlemezek hozzáadása Linux rendszerű virtuális gép](add-disk.md). 
* Lemeztitkosítás kapcsolatos további információkért lásd: [egy Linux rendszerű virtuális gépen az Azure CLI-vel lemezek titkosítása](encrypt-disks.md).
