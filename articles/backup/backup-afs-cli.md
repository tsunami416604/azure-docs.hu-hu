---
title: Az Azure-fájlmegosztások biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági,
description: Megtudhatja, hogy miként készülbiztonsági másolatot az Azure CLI-vel az Azure-fájlmegosztásokról a Recovery Services Vaultban
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844041"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Az Azure-fájlmegosztások biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági és biztonsági,

Az Azure parancssori felülete (CLI) parancssori élményt nyújt az Azure-erőforrások kezeléséhez. Ez egy nagyszerű eszköz az Azure-erőforrások használatához egyéni automatizálás létrehozásához. Ez a cikk ismerteti, hogyan biztonsági másolatot az Azure-fájlmegosztások az Azure CLI. Az [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) vagy az [Azure Portal](backup-afs.md) használatával is elvégezheti ezeket a lépéseket.

Az oktatóanyag végére megtudhatja, hogyan hajthatja végre az alábbi műveleteket az Azure CLI-vel:

* Recovery Services-tároló létrehozása
* Biztonsági mentés engedélyezése az Azure-fájlmegosztásokhoz
* Fájlmegosztások igény szerinti biztonsági mentésének aktiválása

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A parancssori felület helyi telepítéséhez és használatához az Azure CLI 2.0.18-as vagy újabb verzióját kell futtatnia. A CLI-verzió `run az --version`megkereséséhez . Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket.

## <a name="create-a-recovery-services-vault"></a>Helyreállítási szolgáltatások tárolójának létrehozása

A helyreállítási szolgáltatás tároló egy entitás, amely biztosítja a konszolidált nézet és felügyeleti képesség az összes biztonsági mentési elemek. Amikor egy védett erőforrás biztonsági mentésének feladata fut, a rendszer egy helyreállítási pontot hoz létre a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

A helyreállítási szolgáltatások tárolójának létrehozásához kövesse az alábbi lépéseket:

1. A tároló egy erőforráscsoportba kerül. Ha nem rendelkezik meglévő erőforráscsoporttal, hozzon létre egy újat [az csoport létrehozásával.](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) Ebben az oktatóanyagban hozzuk létre az új erőforráscsoport *azurefiles* az USA keleti régiójában.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Használja az [az biztonsági mentési tároló létrehozása](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) parancsmag a tároló létrehozásához. Adja meg a tároló nak ugyanazt a helyet, amelyet az erőforráscsoporthoz használt.

    A következő példa létrehoz egy *azurefilesvault* nevű helyreállítási szolgáltatások tárolóját az USA keleti régiójában.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Adja meg a tároló tárolóhoz használandó redundancia típusát. Helyileg [redundáns tárolást](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) vagy [georedundáns tárolást](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)használhat.

    A következő példa beállítja a tárolási redundancia beállítás *azurefilesvault* **georedundant** az [az biztonsági mentési tároló biztonsági mentési tulajdonságok készlet](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) parancsmag használatával.

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Annak ellenőrzéséhez, hogy a tároló sikeresen létrejött-e, használhatja az [az biztonsági mentési tároló parancsmagját](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) a tároló részleteinek beolvassa. A következő példa a fenti lépésekben létrehozott *azurefilesvault* részleteit jeleníti meg.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    A kimenet hasonló lesz a következő válaszhoz:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Biztonsági mentés engedélyezése az Azure-fájlmegosztásokhoz

Ez a szakasz feltételezi, hogy már rendelkezik egy Azure-fájlmegosztás, amelyhez konfigurálni szeretné a biztonsági mentést. Ha még nem rendelkezik ilyen, hozzon létre egy Azure-fájlmegosztást az [az storage share create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) paranccsal.

A fájlmegosztások biztonsági mentésének engedélyezéséhez létre kell hoznia egy védelmi házirendet, amely meghatározza, hogy egy biztonsági mentési feladat mikor fut, és mennyi ideig tárolja a helyreállítási pontokat. Biztonsági mentési házirendet hozhat létre az [az biztonsági mentési házirend létrehozása](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) parancsmag használatával.

A következő példa az [az biztonsági mentés elleni védelem enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) parancsmaghasználatával engedélyezi az *azurefiles* fájlmegosztás biztonsági mentését az *afsaccount* tárfiókban az *1.*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

A **kimenetben** lévő Name attribútum megegyezik a biztonsági mentési szolgáltatás által a **biztonsági mentési** művelethez létrehozott feladat nevével. A feladat állapotának nyomon követéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Igény szerinti biztonsági mentés aktiválása a fájlmegosztáshoz

Ha igény szerinti biztonsági mentést szeretne kezdeményezni a fájlmegosztáshoz ahelyett, hogy megvárná, hogy a biztonsági mentési házirend az ütemezett időpontban fusson a feladathoz, használja az [az biztonsági mentés idamag-biztonsági](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) mentési parancsmaca.

Az igény szerinti biztonsági mentés elindításához a következő paramétereket kell megadnia:

* **--tárolónév** a fájlmegosztást tároló tárfiók neve. A tároló **nevének** vagy **rövid nevének** beolvasásához használja az [az biztonsági mentési tárolólista](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) parancsot.
* **--elemnév** annak a fájlmegosztásnak a neve, amelyről igény szerinti biztonsági mentést szeretne készíteni. A biztonsági másolat elem **nevének** vagy **rövid nevének** beolvasásához használja az [az biztonsági mentési elemlista](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) parancsot.
* **--retain-until** megadja a dátumot, amíg meg nem szeretné tartani a helyreállítási pontot. Az értéket UTC időformátumban kell beállítani (dd-mm-yyyy).

A következő példa az *azuresfiles* fájlmegosztásigényű biztonsági mentését indítja el az *afsaccount* tárfiókban, *amely2020-2020.01.01-ig*tart megőrzésre kerül .

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

A **név** attribútum a kimenetben megegyezik a feladat nevét, amely a biztonsági mentési szolgáltatás által létrehozott az "igény szerinti biztonsági mentés" művelet. A feladat állapotának nyomon követéséhez használja az [az biztonsági mentési feladat show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) parancsmag.

## <a name="next-steps"></a>További lépések

* Az [Azure-fájlmegosztások cli-vel való visszaállítása](restore-afs-cli.md)
* Ismerje meg, hogyan [kezelheti az Azure fájlmegosztási ackups-t a CLI-vel](manage-afs-backup-cli.md)
