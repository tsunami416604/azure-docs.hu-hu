---
title: Virtuálisgép-lemez visszaállítása az Azure Backuppal
description: Megtudhatja, hogyan állíthatja vissza a lemezt, valamint hogyan hozhat létre és állíthat helyre egy virtuális gépet az Azure-ban a Backup és a Recovery Services használatával.
services: backup
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: tutorial
ms.date: 4/17/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 76b5a5743fd6ac715eca45e49cc08d5006522ad0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871541"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Lemez visszaállítása és helyreállított virtuális gép létrehozása az Azure-ban
Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak egyes fájlokat. Ez a cikk a teljes virtuális gép parancssori felülettel való visszaállításának módját ismerteti. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Helyreállítási pontok listázása és kiválasztása
> * Lemez visszaállítása egy helyreállítási pontból
> * Virtuális gép létrehozása a visszaállított lemezről

További információért a PowerShell a lemez visszaállításhoz és egy helyreállított virtuális gép létrehozásához való használatáról, tekintse meg a [Azure-beli virtuális gépek biztonsági mentése és visszaállítása a PowerShell-lel](backup-azure-vms-automation.md#restore-an-azure-vm) című részt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.18-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 


## <a name="prerequisites"></a>Előfeltételek
Ehhez az oktatóanyaghoz olyan Linux rendszerű virtuális gépre van szükség, amelyet az Azure Backup véd. A virtuális gép véletlen törlésének és a helyreállítási folyamatnak a szimulálásához hozzon létre egy virtuális gépet a helyreállítási pont egyik lemezéről. Ha szüksége van egy linuxos virtuális gépre, amelyet az Azure Backup véd, lásd: [Virtuális gép biztonsági mentése az Azure-ban a parancssori felület használatával](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>A biztonsági mentés áttekintése
Amikor az Azure biztonsági mentést kezdeményez, a virtuális gépen futó biztonsági mentési bővítmény időponthoz kötött pillanatképet készít. A biztonsági mentési bővítmény az első biztonsági mentés kérésekor települ a virtuális gépre. Az Azure Backup akkor is tud pillanatképet készíteni az alapul szolgáló tárolóról, ha a virtuális gép a biztonsági mentés közben nem fut.

Alapértelmezés szerint az Azure Backup a fájlrendszerrel konzisztens biztonsági másolatot készít. Amikor az Azure Backup elkészítette a pillanatképet, az adatok átkerülnek a helyreállítási tárba. A maximális hatékonyság érdekében az Azure Backup csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta változtak.

Ha az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.


## <a name="list-available-recovery-points"></a>Rendelkezésre álló helyreállítási pontok listázása
A lemez visszaállításához ki kell választania egy helyreállítási pontot a helyreállítási adatok forrásaként. Az alapértelmezett szabályzat minden nap létrehoz egy-egy helyreállítási pontot, és mindegyiket 30 napig tartja meg. Így fenntartható egy helyreállítási pontokból álló készlet, amelyből kiválaszthat egy adott időpontot a helyreállításhoz. 

Az elérhető helyreállítási pontok listájának megjelenítéséhez használja az [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) parancsot. A lemezek a helyreállítási pont **nevével** állíthatók helyre. Ebben az oktatóanyagban a legutóbbi rendelkezésre álló helyreállítási pontot használjuk. A `--query [0].name` paraméter az alábbiak szerint választja ki a legutóbbi helyreállítási pont nevét:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Virtuálisgép-lemez visszaállítása
A lemez a helyreállítási pontból történő visszaállításához először hozzon létre egy Azure Storage-fiókot. Ez a tárfiók a visszaállított lemez tárolására szolgál. A további lépésekben a visszaállított lemezzel létrehoz egy virtuális gépet.

1. A tárfiók létrehozásához használja az [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) parancsot. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Cserélje le *mystorageaccount* kifejezést egy saját egyedi névre:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Állítsa vissza a lemezt a helyreállítási pontról az [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) paranccsal. Cserélje le *mystorageaccount* kifejezést az előző paranccsal létrehozott tárfiók nevére. Cserélje le a *myRecoveryPointName* kifejezést az előzőleg használt [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) parancs kimenetéből származó helyreállításipont-névre:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>A visszaállítási feladat monitorozása
A visszaállítási feladat állapotának monitorozásához használja az [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) parancsot:

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

A kimenet a következő példához hasonló, amelyben látható, hogy a visszaállítási feladat *InProgress* (Folyamatban) állapotban van:

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Ha a visszaállítási feladat *állapotánál* a *Befejezve* érték jelenik meg, akkor a lemez vissza lett állítva a tárfiókba.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>A visszaállított lemez átalakítása felügyelt lemezzé
A visszaállítási feladat nem felügyelt lemezt hoz létre. Ha virtuális gépet szeretne létrehozni a lemezről, először át kell alakítania felügyelt lemezzé.

1. Kérje le a tárfiók kapcsolatadatait az [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string) paranccsal. Cserélje le a *mystorageaccount* kifejezést a tárfiók nevére az alábbiak szerint:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. A rendszer a tárfiókban őrzi a nem felügyelt lemezt. Az alábbi parancsok lekérik a nem felügyelt lemez adatait, és létrehoznak egy *uri* nevű változót, amely a következő lépésben, a felügyelt lemez létrehozásához szükséges.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Most létrehozhatja a felügyelt lemezt a helyreállított lemezről az [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-create) paranccsal. Az előző lépésből származó *uri* változó lesz a felügyelt lemez forrása.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Most, hogy a visszaállított lemezről létrehozta a felügyelt lemezt, távolítsa el a nem felügyelt lemezt és a tárfiókot az [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-delete) paranccsal. Cserélje le a *mystorageaccount* kifejezést a tárfiók nevére az alábbiak szerint:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Virtuális gép létrehozása a visszaállított lemezről
Az utolsó lépésben egy virtuális gépet hoz létre a felügyelt lemezről.

1. Hozzon létre egy virtuális gépet a felügyelt lemezről az [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) paranccsal az alábbiak szerint:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Ha ellenőrizni szeretné, hogy létrejött-e a virtuális gép a helyreállított lemezről, listázza az erőforráscsoportban szereplő virtuális gépeket az [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) paranccsal az alábbiak szerint:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban visszaállított egy lemezt a helyreállítási pontról, és létrehozott egy virtuális gépet a lemezről. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Helyreállítási pontok listázása és kiválasztása
> * Lemez visszaállítása egy helyreállítási pontból
> * Virtuális gép létrehozása a visszaállított lemezről

Folytassa a következő oktatóanyaggal, amely egy helyreállítási pont egyes fájljainak visszaállítását ismerteti.

> [!div class="nextstepaction"]
> [Fájlok visszaállítása Azure-beli virtuális gépekre](tutorial-restore-files.md)

