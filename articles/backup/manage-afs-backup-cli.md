---
title: Azure-fájlmegosztás biztonsági másolatainak kezelése a CLI-vel
description: Ismerje meg, hogyan kezelheti és figyelheti az Azure CLI-t a Azure Backup szolgáltatás által készített Azure-fájlmegosztás felügyeletéhez és figyeléséhez.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: bf824b1868ad7c9e4df2cceeca101d82272e18d6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294473"
---
# <a name="manage-azure-file-share-backups-with-azure-cli"></a>Azure-fájlmegosztás biztonsági másolatainak kezelése az Azure CLI-vel

Az Azure parancssori felülete (CLI) parancssori felületet biztosít az Azure-erőforrások kezeléséhez. Ez nagyszerű eszköz az Azure-erőforrások használatára szolgáló egyéni automatizálás kialakításához. Ez a cikk azt ismerteti, hogyan hajthatja végre az alábbi feladatokat az [Azure Backup szolgáltatás](https://docs.microsoft.com/azure/backup/backup-overview)által biztonsági mentés alatt álló Azure-fájlmegosztás felügyeletéhez és figyeléséhez. Ezeket a lépéseket a [Azure Portal](https://portal.azure.com/)is végrehajthatja.

* [Feladatok figyelése](#monitor-jobs)
* [Házirend módosítása](#modify-policy)
* [Fájlmegosztás védelmének leállítása](#stop-protection-on-a-file-share)
* [Fájlmegosztás védelmének folytatása](#resume-protection-on-a-file-share)
* [Storage-fiók regisztrációjának törlése](#unregister-a-storage-account)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A parancssori felület helyi telepítéséhez és használatához az Azure CLI 2.0.18-as vagy újabb verzióját kell futtatnia. A parancssori felület verziójának megkereséséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már rendelkezik egy Azure-fájlmegosztás biztonsági mentésével a [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) szolgáltatás. Ha még nem rendelkezik ilyennel, tekintse meg az [Azure-fájlmegosztás biztonsági mentése a CLI-vel](backup-afs-cli.md) című témakört a fájlmegosztás biztonsági mentésének konfigurálásához. Ebben a cikkben a következő erőforrásokat fogjuk használni:

* **Erőforráscsoport**: *azurefiles*
* **RecoveryServicesVault**: *azurefilesvault*
* **Storage-fiók**: *afsaccount*
* **Fájlmegosztás**: *azurefiles*

## <a name="monitor-jobs"></a>Feladatok figyelése

A biztonsági mentési vagy visszaállítási műveletek indításakor a Backup szolgáltatás létrehoz egy feladatot a követéshez. A befejezett vagy jelenleg futó feladatok figyeléséhez használja az az [Backup Job List](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) parancsmagot. A CLI lehetővé teszi [a jelenleg futó feladatok felfüggesztését](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) , vagy [várjon, amíg a feladatok befejeződik](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

A következő példa a *azurefilesvault* Recovery Services-tároló biztonsági mentési feladatainak állapotát jeleníti meg:

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

A biztonsági mentési szabályzat módosításával módosíthatja a biztonsági mentési gyakoriságot vagy a megőrzési tartományt az [az Backup Item set-Policy](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy)paranccsal.

A szabályzat módosításához adja meg a következő paramétereket:

* **--Container-Name** a fájlmegosztást üzemeltető Storage-fiók neve. A tároló **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* a- **-Name** a fájlmegosztás neve, amelynek módosítani szeretné a házirendet. A biztonsági másolatban szereplő elem **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.
* a- **-Policy-Name** a fájlmegosztás számára beállítani kívánt biztonsági mentési szabályzat neve. A tár összes szabályzatának megtekintéséhez használja az [az biztonsági mentési](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) szabályzatok listáját.

A következő példa a afsaccount *schedule2* található *azurefiles* -fájlmegosztás biztonsági mentési szabályzatát állítja be .

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

A fenti parancsot a tároló és az elem "felhasználóbarát nevei" használatával is végrehajthatja a következő két további paraméter megadásával:

* **--Backup-Management-Type**: *azurestorage*
* **--munkaterhelés-Type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

A kimenetben található **Name** attribútum a "házirend módosítása" művelethez tartozó Backup szolgáltatás által létrehozott feladatokhoz tartozó névvel egyezik. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmagot.

## <a name="stop-protection-on-a-file-share"></a>Fájlmegosztás védelmének leállítása

Az Azure-fájlmegosztások védelmét kétféle módon szüntetheti meg:

* Az összes jövőbeli biztonsági mentési feladat leállítása és az összes helyreállítási pont *törlése*
* Az összes jövőbeli biztonsági mentési feladat leállítása, de a helyreállítási pontok *elhagyása*

Előfordulhat, hogy a tárolóban lévő helyreállítási pontok elhagyása után a rendszer megőrzi a Azure Backup által létrehozott mögöttes pillanatképeket. A helyreállítási pontok elhagyása azonban a fájlmegosztás későbbi visszaállításának lehetősége, ha szükséges. A helyreállítási pontok elhagyása költségével kapcsolatos további információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/storage/files). Ha úgy dönt, hogy törli az összes helyreállítási pontot, a fájlmegosztás nem állítható vissza.

A fájlmegosztás védelmének leállításához adja meg a következő paramétereket:

* a **--Container-Name** a fájlmegosztást üzemeltető Storage-fiók neve. A tároló **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* **– az item-Name** annak a fájlmegosztás a neve, amelynek a védelmét le kívánja állítani. A biztonsági másolatban szereplő elem **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.

### <a name="stop-protection-and-retain-recovery-points"></a>Védelem leállítása és helyreállítási pontok megőrzése

Ha le szeretné állítani a védelmet az adatmegőrzés során, használja az az [Backup Protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) parancsmagot.

Az alábbi példa leállítja a *azurefiles* -fájlmegosztás védelmét, de megőrzi az összes helyreállítási pontot.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

A fenti parancsot a tároló és az elem "felhasználóbarát neve" használatával is végrehajthatja a következő két további paraméter megadásával:

* **--Backup-Management-Type**: *azurestorage*
* **--munkaterhelés-Type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

A kimenetben található **Name** attribútum a "védelem leállítása" művelethez tartozó Backup szolgáltatás által létrehozott feladatokhoz tartozó névvel egyezik. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmagot.

### <a name="stop-protection-without-retaining-recovery-points"></a>A védelem leállítása a helyreállítási pontok megőrzése nélkül

A védelem a helyreállítási pontok megőrzése nélkül történő leállításához használja az az [Backup Protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) parancsmagot a következővel: **true**.

A következő példa leállítja a *azurefiles* -fájlmegosztás védelmét a helyreállítási pontok megőrzése nélkül:

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

A fenti parancsot a tároló és az elem "felhasználóbarát neve" használatával is végrehajthatja a következő két további paraméter megadásával:

* **--Backup-Management-Type**: *azurestorage*
* **--munkaterhelés-Type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Fájlmegosztás védelmének folytatása

Ha leállította egy Azure-fájlmegosztás védelmét, de megtartotta a helyreállítási pontokat, később is folytathatja a védelmet. Ha nem tartja meg a helyreállítási pontokat, nem folytathatja a védelmet.

A fájlmegosztás védelmének folytatásához adja meg a következő paramétereket:

* **--Container-Name** a fájlmegosztást üzemeltető Storage-fiók neve. A tároló **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* **– az item-Name** annak a fájlmegosztás a neve, amelynek a védelmét folytatni kívánja. A biztonsági másolatban szereplő elem **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.
* a- **-Policy-Name** annak a biztonsági mentési házirendnek a neve, amelyre vonatkozóan folytatni szeretné a fájlmegosztás védelmét.

Az alábbi példa az az [Backup Protection Resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) parancsmagot használja a *azurefiles* -fájlmegosztás védelmének folytatásához a *schedule1* biztonsági mentési szabályzatának használatával.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

A fenti parancsot a tároló és az elem "felhasználóbarát neve" használatával is végrehajthatja a következő két további paraméter megadásával:

* **--Backup-Management-Type**: *azurestorage*
* **--munkaterhelés-Type**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

A kimenet **Name** attribútuma megegyezik a Backup szolgáltatás által a "védelem folytatása" művelethez létrehozott feladatokhoz tartozó névvel. A feladatok állapotának nyomon követéséhez használja az az [Backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmagot.

## <a name="unregister-a-storage-account"></a>Storage-fiók regisztrációjának törlése

Ha egy másik Recovery Services-tárolóval szeretne védelemmel ellátni egy adott tárolási fiókban lévő fájlmegosztást, először állítsa le a Storage-fiókban lévő [összes fájlmegosztás védelmét](#stop-protection-on-a-file-share) . Ezután törölje a fiók regisztrációját a védelemhez jelenleg használt Recovery Services-tárból.

Meg kell adnia egy tároló nevét a Storage-fiók regisztrációjának megszüntetéséhez. A tároló **nevének** vagy **rövid nevének** lekéréséhez használja az az [Backup Container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.

Az alábbi példa megszünteti a *afsaccount* Storage-fiók regisztrációját a *azurefilesvault* az az [Backup Container unregister](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) parancsmag használatával.

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

A fenti parancsmagot a tároló "felhasználóbarát neve" használatával is végrehajthatja a következő további paraméter megadásával:

* **--Backup-Management-Type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Következő lépések

További információ: [biztonsági mentési/visszaállítási hibák elhárítása az Azure-fájlmegosztás esetében](troubleshoot-azure-files.md)
