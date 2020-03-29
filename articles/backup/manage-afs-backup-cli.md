---
title: Azure-fájlmegosztási biztonsági mentések kezelése az Azure CLI-vel
description: Ismerje meg, hogyan használhatja az Azure CLI-t az Azure Backup által biztonsági másolatot készítő Azure-fájlmegosztások kezelésére és figyelésére.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934880"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Azure-fájlmegosztási biztonsági mentések kezelése az Azure CLI-vel

Az Azure CLI parancssori élményt nyújt az Azure-erőforrások kezeléséhez. Ez egy nagyszerű eszköz az Azure-erőforrások használatához egyéni automatizálás létrehozásához. Ebből a cikkből megtudhatja, hogyan végezhet feladatokat az Azure Backup által biztonsági mentésben szereplő Azure-fájlmegosztások kezeléséhez és [figyeléséhez.](https://docs.microsoft.com/azure/backup/backup-overview) Ezeket a lépéseket az [Azure Portalon](https://portal.azure.com/)is végrehajthatja. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A CLI helyi telepítéséhez és használatához futtatnia kell az Azure CLI 2.0.18-as vagy újabb verzióját. A parancssori felület verziójának megkereséséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már rendelkezik az [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)által biztonsági másolattal. Ha még nem rendelkezik ilyennel, olvassa el [az Azure fájlmegosztások biztonsági mentése a CLI-vel](backup-afs-cli.md) című témakört a fájlmegosztások biztonsági mentésének konfigurálásához című témakörben. Ebben a cikkben a következő forrásokat használja:

* **Erőforráscsoport**: *azurefiles*
* **RecoveryServicesVault**: *azurefilesvault*
* **Tárfiók**: *afsaccount*
* **Fájlmegosztás**: *azurefiles*

## <a name="monitor-jobs"></a>Feladatok figyelése

Amikor biztonsági mentési vagy visszaállítási műveleteket indít el, a biztonsági mentési szolgáltatás létrehoz egy feladatot a nyomon követéshez. Befejezett vagy jelenleg futó feladatok figyeléséhez használja az [az biztonsági mentési feladat listájának](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) parancsmag. A CLI-vel [felfüggesztheti az éppen futó feladatot,](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) vagy [megvárhatja, amíg egy feladat befejeződik.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)

A következő példa az *azurefilesvault* recovery services-tároló biztonsági mentési feladatainak állapotát jeleníti meg:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Házirend módosítása

A biztonsági mentési házirend módosításával módosíthatja a biztonsági mentés gyakoriságát vagy az adatmegőrzési tartományt [az az biztonsági másolat elemkészlet-házirend](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy)használatával.

A házirend módosításához adja meg a következő paramétereket:

* **--container-name**: A fájlmegosztást tároló tárfiók neve. A tároló **nevének** vagy **rövid nevének** beolvasásához használja az [az biztonsági mentési tárolólista](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* **--name**: Annak a fájlmegosztásnak a neve, amelynek házirendét módosítani szeretné. A biztonsági másolat elem **nevének** vagy **rövid nevének** beolvasásához használja az [az biztonsági mentési elemlista](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.
* **--házirend-név**: A fájlmegosztáshoz beállítani kívánt biztonsági mentési házirend neve. Az [biztonsági mentési házirend-lista](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) segítségével megtekintheti a tároló összes házirendjét.

A következő példa beállítja az *afsaccount* tárfiókban található *azurefiles* fájlmegosztás *schedule2* biztonsági mentési szabályzatát.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Az előző parancsot a tároló és az elem rövid neveivel is futtathatja a következő két további paraméter megadásával:

* **--backup-management-type**: *azurestorage*
* **--munkaterhelés típusa:** *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

A **név** attribútum a kimenetben megegyezik a feladat nevét, amely a biztonsági mentési szolgáltatás által létrehozott a változási házirend-művelet. A feladat állapotának nyomon követéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

## <a name="stop-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének leállítása

Az Azure-fájlmegosztások védelmét kétféle módon szüntetheti meg:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és *törölje az* összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatokat, de *hagyja a* helyreállítási pontokat.

Előfordulhat, hogy a helyreállítási pontok tárolása során a helyreállítási pontok at, mert az Azure Backup által létrehozott alapul szolgáló pillanatképek megmaradnak. A helyreállítási pontok elhagyása a fájlmegosztás későbbi visszaállításának lehetősége, ha szeretné. A helyreállítási pontok elhagyásának költségeiről az [árképzés részleteiben](https://azure.microsoft.com/pricing/details/storage/files)talál további információt. Ha úgy dönt, hogy törli az összes helyreállítási pontot, nem állíthatja vissza a fájlmegosztást.

A fájlmegosztás védelmének leállításához adja meg a következő paramétereket:

* **--container-name**: A fájlmegosztást tároló tárfiók neve. A tároló **nevének** vagy **rövid nevének** beolvasásához használja az [az biztonsági mentési tárolólista](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* **--elemnév:** Annak a fájlmegosztásnak a neve, amelynek védelmét le szeretné állítani. A biztonsági másolat elem **nevének** vagy **rövid nevének** beolvasásához használja az [az biztonsági mentési elemlista](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.

### <a name="stop-protection-and-retain-recovery-points"></a>Védelem leállítása és helyreállítási pontok megtartása

Az adatok megőrzése közbeni védelem leállításához használja az [az biztonsági mentés imázsának letiltási](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) parancsmagja.

A következő példa leállítja az *azurefiles* fájlmegosztás védelmét, de megtartja az összes helyreállítási pontot.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Az előző parancsot a tároló és az elem rövid nevével is futtathatja a következő két további paraméter megadásával:

* **--backup-management-type**: *azurestorage*
* **--munkaterhelés típusa:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

A **név** attribútum a kimenetben megfelel a feladat neve, amely a biztonsági mentési szolgáltatás által létrehozott a stop védelmi művelet. A feladat állapotának nyomon követéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

### <a name="stop-protection-without-retaining-recovery-points"></a>Védelem leállítása a helyreállítási pontok megtartása nélkül

A helyreállítási pontok megtartása nélküli védelem leállításához használja az [az biztonsági mentés elleni védelem letiltási](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) parancsmagja it, a **delete-backup-data** beállítás **értéke igaz.**

A következő példa leállítja az *azurefiles* fájlmegosztás védelmét a helyreállítási pontok megőrzése nélkül.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Az előző parancsot a tároló és az elem rövid nevével is futtathatja a következő két további paraméter megadásával:

* **--backup-management-type**: *azurestorage*
* **--munkaterhelés típusa:** *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének folytatása

Ha leállította egy Azure-fájlmegosztás védelmét, de megőrizte a helyreállítási pontokat, később folytathatja a védelmet. Ha nem tartja meg a helyreállítási pontokat, nem folytathatja a védelmet.

A fájlmegosztás védelmének folytatásához adja meg a következő paramétereket:

* **--container-name**: A fájlmegosztást tároló tárfiók neve. A tároló **nevének** vagy **rövid nevének** beolvasásához használja az [az biztonsági mentési tárolólista](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* **--elemnév:** Annak a fájlmegosztásnak a neve, amelynek védelmét folytatni szeretné. A biztonsági másolat elem **nevének** vagy **rövid nevének** beolvasásához használja az [az biztonsági mentési elemlista](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.
* **--házirend-név**: Annak a biztonsági mentési házirendnek a neve, amelynek védelmét folytatni szeretné a fájlmegosztás védelme érdekében.

A következő példa az [az biztonsági mentés védelmi folytatási](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) parancsmag használatával folytatja az *azurefiles* fájlmegosztás védelmét az *schedule1* biztonsági mentési házirend használatával.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Az előző parancsot a tároló és az elem rövid nevével is futtathatja a következő két további paraméter megadásával:

* **--backup-management-type**: *azurestorage*
* **--munkaterhelés típusa:** *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

A **név** attribútum a kimenetben megegyezik a feladat nevét, amely a biztonsági mentési szolgáltatás által létrehozott a folytatásvédelmi művelet. A feladat állapotának nyomon követéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

## <a name="unregister-a-storage-account"></a>Tárfiók regisztrációjának megszüntetése

Ha egy adott tárfiókban lévő fájlmegosztásokat egy másik Recovery Services-tároló használatával szeretné védeni, először [állítsa le a tárfiók összes fájlmegosztásának védelmét.](#stop-protection-on-a-file-share) Ezután törölje a fiókot a recovery szolgáltatások tároló jelenleg védelemre használt.

Meg kell adnia egy tároló nevet a tárfiók regisztrációjának megszüntetése. A tároló **nevének** vagy **rövid nevének** beolvasásához használja az [az biztonsági mentési tárolólista](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.

A következő példa az *afsaccount* tárfiókot az *azurefilesvault-ból* az [az biztonsági mentési tároló regisztrációjának megszüntetése](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) segítségével nem regisztrálja.

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Az előző parancsmast a tároló rövid nevének használatával is futtathatja a következő további paraméter megadásával:

* **--backup-management-type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>További lépések

További információt az [Azure-fájlmegosztások biztonsági másolatának elhárítása című témakörben](troubleshoot-azure-files.md)talál.
