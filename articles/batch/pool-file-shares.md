---
title: Azure-fájlmegosztás az Azure Batch-készletekben |} A Microsoft Docs
description: Hogyan lehet egy Azure-fájlmegosztási csatlakoztassa egy Linux vagy Windows Azure Batch a készlet számítási csomópontjain.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: lahugh
ms.custom: ''
ms.openlocfilehash: 13ed2caa5ae547747707c368246ea23486dbed72
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469566"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Azure-fájlmegosztás használata Batch-készlet

[Az Azure Files](../storage/files/storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló, amely a Server Message Block (SMB) protokollon keresztül érhető el a felhőben. Ez a cikk nyújt információkat, és a kód példák csatlakoztatja, és az Azure-fájlmegosztások használatával a készlet számítási csomópontjait. Hitelesítésikód-példák használata a Batch .NET-es és a Python SDK-k, de más Batch SDK-k és eszközök használatával hasonló műveleteket hajthat végre.

A Batch natív API-támogatást biztosít az Azure Storage blobs használata olvasása és írása az adatok. Azonban bizonyos esetekben érdemes az Azure-fájlmegosztások elérését a készlet számítási csomópontjait. Például ha egy örökölt számítási feladatokhoz, amelyek SMB-fájlmegosztás függ, vagy a tevékenységek kell a megosztott adatok eléréséhez, vagy létre megosztott kimenetét. 

## <a name="considerations-for-use-with-batch"></a>Szempontok a Batch segítségével

* Fontolja meg Azure-fájlmegosztások használatát, ha címkészleteket, amelyek viszonylag kis mennyiségű párhuzamos feladatot futtatni. Tekintse át a [teljesítmény és méretezhetőség célok](../storage/files/storage-files-scale-targets.md) meghatározni, ha az Azure Files (amely egy Azure Storage-fiókot) kell használni, a várt készlet méretét és az adategység-fájlok száma. 

* Azure-fájlmegosztások vannak [költséghatékony](https://azure.microsoft.com/pricing/details/storage/files/) és konfigurálhatja az adatok replikálása egy másik régióba úgy globálisan redundáns. 

* A helyi számítógépről Azure-fájlmegosztások párhuzamosan lehet csatlakoztatni.

* Lásd még az általános [tervezési megfontolások a](../storage/files/storage-files-planning.md) az Azure-fájlmegosztások.


## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

[Fájlmegosztás létrehozása](../storage/files/storage-how-to-create-file-share.md) egy tárfiókot, amelyet a Batch-fiókhoz van csatolva, vagy egy önálló tárfiókot.

## <a name="mount-a-share-on-a-windows-pool"></a>Egy fájlmegosztás egy Windows-készlet csatlakoztatása

Ez a szakasz ismerteti, és a Windows-csomópontok készletét a megosztás csatlakoztatásához és a egy Azure fájllal hitelesítésikód-példák. A további háttér-információkért lásd: a [dokumentáció](../storage/files/storage-how-to-use-files-windows.md) csatlakoztatási Azure-beli fájlmegosztás a Windows. 

A Batch szolgáltatásban meg kell minden alkalommal, amikor egy tevékenység fut egy Windows-csomóponton a megosztás csatlakoztatásához. Jelenleg nincs lehetőség a feladatok a Windows-csomópontok közötti hálózati kapcsolat megőrzéséhez.

Például egy `net use` parancsot minden egyes tevékenység parancssorának részeként a fájlmegosztás csatlakoztatásához. A fájlmegosztás csatlakoztatása, a következő hitelesítő adatok szükségesek:

* **Felhasználónév**: AZURE\\\<storageaccountname\>, például az AZURE\\*mystorageaccountname*
* **Jelszó**: < StorageAccountKeyWhichEnds a == >, például *XXXXXXXXXXXXXXXXXXXXX ==*

A következő parancsot egy fájlmegosztást csatlakoztathatnak *myfileshare* tárfiókban *mystorageaccountname* , a *S:* meghajtó:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Az egyszerűség kedvéért a az itt szereplő példák a hitelesítő adatokat küldenie a közvetlenül a szöveget. A gyakorlatban javasoljuk, hogy a hitelesítő adatokat, a környezeti változók, tanúsítványok vagy hasonló megoldással például az Azure Key Vault kezelése.

A csatlakoztatási műveletet leegyszerűsítése igény szerint megőrzéséhez a hitelesítő adatokat a csomópontokon. Ezután csatlakoztathatja a hitelesítő adatok nélkül. Hajtsa végre a következő lépéseket:

1. Futtassa a `cmdkey` parancssori segédprogram használatával az indítási tevékenység a készlet konfigurációjában. Ez továbbra is fennáll a hitelesítő adatokat a Windows-csomópontokon. A kezdő tevékenység parancssorának hasonlít:

  ```
  cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

  ```

2. A megosztás csatlakoztatásához használatával minden egyes feladat részeként minden egyes csomóponton `net use`. Például az a következő tevékenység parancssorának csatlakoztatja a fájlmegosztást, mint a *S:* meghajtót. Ez lenne követi, parancsot vagy parancsfájlt, amely hivatkozik a megosztást. Gyorsítótárazott hitelesítő adatok használhatók hívásában `net use`. Ez a lépés feltételezi, hogy az ugyanazon felhasználói identitás használ a feladatokkal, amelyek az indítási tevékenység a készlet, amely nem minden esetben megfelelő használt.

  ```
  cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
  ```

### <a name="c-example"></a>C#-példa
A következő C# példa bemutatja, hogyan is tartalmaz a hitelesítő adatokat, egy Windows-készlet indítási tevékenységet használ. A storage szolgáltatás nevét és a storage hitelesítő adatai továbbítódnak, meghatározott állandókkal van megadva. Az indítási tevékenység itt, normál (nem rendszergazdai) automatikus felhasználói fiók alatt fut, a készlet hatóköre.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Rendezést követően a hitelesítő adatokat használja a feladat parancssorait a megosztás csatlakoztatásához, és hivatkozzon a megosztás olvasási vagy írási műveleteket. Egy alapszintű példaként az alábbi kódrészletben a tevékenység parancssora használja a `dir` parancsot a fájlok listázása a fájlmegosztásban. Ellenőrizze, hogy minden egyes feladat feladatot azonos [felhasználói identitás](batch-user-accounts.md) segítségével futtatta az indítási tevékenység a készlet. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Csatlakoztassa egy könyvtármegosztáson található a Linux-készlet

Azure-fájlmegosztások használatával Linux-disztribúciók csatlakoztathatók a [CIFS kernel ügyfél](https://wiki.samba.org/index.php/LinuxCIFS). Az alábbi példa bemutatja, hogyan Ubuntu 16.04 LTS számítási csomópontok készletét a fájlmegosztást. Ha használja a különböző Linux-disztribúció, az általános lépések hasonlóak, de használja a disztribúciónak megfelelő Csomagkezelő. Részletek és további példákért lásd: [használata az Azure Files linuxszal](../storage/files/storage-how-to-use-files-linux.md).

Először a rendszergazda felhasználói identitást, telepítse a `cifs-utils` csomagot, majd a csatlakoztatási pont létrehozása (például */mnt/MyAzureFileShare*) a helyi fájlrendszerben található. Egy mappát a csatlakoztatási pont hozható létre tetszőleges a fájlrendszerben, de általános egyezmény a alatt létrehozásához a `/mnt` mappát. Győződjön meg arról, hogy ne hozzon létre egy csatlakoztatási pontot közvetlenül `/mnt` (Ubuntu) rendszeren vagy `/mnt/resource` (a többi disztribúciók).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Ezután futtassa a `mount` parancs segítségével csatlakoztassa a fájlmegosztást, ezeket a hitelesítő adatokkal:

* **Felhasználónév**: \<storageaccountname\>, például *mystorageaccountname*
* **Jelszó**: < StorageAccountKeyWhichEnds a == >, például *XXXXXXXXXXXXXXXXXXXXX ==*

A következő parancsot egy fájlmegosztást csatlakoztathatnak *myfileshare* tárfiókban *mystorageaccountname* , */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Az egyszerűség kedvéért a az itt szereplő példák a hitelesítő adatokat küldenie a közvetlenül a szöveget. A gyakorlatban javasoljuk, hogy a hitelesítő adatokat, a környezeti változók, tanúsítványok vagy hasonló megoldással például az Azure Key Vault kezelése.

A Linux-készlet kombinálhatja az összes egyetlen az indítási tevékenység ezeket a lépéseket, vagy pedig egy parancsfájlt is futtathatja. Az indítási tevékenység futtató egy rendszergazda felhasználó a készleten. Állítsa be a kezdő tevékenység sikeresen befejeződik, mielőtt további feladatok futtatása a készleten, hogy a megosztás várnia.

### <a name="python-example"></a>Python-példát

Ez a példa Python bemutatja, hogyan konfigurálhatja egy Ubuntu-készlet indítási tevékenységet a megosztás csatlakoztatásához. A csatlakoztatási pont, a végpont részesedés és a storage hitelesítő adatai továbbítódnak, meghatározott állandókkal van megadva. Az indítási tevékenység a készlet hatóköre egy rendszergazda automatikus felhasználói fiók alatt fut.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration = batchmodels.VirtualMachineConfiguration(
        image_reference = batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id = "batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(_COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

A megosztás csatlakoztatásával és a egy feladat meghatározása után a feladat parancssorait a megosztás használatára. Például a következő alapvető parancsot használ `ls` a fájlmegosztásban található fájlok listázásához.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>További lépések

* Egyéb beállítások olvasása és írása az adatokat a Batch szolgáltatásban, lásd: a [Azure Batch funkcióinak áttekintése](batch-api-basics.md) és [feladatok és tevékenységek kimenetének megőrzése](batch-task-output.md).

* Lásd még a [Batch hajógyárnak](https://github.com/Azure/batch-shipyard) eszközkészlet, amely tartalmazza a [hajógyárnak receptek](https://github.com/Azure/batch-shipyard/tree/master/recipes) fájlrendszereket, a Batch-tárolókhoz kapcsolódó számítási feladatok üzembe helyezéséhez.