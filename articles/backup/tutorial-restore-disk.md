---
title: "Állítsa vissza egy virtuális gép lemezt az Azure Backup szolgáltatással |} Microsoft Docs"
description: "Megtudhatja, hogyan állítsa helyre a lemezt, majd a helyreállítandó virtuális gép létrehozása az Azure-ban a biztonsági mentési és helyreállítási szolgáltatásokhoz."
services: backup, virtual-machines
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/28/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9bc6da13786eb9eb6186ceadf0432b3a3ec2c941
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Állítsa vissza egy lemezt, és a helyreállított virtuális gép létrehozása az Azure-ban
Az Azure Backup georedundáns helyreállítás tárolók tárolt helyreállítási pontokat hoz létre. Helyreállításakor a helyreállítási pont, az egész virtuális gép vagy a fájlokat is helyreállíthatja. Ez a cikk azt ismerteti, hogyan teljes virtuális gép visszaállítására. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Lista, és válassza a helyreállítási pontok
> * Lemez visszaállíthatja őket egy helyreállítási pontból
> * Hozzon létre egy virtuális Gépet a visszaállított lemezről

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.18 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag egy Linux virtuális Gépet, amely védett az Azure Backup szolgáltatással van szükség. Egy virtuális gép véletlen törlés és a helyreállítási folyamat szimulálására, hoz létre egy virtuális Gépet a helyreállítási pont lemezen. Ha egy Linux virtuális Gépet, amely védett az Azure Backup szolgáltatással van szüksége, tekintse meg [készítsen biztonsági másolatot a virtuális gép az Azure-ban a parancssori felület](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>A biztonsági mentés áttekintése
Ha Azure biztonsági másolatot, a biztonsági mentési bővítményt a virtuális Gépre időpontban pillanatképet készít. A tartalék mellék a virtuális Gépen telepítve van, ha az első biztonsági mentés van szükség. Azure biztonsági mentés is is pillanatkép készítése a mögöttes tároló Ha a virtuális gép nem fut, amikor megtörténik a biztonsági mentés.

Alapértelmezés szerint Azure biztonsági mentési időt vesz igénybe a fájlrendszer konzisztens biztonsági mentése. Amennyiben az Azure Backup a pillanatfelvételt, az adatok átvitele történik meg a Recovery Services-tároló. Hatékonyságának maximalizálása érdekében Azure biztonsági mentési azonosítja, és csak az adatok a korábbi biztonsági mentés óta módosult blokkok átvitele.

Ha az adatok átvitele befejeződött, a rendszer eltávolítja a pillanatkép, és egy helyreállítási pontot hoz létre.


## <a name="list-available-recovery-points"></a>Rendelkezésre álló helyreállítási pontok listája
A lemez visszaállításához a helyreállítási pont helyreállítási adatok forrásaként választja. Az alapértelmezett házirend létrehoz egy helyreállítási pontot minden nap, és 30 napig őrzi meg őket, megtarthatja a helyreállítási pontok olyan készlete, amely lehetővé teszi, hogy válasszon ki egy adott pontot időben a helyreállításhoz. 

Rendelkezésre álló helyreállítási pontok listájának megjelenítéséhez használja [az biztonsági mentési recoverypoint lista](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list). A helyreállítási pont **neve** lemezek visszaállítására használt. Az oktatóanyag azt szeretnénk, a legutóbbi helyreállítási pont érhető el. A `--query [0].name` paraméter választja ki a legutóbbi helyreállítási pont neve az alábbiak szerint:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Állítsa vissza egy Virtuálisgép-lemez
A helyreállítási pontból állítsa vissza a lemezt, akkor először hozzon létre egy Azure storage-fiók. Ez a tárfiók a visszaállított lemez tárolására szolgál. A további lépések a visszaállított lemezt egy virtuális gép létrehozásához használt.

1. A storage-fiók létrehozásához használja [az storage-fiók létrehozása](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create). A tárfiók nevét kell csak kisbetű szerepelhet, és legyen globálisan egyedülálló. Cserélje le *mystorageaccount* saját egyedi névvel rendelkező:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. A lemez visszaállíthatók a helyreállítási pont a [az biztonsági másolat visszaállítása a restore-lemezek](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks). Cserélje le *mystorageaccount* a fenti paranccsal létrehozott tárfiók nevével. Cserélje le *myRecoveryPointName* a helyreállítási pont nevű kimenete az előző a beolvasott [az biztonsági mentési recoverypoint lista](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list) parancs:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>A visszaállítási feladat figyeléséhez
Helyreállítási feladat állapotának figyelése [az biztonsági mentési feladat lista](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az_backup_job_list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

A kimeneti hasonlít a következő példának, amely bemutatja a visszaállítási folyamat *esetbejegyzések*:

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Ha a *állapot* a visszaállítási feladat jelentések *befejezve*, a lemez vissza lett állítva a tárfiók.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>A visszaállított lemez konvertálása felügyelt lemezt
A visszaállítási feladatot hoz létre egy nem felügyelt lemezt. Ahhoz, hogy a virtuális gép létrehozása a lemezről, azt kell először konvertálható felügyelt lemezes.

1. A tárfiók kapcsolati információkhoz [az storage-fiók megjelenítése-kapcsolat-karakterlánc](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_show_connection_string). Cserélje le *mystorageaccount* nevű, a tárolási fiók az alábbiak szerint:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. A nem felügyelt lemezt a tárfiók védett. Az alábbi parancsokat a nem kezelt lemez adatainak beolvasása, és hozzon létre egy változót nevű *uri* , amely szerepel a következő lépés a felügyelt lemezek létrehozása során.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Most a helyreállított lemezt hozhat létre egy felügyelt lemezt [az lemez létrehozása](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az_disk_create). A *uri* változó az előző lépésben forrásaként használható a felügyelt lemezhez.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Most már rendelkezik egy felügyelt lemezt a visszaállított lemezről, mert a nem felügyelt lemezen és fiókot tisztítása [az tárfiók törlése](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete). Cserélje le *mystorageaccount* nevű, a tárolási fiók az alábbiak szerint:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Hozzon létre egy virtuális Gépet a visszaállított lemezről
Az utolsó lépést, ha egy virtuális Gépet a felügyelt lemezről.

1. A felügyelt lemezt a virtuális gép létrehozása [az virtuális gép létrehozása](/cli/azure/vm?view=azure-cli-latest#az_vm_create) az alábbiak szerint:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Annak ellenőrzéséhez, hogy létrejött-e a virtuális Gépet a helyreállított lemezről, a virtuális gépek az erőforráscsoport listában [az vm lista](/cli/azure/vm?view=azure-cli-latest#az_vm_list) az alábbiak szerint:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy lemezt vissza ebből a helyreállítási pont, és a lemezről egy virtuális gép létrehozása. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Lista, és válassza a helyreállítási pontok
> * Lemez visszaállíthatja őket egy helyreállítási pontból
> * Hozzon létre egy virtuális Gépet a visszaállított lemezről

Előzetes tájékozódhat az egyes fájlok visszaállítása során egy helyreállítási pontot a következő oktatóanyagot.

> [!div class="nextstepaction"]
> [Fájlok visszaállítása az Azure virtuális géphez](tutorial-restore-files.md)

