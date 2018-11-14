---
title: Bemeneti és kimeneti az Azure Storage tárolja a Batch AI feladat |} A Microsoft Docs
description: Azure Storage gyors és egyszerű, felhőalapú tárolás, a bemeneti és kimeneti fájlok a Batch AI használata
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 42697f7f4bb8c6b9ef785eef0fe2f5f33b2b38a7
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615603"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>Batch AI-feladat bemenete és kimenete az Azure Storage Store

Ez az útmutató az Azure Storage használata a bemeneti és kimeneti fájlok tárolásához, a feladat futtatásakor. Az Azure Storage a Batch AI által támogatott számos tárolási lehetőségek egyike. A Batch AI integrálható az Azure Storage csatlakoztatja az Azure-Felhőtároló rendszerek választásával egy Batch AI feladat vagy a fürt fájlrendszer, a felhőben tárolt fájlok is zökkenőmentes hozzáférést. 

## <a name="introduction-to-azure-storage"></a>A Microsoft Azure Storage bemutatása

Az Azure Storage a Microsoft felhőalapú tárolási megoldása. A Batch AI támogatja az Azure Blob-tárolók és a Batch AI-feladatok és fürtök Azure-fájlmegosztások csatlakoztatására, lehetővé téve a fájlok egy feladatot a érhető el, mintha az a natív fájlrendszer. A Batch AI csatlakoztatja az Azure Blob-tárolók [blobfuse](https://github.com/Azure/azure-storage-fuse), és az Azure-fájlmegosztások az SMB protokollon keresztül. További információ az Azure Storage: [Azure Storage bemutatása](../storage/common/storage-introduction.md).

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>Az Azure Storage-adatkészletek és a bemeneti parancsfájlok Store

Ha a Batch AI-környezet az Azure Storage úgy dönt, azt javasoljuk, hogy (például adatkészletek) a bemeneti fájlok tárolása a Blob-tárolóba, melynek nagyobb átviteli sebességet, a képzési kimeneti tárolása egy fájlmegosztást, amely támogatja a folyamatos átviteli (engedélyezése olvasási kimeneti naplók egyidejűleg a feladat futása közben). 

Mielőtt az Azure Storage használatához [hozzon létre egy Azure Storage-fiók](../storage/common/storage-quickstart-create-account.md). A Batch AI támogatja a csatlakoztatáshoz szükséges kötetek mindkét általános célú v1 (GPv1) és az általános célú v2 (GPv2) Azure Storage-fiókokat. Az Azure Storage-fiók több blobtárolók tárolására is, vagy a fájlkezelő példányokat. Vegye figyelembe a költségekre és teljesítményre vonatkozó követelmények, amikor kiválasztja a létrehozni kívánt tárfiók típusát. További információkat az [Azure Storage-fiókok áttekintésében](../storage/common/storage-account-overview.md) találhat. 

Hozzon létre egy blobtárolót és az adatkészlet egy Azure Blob-tárolóba való feltöltéséhez, válassza ki a következő módszerek egyikét:
- [Az Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) webes grafikus felhasználói felülettel fel. A feltöltendő fájlokat kis számú, az Azure portal a legegyszerűbb műveletet biztosít.
- [Az Azure Storage CLI](../storage/blobs/storage-quickstart-blobs-cli.md) feltöltése (támogatja a directory feltöltés) parancssori felületen. Könyvtárak a fájlok feltöltéséhez használja `az storage blob upload-batch`.
- [egyéb eljárások](../storage/common/storage-moving-data.md), beleértve az alkalmazás SDK-k használatával.

Ehhez hasonlóan az Azure-fájlmegosztás létrehozása, válassza ki a következő módszerek egyikét:
- [Azure Portal](../storage/files/storage-how-to-use-files-portal.md)
- [Az Azure Storage CLI](../storage/files/storage-how-to-use-files-cli.md)
- [egyéb eljárások](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>Automatikus tárhelyszolgáltatás, a Batch AI segítségével

Azt is megteheti, hozzon létre egy Azure Storage-fiókot egy Azure-fájlmegosztást és a Blob-tároló (és automatikusan csatlakoztathatja ezeket a köteteket egy Batch AI-fürtön) használatával a `--use-auto-storage` paraméterrel `az batchai cluster create`. További információkért lásd: [Itt](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account).

## <a name="mount-azure-storage"></a>Az Azure Storage csatlakoztatása 

### <a name="mount-volumes-to-a-job"></a>Csatlakoztatott köteteit egy feladathoz

Az Azure Storage-kötet csatlakoztatási lehetővé teszi, hogy a fájlrendszer minden feladathoz létrehozott keresztül érhető el. Ezért egy feladat olvashat és írhat fájlokat zökkenőmentesen felhőbeli tárhelyén, mintha helyi fájlok.

Egy feladat az Azure CLI-vel létrehozott egy Azure Storage kötet csatlakoztatása, használja a `mountVolumes` tulajdonságot a `job.json` fájl futtatásakor `az batchai job create`. Egy vonatkozó példáért tekintse meg a [Tensorflow GPU elosztott recept](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json). A sémában `mountVolumes` van:
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

`azureFileShares` és `azureBlobFileSystems` mindkét tömb a kötetek képviselő objektum. A helyőrzők leírása:

- < RELATIVE_MOUNT_PATH > - a kötetet csatlakoztatnia kell az elérési úton. Ha például `relativeMountPath` van `jobs`, a kötet lesz a következő helyen található `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs`)
- < tárfiók_neve > – az Azure Storage-fiókot, amely tárol a fájlmegosztás vagy a Blob-tároló neve
- < FILE_SHARE_NAME > - a fájlmegosztás neve
- < BLOB_CONTAINER_NAME > - a Blob-tároló nevét.

Az Azure Batch AI SDK-k az Azure Storage kötet csatlakoztatása, állítsa be a `mount_volumes` (Python) vagy `MountVolumes` (C#, Java) tulajdonságának `JobCreateParameters`. Meg kell adnia a tárfiók hitelesítő adatait, amikor az Azure Batch AI SDK-t tartalmazó kötet. A kötetek csatlakoztatása a sémáinak megtekintése [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python), [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet), és [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai._mount_volumes?view=azure-java-stable).

### <a name="mount-volumes-to-a-cluster"></a>A fürt csatlakoztatott köteteit

Batch AI használatát is támogatja, egy Batch AI-fürtön az Azure Storage-kötetek csatlakoztatása. Amikor egy kötet csatlakoztatva van a fürthöz, a fürtön futó összes feladatot a létrehozott fürtre csatlakoztatott kötetek használhatja. Feladatszintű csatlakoztatási (engedélyezése az egyes feladatok különböző a csatlakoztatott kötetek) a legnagyobb rugalmasságot biztosít, miközben fürt szintű csatlakoztatási elegendő egyszerű helyzetekben lehet.

Egy Azure Storage kötetet a fürt létrehozása az Azure CLI használatával csatlakoztatja, használja a `mountVolumes` tulajdonságot a `cluster.json` fájl futtatásakor `az batchai cluster create`. A sémában `mountVolumes` csatlakoztatása egy fürthöz esetén ugyanaz, mint amikor az egy feladathoz. 

Ehhez hasonlóan használhatja a `mount_volumes` (Python) vagy `MountVolumes` (C#, Java) tulajdonságának `ClusterCreateParameters` az Azure Batch AI SDK-k csatlakoztatása közben. 

## <a name="access-mounted-filesystem-in-a-job"></a>Hozzáférés a csatlakoztatott fájlrendszerek egy feladat

### <a name="azbatchaijobmountroot-environment-variable"></a>$AZ_BATCHAI_JOB_MOUNT_ROOT környezeti változó

Belül a feladat-végrehajtási környezetet, a könyvtárra, amelyben a csatlakoztatott tároló rendszerek címmel elérhetőek legyenek a `$AZ_BATCHAI_JOB_MOUNT_ROOT` környezeti változót (Ha a feladat-szintű csatlakoztatási használja). Ha fürt szintű csatlakoztatási használja, ez a környezeti változó van `$AZ_BATCHAI_MOUNT_ROOT`. Az alábbi példák azt feltételezik feladatszintű csatlakoztatáshoz szükséges.

Az elérési útját egy csatlakoztatott kötet adatait használja a környezeti változó `$AZ_BATCHAI_JOB_MOUNT_ROOT` együtt a csatlakoztatott elérési útja. Például ha a tanítási szkriptet `train.py` töltött fel az Azure File megosztás csatlakoztatásának helye relatív csatlakoztatási útvonal `scripts`, a fájl csak akkor érhető el `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py`.

Ha a tanítási szkriptet egy elérési utat ismeretek szükségesek, parancssori argumentumként kell átadnia. Például, ha az adatok tárolva nevű mappát `train_data` elérési úton csatlakoztatása egy Azure Blob-tárolóban `data`, akkor helyettesíthető `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data` parancssori argumentumként a szkriptbe. Ennek megfelelően módosítania kell a parancsfájl fogadja el a parancssori argumentumokat.

### <a name="abbreviate-input-paths"></a>A bemeneti elérési utak rövidítése

Bemeneti elérési utak rövidítése környezeti változóban, használja a `inputDirectories` tulajdonságát a `job.json` fájlt (vagy `models.JobCreateParameters.input_directories` , ha a Batch AI SDK-val). Sémája `inputDirectories` van:

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

Minden egyes megadott elérési út kerülnek nevű környezeti változó `$AZ_BATCHAI_INPUT_<ID>`. Ezzel a módszerrel egyszerűsítheti a bemeneti fájlok vagy könyvtárak elérési útjára. Például egy tanítási szkriptet elérési útját rövidítése: Ha `"id"` van `"SCRIPT"` és `"path"` van `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"`, majd azt az elérési utat mindig elérhető legyen `$AZ_BATCHAI_INPUT_SCRIPT` a feladat-végrehajtási környezetben.

További információkért lásd: [Itt](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories).

### <a name="abbreviate-output-paths"></a>Kimeneti elérési utak rövidítése

Kimeneti elérési utak rövidítése környezeti változóban, használja a `outputDirectories` tulajdonságát a `job.json` fájlt (vagy `models.JobCreateParameters.output_directories` , ha a Batch AI SDK-val). Ezzel a módszerrel egyszerűsítheti a kimeneti fájlok elérési útjait. Sémája `outputDirectories` van:

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

Minden egyes megadott elérési út kerülnek nevű környezeti változó `$AZ_BATCHAI_OUTPUT_<ID>`. A `pathPrefix` meghatározza, hogy a csatlakoztatott kötetet, a kimenet tárolására (például `"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`). A `pathSuffix` határozza meg a kimeneti mappa nevét (például `"logs"`, `"checkpoints"`). A kimenet a tényleges elérési útja összefűzésével `pathPrefix`, `jobOutputDirectoryPathSegment` (automatikusan létrehozott minden egyes feladathoz) és `pathSuffix`.

További információkért lásd: [Itt](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories).

## <a name="retrieve-job-output-from-azure-storage"></a>Feladat kimenetének beolvasása az Azure Storage-ból

### <a name="use-azure-portal"></a>Az Azure Portal használata

Az Azure Portalon kényelmes módja a feladatok kimenetét megtekintése egy grafikus felhasználói felület a fájlkezelő segítségével. Azonban ha meg szeretné tekinteni az Stdout és Stderr, vagy a elérési utat a kimeneti `outputDirectories`, a fájlok kerülnek egy automatikusan létrehozott útvonalat az Azure Storage-kötet. A további információkat lásd alább.

### <a name="access-stdout-and-stderr-output"></a>Hozzáférés az Stdout és Stderr kimenetet

Használja a `stdOutErrPathPrefix` tulajdonsága `job.json` , hogy a feladat a feladat végrehajtási naplókat és az Stdout és Stderr kimenetet elhelyezése. Például, ha egy fájlmegosztást relatív csatlakoztatási útvonal csatlakoztatta `outputs`, és adja meg a `stdOutErrPathPrefix` kell `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"`, majd a feladat Stdout és Stderr kimenetének elérhető legyen `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr` , amely a kötet csatlakoztatva. Az automatikusan létrehozott elérési utat használjuk az azonos nevű feladatok között kimeneti ütközések elkerülése érdekében.

További információkért lásd: [Itt](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output).

### <a name="list-the-output-files"></a>A kimeneti fájlok listázása

Az Azure CLI segítségével listázza a feladatok elérhető kimeneti fájlokat a `az batchai job file list` parancsot. Például egy feladat a standard kimeneti könyvtár listázásához használja `az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>`.

További információért és példákért lásd: [Itt](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

### <a name="stream-output-files"></a>Stream kimeneti fájlok

Az Azure CLI segítségével a stream-fájlokat a `az batchai job file stream` parancsot. Ha például megtekintése:
- Az STDOUT: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- Stderr: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

További információért és példákért lásd: [Itt](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

## <a name="next-steps"></a>További lépések
- CLI-parancsok az Azure Storage csatoló használatával kapcsolatos további információkért tekintse meg a [Azure Batch AI CLI dokumentációját](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
- Batch AI, beleértve a tárolási csatlakoztatja, és a kimeneti fájlok, olvasásának további használati példákat talál a [Jupyter Notebook receptek Batch AI](https://github.com/Azure/BatchAI).
- További lehetőségek csatlakoztatási tárolási, többek között [NFS-kiszolgáló csatlakoztatása](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs) és [saját NFS, a cifs vagy GlusterFS fürt csatlakoztatása](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)