---
title: Azure-fájlmegosztás visszaállítása az Azure CLI-vel
description: Ismerje meg, hogyan állíthatja vissza az Azure-fájlmegosztás biztonsági mentését az Azure CLI használatával a Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 8d91031ff9ee0d4f32cc74ff7031d39ddab3e2b4
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294408"
---
# <a name="restore-azure-file-shares-with-cli"></a>Azure-fájlmegosztás visszaállítása a parancssori felülettel

Az Azure parancssori felülete (CLI) parancssori felületet biztosít az Azure-erőforrások kezeléséhez. Ez nagyszerű eszköz az Azure-erőforrások használatára szolgáló egyéni automatizálás kialakításához. Ez a cikk azt ismerteti, hogyan lehet visszaállítani egy teljes fájlmegosztást vagy adott fájlokat a [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) szolgáltatás által az Azure CLI használatával létrehozott visszaállítási pontról. Az [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) vagy az [Azure Portal](backup-afs.md) használatával is elvégezheti ezeket a lépéseket.

Az oktatóanyag végén megtudhatja, hogyan hajthatja végre az alábbi műveleteket az Azure CLI használatával:

* Biztonsági másolatba mentett Azure-fájlmegosztás visszaállítási pontjainak megtekintése
* Teljes Azure-fájlmegosztás visszaállítása
* Egyéni fájlok vagy mappák visszaállítása

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A parancssori felület helyi telepítéséhez és használatához az Azure CLI 2.0.18-as vagy újabb verzióját kell futtatnia. A CLI verziójának megkereséséhez `run az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már rendelkezik egy Azure-fájlmegosztás, amelyről biztonsági másolatot készít a Azure Backup szolgáltatás. Ha még nem rendelkezik ilyennel, tekintse meg az [Azure-fájlmegosztás biztonsági mentése a CLI-vel](backup-afs-cli.md) a fájlmegosztás biztonsági mentésének konfigurálásához című részt. Ebben a cikkben a következő erőforrásokat fogjuk használni:

| Fájlmegosztás  | Tárfiók | Region (Régió) | Részletek                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount*      | EastUS | Az eredeti forrás biztonsági mentése Azure Backup használatával                 |
| *azurefiles1* | *afaccount1*      | EastUS | Másodlagos hely helyreállításához használt cél forrás |

A fájlmegosztás hasonló struktúrájának használatával kipróbálhatja a jelen dokumentumban ismertetett visszaállítások különböző típusait.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Helyreállítási pontok beolvasása az Azure-fájlmegosztás számára

A biztonsági másolatban szereplő fájlmegosztás összes helyreállítási pontjának listázásához használja az az [Backup recoverypoint List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) parancsmagot.

Az alábbi példa lekéri a helyreállítási pontok listáját a *azurefiles* -fájlmegosztás számára a *afsaccount* Storage-fiókban.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

A fenti parancsmagot a tároló és az elem "felhasználóbarát neve" használatával is végrehajthatja a következő két további paraméter megadásával:

* **--Backup-Management-Type**: *azurestorage*
* **--munkaterhelés-Type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Az eredményhalmaz a helyreállítási pontok listáját tartalmazza, az egyes visszaállítási pontokra vonatkozó idő-és konzisztencia-részletekkel.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

A kimenetben található **Name** attribútum a helyreállítási pont neveként a helyreállítási műveletekben a **--RP-Name** paraméter értékeként használható.

## <a name="full-share-recovery-using-azure-cli"></a>Teljes megosztás helyreállítása az Azure CLI-vel

Ezzel a visszaállítási lehetőséggel visszaállíthatja a teljes fájlmegosztást az eredeti vagy egy másik helyen.

Adja meg a következő paramétereket a visszaállítási műveletek elvégzéséhez:

* **– a tároló neve** annak a Storage-fióknak a neve, amely az eredeti fájlmegosztás biztonsági másolatát üzemelteti. A tároló **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* **– az elemnév** a visszaállítási művelethez használni kívánt biztonsági másolat eredeti fájlmegosztás neve. A biztonsági másolatban szereplő elem **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.

### <a name="restore-full-share-to-the-original-location"></a>Teljes megosztás visszaállítása az eredeti helyre

Ha eredeti helyre állítja vissza, nem kell megadnia a célként kapcsolódó paramétereket. Csak a **feloldási ütközést** kell megadni.

Az alábbi példa az az [Backup Restore Restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) parancsmagot használja a Restore Mode set to *originallocation* értékre, hogy visszaállítsa a *azurefiles* -fájlmegosztást az eredeti helyen a 932883129628959823 helyreállítási pont használatával, amelyet az [Azure-fájlmegosztás helyreállítási pontjainak beolvasása](#fetch-recovery-points-for-the-azure-file-share)során kaptunk:

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

A kimenet **Name** attribútuma megegyezik a Backup szolgáltatás által a visszaállítási művelethez létrehozott feladatokhoz használt névvel. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmagot.

### <a name="restore-full-share-to-an-alternate-location"></a>Teljes megosztás visszaállítása másik helyre

Ezzel a lehetőséggel visszaállíthat egy fájlmegosztást egy másik helyre, és megtarthatja az eredeti fájlmegosztást. A másodlagos hely helyreállításához a következő paramétereket kell megadni:

* **--Target-Storage-Account**: az a Storage-fiók, amelyhez a biztonsági másolat tartalma visszaállítható. A célként megadott Storage-fióknak a tárolóval megegyező helyen kell lennie.
* **--Target-file-share**: a célként megadott Storage-fiókon belüli fájlmegosztás, amelyhez a biztonsági másolat tartalma vissza lesz állítva.
* **--Target-Folder**: az a fájlmegosztás alatt lévő mappa, amelybe az adat vissza lett állítva. Ha a biztonsági másolatban lévő tartalmat vissza szeretné állítani egy gyökérkönyvtárba, adja meg a célmappa értékeit üres karakterláncként.
* **--hárítsa-Conflict**: utasítás, ha ütközik a visszaállított adattal. A **felülírás** vagy **kihagyás**elfogadása.

Az alábbi példa az [az Backup Restore Restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) és a Restore Mode *alternatelocation* használatával állítja vissza a *azurefiles* fájlmegosztást a *afsaccount* Storage-fiókban, hogy *azurefiles1 "* fájlmegosztás legyen a *afaccount1* Storage-fiókban.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

A kimenet **Name** attribútuma megegyezik a Backup szolgáltatás által a visszaállítási művelethez létrehozott feladatokhoz használt névvel. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmagot.

## <a name="item-level-recovery"></a>Elemszintű helyreállítás

Ezzel a visszaállítási lehetőséggel visszaállíthatja az egyes fájlokat vagy mappákat az eredeti vagy egy másik helyen.

Adja meg a következő paramétereket a visszaállítási műveletek elvégzéséhez:

* **– a tároló neve** annak a Storage-fióknak a neve, amely az eredeti fájlmegosztás biztonsági másolatát üzemelteti. A tároló **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* **– az elemnév** a visszaállítási művelethez használni kívánt, eredeti fájlmegosztás biztonsági másolatának neve. A biztonsági másolatban szereplő elem **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.

A helyreállítani kívánt elemekhez a következő paramétereket kell megadni:

* **SourceFilePath**: a fájl abszolút elérési útja, amelyet a fájlmegosztás keretén belül helyre kell állítani karakterláncként. Ez az elérési út az az [Storage File Download](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) vagy az [Storage file](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI parancsokban használt elérési út.
* **SourceFileType**: válassza ki, hogy ki van-e választva egy könyvtár vagy fájl. **Címtár** vagy **fájl**elfogadása.
* **ResolveConflict**: utasítás, ha a visszaállított adattal ütközik. A **felülírás** vagy **kihagyás**elfogadása.

### <a name="restore-individual-filesfolders-to-the-original-location"></a>Egyedi fájlok/mappák visszaállítása az eredeti helyre

Az az [Backup Restore Restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) parancsmagot használja a Restore Mode set to *originallocation* értékre, hogy az adott fájlokat vagy mappákat az eredeti helyükre állítsa vissza.

A következő példa visszaállítja a *"RestoreTest. txt"* fájlt az eredeti helyen: a *azurefiles* fájlmegosztás.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

A kimenet **Name** attribútuma megegyezik a Backup szolgáltatás által a visszaállítási művelethez létrehozott feladatokhoz használt névvel. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmagot.

### <a name="restore-individual-filesfolders-to-an-alternate-location"></a>Egyes fájlok/mappák visszaállítása másik helyre

Ha meghatározott fájlokat vagy mappákat kíván visszaállítani egy másik helyre, használja az az Backup Restore [Restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) parancsmagot a Restore Mode (visszaállítási mód) beállítással a *alternatelocation* értékre, és adja meg a következő célként kapcsolódó paramétereket:

* **--Target-Storage-Account**: az a Storage-fiók, amelyhez a biztonsági másolat tartalma visszaállítható. A célként megadott Storage-fióknak a tárolóval megegyező helyen kell lennie.
* **--Target-file-share**: a célként megadott Storage-fiókon belüli fájlmegosztás, amelyhez a biztonsági másolat tartalma vissza lesz állítva.
* **--Target-Folder**: az a fájlmegosztás alatt lévő mappa, amelybe az adat vissza lett állítva. Ha a biztonsági másolatban lévő tartalmat vissza szeretné állítani egy gyökérkönyvtárba, adja meg a célmappa értékét üres karakterláncként.

A következő példa visszaállítja a *RestoreTest. txt* fájlt, amely eredetileg szerepel a *azurefiles* fájlmegosztás egy másik helyen: *a azurefiles1-* fájlmegosztás tárolt *afaccount1* *restoredata* mappája.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

A kimenet **Name** attribútuma megegyezik a Backup szolgáltatás által a visszaállítási művelethez létrehozott feladatokhoz használt névvel. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmagot.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [kezelheti az Azure-fájlmegosztás biztonsági másolatait az Azure CLI-vel](manage-afs-backup-cli.md)
