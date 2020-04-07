---
title: Az Azure-fájlmegosztások visszaállítása az Azure CLI-vel
description: Megtudhatja, hogy az Azure CLI használatával hogyan állíthatja vissza a biztonsági másolatot az Azure-fájlmegosztásokat a Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 980044011e3417a2aff8447a939e02299923da38
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757092"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Az Azure-fájlmegosztások visszaállítása az Azure CLI-vel

Az Azure CLI parancssori élményt nyújt az Azure-erőforrások kezeléséhez. Ez egy nagyszerű eszköz az Azure-erőforrások használatához egyéni automatizálás létrehozásához. Ez a cikk bemutatja, hogyan állíthat vissza egy teljes fájlmegosztást vagy adott fájlokat az [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) által az Azure CLI használatával létrehozott visszaállítási pontról. Az [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) vagy az [Azure Portal](backup-afs.md) használatával is elvégezheti ezeket a lépéseket.

A cikk végén megtudhatja, hogyan hajthatja végre a következő műveleteket az Azure CLI-vel:

* Tekintse meg a biztonsági másolatot, az Azure-fájlmegosztás visszaállítási pontjait.
* Teljes Azure-fájlmegosztás visszaállítása.
* Az egyes fájlok vagy mappák visszaállítása.

>[!NOTE]
> Az Azure Backup mostantól támogatja több fájl vagy mappa visszaállítását az eredeti vagy egy másik helyre az Azure CLI használatával. További információért olvassa el a [dokumentum több fájl vagy mappa visszaállítása eredeti vagy alternatív helyre](#restore-multiple-files-or-folders-to-original-or-alternate-location) című szakaszát.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A parancssori felület helyi telepítéséhez és használatához az Azure CLI 2.0.18-as vagy újabb verzióját kell futtatnia. A parancssori felület verziójának megkereséséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már rendelkezik egy Azure-fájlmegosztás, amely az Azure Backup biztonsági mentési. Ha még nem rendelkezik ilyennel, olvassa el [az Azure fájlmegosztások biztonsági mentése a CLI-vel](backup-afs-cli.md) című témakört a fájlmegosztás biztonsági mentésének konfigurálásához című témakörben. Ebben a cikkben a következő forrásokat használja:

| Fájlmegosztás  | Tárfiók | Régió | Részletek                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount*      | KeletUSA | Az eredeti forrás ról az Azure Backup használatával készül                 |
| *azurefiles1* | *afaccount1*      | KeletUSA | Alternatív hely-helyreállításhoz használt célforrás |

A fájlmegosztások hoz hasonló struktúrát használhat a cikkben ismertetett különböző típusú visszaállítások kipróbálásához.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Helyreállítási pontok beolvasása az Azure-fájlmegosztáshoz

Az [az biztonsági mentés helyreállításipont-listájának](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) parancsmagja segítségével sorolja fel a biztonsági másolat fájlmegosztás összes helyreállítási pontját.

A következő példa lekéri az *azurefiles* fájlmegosztás helyreállítási pontjainak listáját az *afsaccount* tárfiókban.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Az előző parancsmast a tároló és az elem rövid nevével is futtathatja a következő két további paraméter megadásával:

* **--backup-management-type**: *azurestorage*
* **--munkaterhelés típusa:** *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Az eredményhalmaz a helyreállítási pontok listája az egyes visszaállítási pontok idő- és konzisztencia-részleteivel.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

A **kimenetben** lévő Name attribútum megfelel a helyreállítási pont nevének, amely a helyreállítási műveletekben a **--rp-name** paraméter értékeként használható.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Teljes megosztás helyreállítása az Azure CLI használatával

Ezzel a visszaállítási lehetőséggel visszaállíthatja a teljes fájlmegosztást az eredeti vagy egy másik helyen.

Adja meg a következő paramétereket a visszaállítási műveletek végrehajtásához:

* **--container-name**: Annak a tárfióknak a neve, amely a biztonsági másolatot tartalmazó eredeti fájlmegosztást tárolja. A tároló nevének vagy rövid nevének beolvasásához használja az [az biztonsági mentési tárolólista](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* **--elemnév:** A visszaállítási művelethez használni kívánt eredeti fájlmegosztás neve. A biztonsági másolat elem nevének vagy rövid nevének beolvasásához használja az [az biztonsági mentési elemlista](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.

### <a name="restore-a-full-share-to-the-original-location"></a>Teljes megosztás visszaállítása az eredeti helyre

Amikor visszaállít egy eredeti helyre, nem kell megadnia a célhoz kapcsolódó paramétereket. Csak **ütközésfeloldási** kell.

A következő példa az [az biztonsági mentés visszaállítása-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) parancsmag használatával visszaállítási mód *beállítása az eredeti helyre* az *azurefiles* fájlmegosztás az eredeti helyen. A 932883129628959823 helyreállítási pontot használja, amelyet [az Azure-fájlmegosztás helyreállítási pontjaiban](#fetch-recovery-points-for-the-azure-file-share)kapott:

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

A **név** attribútum a kimenetben megegyezik a feladat nevét, amely a biztonsági mentési szolgáltatás által létrehozott a visszaállítási művelethez. A feladat állapotának nyomon követéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Teljes megosztás visszaállítása másik helyre

Ezzel a beállítással visszaállíthatja a fájlmegosztást egy másik helyre, és megtarthatja az eredeti fájlmegosztást. Adja meg a következő paramétereket az alternatív hely helyreállítása érdekében:

* **--target-storage-account**: Az a tárfiók, amelyre a biztonsági másolatot készítő tartalom visszaáll. A céltárfióknak ugyanazon a helyen kell lennie, mint a tárolónak.
* **--target-file-share**: Az a fájlmegosztás a céltárfiókon belül, amelyre a biztonsági másolatot készítő tartalom visszaáll.
* **--target-folder**: Az a mappa a fájlmegosztás alatt, amelyre az adatokat visszaállítják. Ha a biztonsági másolatot tartalmazó tartalmat vissza kell állítani egy gyökérmappába, adja meg a célmappa értékeit üres karakterláncként.
* **--resolve-conflict**: Utasítás, ha ütközés van a visszaállított adatokkal. Elfogadja **a Felülírást** vagy **a Kihagyás műveletet.**

A következő példa az [a biztonsági mentés visszaállítása-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) visszaállítási módalternatívhelyként használja az *azurefiles* fájlmegosztás visszaállításához az *afsaccount* storage-fiókban az *azurefiles1"* fájlmegosztáshoz az *afaccount1* tárfiókban. *alternatelocation*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

A **név** attribútum a kimenetben megegyezik a feladat nevét, amely a biztonsági mentési szolgáltatás által létrehozott a visszaállítási művelethez. A feladat állapotának nyomon követéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

## <a name="item-level-recovery"></a>Elemszintű helyreállítás

Ezzel a visszaállítási lehetőséggel visszaállíthatja az egyes fájlokat vagy mappákat az eredeti vagy egy másik helyen.

Adja meg a következő paramétereket a visszaállítási műveletek végrehajtásához:

* **--container-name**: Annak a tárfióknak a neve, amely a biztonsági másolatot tartalmazó eredeti fájlmegosztást tárolja. A tároló nevének vagy rövid nevének beolvasásához használja az [az biztonsági mentési tárolólista](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* **--elemnév:** A visszaállítási művelethez használni kívánt eredeti fájlmegosztás neve. A biztonsági másolat elem nevének vagy rövid nevének beolvasásához használja az [az biztonsági mentési elemlista](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.

Adja meg a következő paramétereket a helyreállítani kívánt elemekhez:

* **SourceFilePath**: A fájl abszolút elérési útja, amelyet a fájlmegosztáson belül karakterláncként kell visszaállítani. Ez az elérési út megegyezik az [az tárolófájl letöltési](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) helyén használt elérési út, vagy az az storage file CLI-parancsokat [jelenít meg.](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show)
* **SourceFileType**: Megadhatja, hogy egy könyvtár vagy fájl legyen kijelölve. Elfogadja a **címzetet** vagy **a fájlt.**
* **ResolveConflict**: Utasítás, ha ütközés van a visszaállított adatokkal. Elfogadja **a Felülírást** vagy **a Kihagyás műveletet.**

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Egyes fájlok vagy mappák visszaállítása az eredeti helyre

Használja az [az biztonsági mentés visszaállítása-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) parancsmag visszaállítási mód beállítása *az eredeti helyre,* hogy visszaállítsa az adott fájlokat vagy mappákat az eredeti helyre.

A következő példa visszaállítja a *RestoreTest.txt* fájlt az eredeti helyén: az *azurefiles* fájlmegosztás.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

A **név** attribútum a kimenetben megegyezik a feladat nevét, amely a biztonsági mentési szolgáltatás által létrehozott a visszaállítási művelethez. A feladat állapotának nyomon követéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Az egyes fájlok vagy mappák visszaállítása másik helyre

Adott fájlok vagy mappák másik helyre való visszaállításához használja az [az biztonsági mentés visszaállítása-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) parancsmalt, amelynek visszaállítási módja *alternatív helyre* van állítva, és adja meg a következő célhoz kapcsolódó paramétereket:

* **--target-storage-account**: Az a tárfiók, amelyre a biztonsági másolatot készítő tartalom visszaáll. A céltárfióknak ugyanazon a helyen kell lennie, mint a tárolónak.
* **--target-file-share**: Az a fájlmegosztás a céltárfiókon belül, amelyre a biztonsági másolatot készítő tartalom visszaáll.
* **--target-folder**: Az a mappa a fájlmegosztás alatt, amelyre az adatokat visszaállítják. Ha a biztonsági másolatot tartalmazó tartalmat vissza szeretné állítani egy gyökérmappába, adja meg a célmappa értékét üres karakterláncként.

A következő példa visszaállítja a *RestoreTest.txt* fájlt eredetileg az *azurefiles* fájlmegosztásegy másik helyre: a *restoredata* mappát az *afaccount1* tárfiókban tárolt *azurefiles1* fájlmegosztás.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

A **név** attribútum a kimenetben megegyezik a feladat nevét, amely a biztonsági mentési szolgáltatás által létrehozott a visszaállítási művelethez. A feladat állapotának nyomon követéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Több fájl vagy mappa visszaállítása eredeti vagy alternatív helyre

Több elem visszaállításához adja át a **forrásfájl elérési útjának** értékét a visszaállítani kívánt fájlok vagy mappák **szóközel választott** elérési útjaként.

A következő példa visszaállítja a *Restore.txt* és az *AFS tesztelési Report.docx* fájlokat az eredeti helyükön.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

A kimenet az alábbihoz hasonló lesz:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

A **név** attribútum a kimenetben megegyezik a feladat nevét, amely a biztonsági mentési szolgáltatás által létrehozott a visszaállítási művelethez. A feladat állapotának nyomon követéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

Ha több elemet szeretne visszaállítani egy másik helyre, használja a fenti parancsot a célhoz kapcsolódó paraméterek megadásával az [egyes fájlok vagy mappák visszaállítása egy másik helyre](#restore-individual-files-or-folders-to-an-alternate-location) szakaszban leírtak szerint.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [kezelheti az Azure-fájlmegosztási biztonsági mentéseket az Azure CLI-vel.](manage-afs-backup-cli.md)
