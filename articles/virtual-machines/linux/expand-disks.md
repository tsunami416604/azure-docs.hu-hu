---
title: Virtuális merevlemezek bővítése Linux os virtuális gépen
description: Ismerje meg, hogyan bonthat ki virtuális merevlemezeket linuxos virtuális gépen az Azure CLI segítségével.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 1295c5276f0f342323acf8d86eaaf9f785af3e9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945186"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Bővítse ki a virtuális merevlemezeket linuxos virtuális gépen az Azure CLI-vel

Ez a cikk ismerteti, hogyan bontsa ki a felügyelt lemezek egy Linux virtuális gép (VM) az Azure CLI. A további tárhely érdekében [adatlemezeket](add-disk.md) is hozzáadhat, és kiis bonthatja a meglévő adatlemezeket. Az operációs rendszer (OS) alapértelmezett virtuális merevlemez-mérete általában 30 GB egy Linux virtuális gépen az Azure-ban. 

> [!WARNING]
> Mindig győződjön meg arról, hogy a fájlrendszer kifogástalan állapotban van, a lemezpartíció-tábla típusa támogatja az új méretet, és a lemez átméretezési műveleteinek végrehajtása előtt győződjön meg arról, hogy az adatokról biztonsági másolatot készít. További információ: [A Linux virtuális gépek biztonsági másolatot az Azure-ban.](tutorial-backup-vms.md) 

## <a name="expand-an-azure-managed-disk"></a>Azure felügyelt lemez kibontása
Győződjön meg arról, hogy a legújabb [Azure CLI](/cli/azure/install-az-cli2) telepítve van, és az az login használatával be van jelentkezve egy Azure-fiókba. [az login](/cli/azure/reference-index#az-login)

Ez a cikk egy meglévő virtuális gép az Azure-ban legalább egy adatlemez csatlakoztatva van, és előkészített. Ha még nem rendelkezik használható virtuális géptel, olvassa el a [Virtuális gép létrehozása és előkészítése adatlemezekkel című témakört.](tutorial-manage-disks.md#create-and-attach-disks)

A következő mintákban cserélje le a példaparaméter-neveket, például *a myResourceGroup-ot* és *a myVM-et* a saját értékeivel.

1. A virtuális merevlemezeken végzett műveletek nem hajthatók végre a virtuális gép futásával. A virtuális gép felszabadítása az [vm felszabadításával.](/cli/azure/vm#az-vm-deallocate) A következő példa felszabadítja a *myVM* nevű virtuális gép a *myResourceGroup*nevű erőforráscsoportban:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > A virtuális gépet fel kell osztani a virtuális merevlemez kibontásához. A virtuális gép `az vm stop` leállítása nem szabadítja fel a számítási erőforrásokat. A számítási erőforrások felszabadításához használja a használatát. `az vm deallocate`

1. Az [az lemezlistával](/cli/azure/disk#az-disk-list)rendelkező erőforráscsoport ban lévő kezelt lemezek listájának megtekintése . A következő példa a *myResourceGroup*nevű erőforráscsoport felügyelt lemezeinek listáját jeleníti meg:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Bontsa ki a szükséges lemezt az [az lemezfrissítéssel.](/cli/azure/disk#az-disk-update) A következő példa a *myDataDisk* nevű felügyelt lemezt *200* GB-ra bővíti ki:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Felügyelt lemez kibontásakor a frissített méret a legközelebbi kezelt lemezméretre lesz felfelé kerekítve. A rendelkezésre álló felügyelt lemezméretek és -rétegek táblázatát az [Azure felügyelt lemezek áttekintése – díjszabás és számlázás című témakörben találja.](../windows/managed-disks-overview.md)

1. Indítsa el a virtuális gép [az vm start](/cli/azure/vm#az-vm-start). A következő példa elindítja a *myVM* nevű virtuális gép a *myResourceGroup*nevű erőforráscsoportban:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Lemezpartíció és fájlrendszer kibontása
Kibontott lemez használatához bontsa ki az alapul szolgáló partíciót és fájlrendszert.

1. SSH a virtuális gép a megfelelő hitelesítő adatokkal. Láthatjuk a nyilvános IP-címét a virtuális gép [az vm show:](/cli/azure/vm#az-vm-show)

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Bontsa ki az alapul szolgáló partíciót és fájlrendszert.

    a. Ha a lemez már csatlakoztatva van, ne csatlakoztassa:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. A `parted` lemezadatok megtekintésére és a partíció átméretezésére használható:

    ```bash
    sudo parted /dev/sdc
    ```

    A meglévő partícióelrendezésre `print`vonatkozó információk megtekintése a alkalmazással A kimenet hasonló a következő példához, amely azt mutatja, hogy az alapul szolgáló lemez 215 GB:

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

    c. Bontsa `resizepart`ki a partíciót a segítségével. Adja meg az új partíció partíciószámát *(1)* és az új partíció méretét:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. A kilépéshez `quit`írja be a be.

1. A partíció átméretezése után ellenőrizze `e2fsck`a partíció konzisztenciáját a következővel:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. A fájlrendszer átméretezése a következővel: `resize2fs`

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Csatlakoztassa a partíciót a kívánt `/datadrive`helyre, például:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Az adatlemez átméretezésének ellenőrzéséhez `df -h`használja a használatát. A következő példa kimeneti kimenet imázsát mutatja, hogy a /dev/sdc1 adatmeghajtó jelenleg 200 GB:The following example output shows the data drive */dev/sdc1* is now 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>További lépések
* Ha további tárhelyre van szüksége, [adatlemezeket is hozzáadhat egy Linux virtuális géphez.](add-disk.md) 
* A lemeztitkosításról további információt az Azure Disk Encryption for Linux virtuális gépek című [témakörben talál.](disk-encryption-overview.md)
