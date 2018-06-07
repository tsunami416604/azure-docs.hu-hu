---
title: Azure Batch készletek Azure fájlmegosztás |} Microsoft Docs
description: Megtudhatja, hogyan csatlakoztathatja egy Azure fájlok megosztás egy Linux vagy a Windows Azure kötegben készlet számítási csomópontjai.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 88d7c0d033d7b517a396df27468de8be7ae20be9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34811731"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Az Azure fájlmegosztások használata a Batch-készlet

[Az Azure Files](../storage/files/storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló, amelyek elérhetők a Server Message Block (SMB) protokoll a felhőben. A cikkben információkat és a kód például a csatlakoztatása és az Azure fájlmegosztások használatával készlet számítási csomóponton. A Kódminták Batch .NET és Python SDK-k használata, de egyéb kötegelt SDK-k és eszközök használatával hasonló műveleteket hajthat végre.

Kötegelt natív API-támogatást biztosít az Azure Storage blobs használata olvashatja és írhatja az adatokat. Azonban bizonyos esetekben érdemes a készlet számítási csomópontok az Azure fájlmegosztások eléréséhez. Például egy örökölt alkalmazások és szolgáltatások, amelyek elengedhetetlenek az SMB-fájlmegosztás rendelkezik, vagy a feladatokat kell fér hozzá a megosztott adatokat vagy megosztott eredménye. 

## <a name="considerations-for-use-with-batch"></a>Ezzel a kötegelt szempontjai

* Fontolja meg az Azure fájlmegosztások használatát, ha erőforráskészleteket, amelyekben a párhuzamos tevékenységek száma viszonylag alacsony futtatásához. Tekintse át a [teljesítmény és méretezhetőség célok](../storage/files/storage-files-scale-targets.md) meghatározni, ha Azure-fájlok (amely egy Azure Storage-fiókot használ) kell használni, a várható mérete és az eszköz fájlok száma. 

* Az Azure fájlmegosztás [költséghatékony](https://azure.microsoft.com/pricing/details/storage/files/) , és adatokat tartalmazó replikációs egy másik régióban úgy globálisan redundáns. 

* Az Azure fájlmegosztások egyidejűleg csatlakoztathatnak egy helyi számítógépről.

* További információ az általános [tervezési szempontok](../storage/files/storage-files-planning.md) Azure fájlmegosztásokat.


## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

[Fájlmegosztás létrehozása](../storage/files/storage-how-to-create-file-share.md) egy tárfiókot, amely a Batch-fiókhoz csatolva van, vagy külön tárfiókot.

## <a name="mount-a-share-on-a-windows-pool"></a>Egy könyvtármegosztáson található a Windows-készletben csatlakoztatása

Ez a szakasz lépéseit, és csatlakoztatja, és egy Azure-fájlt használjon Kódminták megosztás Windows csomópontok készlete. További háttér, tekintse meg a [dokumentáció](../storage/files/storage-how-to-use-files-windows.md) csatlakoztatására egy Azure fájlmegosztás Windows rendszeren. 

A kötegelt kell minden alkalommal, amikor egy feladat fut egy Windows-csomóponton a megosztás csatlakoztatásához. Jelenleg nincs lehetőség a feladatok számára Windows-csomópontok közötti hálózati kapcsolat megőrzéséhez.

Például egy `net use` parancsot minden feladat parancssor részeként a fájlmegosztás csatlakoztatásához. A fájlmegosztás csatlakoztatásához az alábbi hitelesítő adatokra van szükség:

* **Felhasználónév**: AZURE\\\<storageaccountname\>, például AZURE\\*mystorageaccountname*
* **Jelszó**: < StorageAccountKeyWhichEnds a == >, például *XXXXXXXXXXXXXXXXXXXXX ==*

A következő parancsot a fájlmegosztás csatlakoztatja *myfileshare* tárfiókban *mystorageaccountname* , a *%s* meghajtó:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Az egyszerűség kedvéért a Itt a példák a hitelesítő adatok továbbítása közvetlenül a szövegben. A gyakorlatban erősen ajánlott a hitelesítő adatokat, a környezeti változók, tanúsítványok vagy hasonló megoldással például az Azure Key Vault kezeléséhez.

A szalagcsatlakoztatási művelet leegyszerűsítése opcionálisan továbbra is fennáll a hitelesítő adatokat a csomópontokon. Ezt követően a megosztás hitelesítő adatokkal nem lehet csatlakoztatni. Hajtsa végre a következő két lépést:

1. Futtassa a `cmdkey` parancssori segédprogram használatával a kezdő tevékenységre a készlet konfigurációjában. Szűnik meg a hitelesítő adatokat, minden Windows-csomóponton. A kezdő tevékenység parancssora hasonló:

  ```
  cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

  ```

2. A megosztás csatlakoztatásához minden feladat használatával részeként minden egyes csomóponton `net use`. Például a következő feladat parancssor csatlakoztatja a fájlmegosztást, mint a *%s* meghajtó. Ez kellene következnie parancsot vagy parancsfájlt, amely hivatkozik a megosztás. Gyorsítótárazott hitelesítő adatok használhatók a hívásában `net use`. Ez a lépés feltételezi, hogy a felhasználó azonosító adatai a feladatokkal, amelyek a kezdő tevékenység az a készletbe, amely nem megfelelő összes forgatókönyv használt.

  ```
  cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
  ```

### <a name="c-example"></a>C# – példa
A következő C# példa bemutatja, hogyan egy Windows-címkészlet egy indítási feladat a hitelesítő adatok megőrzéséhez. A storage szolgáltatás nevét, és a tároló hitelesítő adatait meghatározott állandóként átadott. A kezdő tevékenység itt, egy szabványos (nem rendszergazda) automatikus-felhasználói fiók alatt fut, készlet hatókörrel.

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

A hitelesítő adatok tárolása, miután segítségével a feladat parancssorokat a megosztás csatlakoztatásához és a megosztás hivatkozik az olvasási vagy írási műveletet. Egyszerű példaként a következő kódrészletet a feladat parancssori használja a `dir` fájlok listázása a fájlmegosztás a parancsot. Ügyeljen arra, hogy minden azonos feladat futtatása [felhasználói identitás](batch-user-accounts.md) a készlet a kezdő tevékenység futtatásához használt. 

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

## <a name="mount-a-share-on-a-linux-pool"></a>Egy könyvtármegosztáson található a Linux-készletben csatlakoztatása

Az Azure fájlmegosztások a Linux terjesztéseket segítségével lehet csatlakoztatni az [CIFS kernel ügyfél](https://wiki.samba.org/index.php/LinuxCIFS). A következő példa bemutatja, hogyan Ubuntu 16.04 LTS számítási csomópontok készlete egy fájlmegosztás csatlakoztatásához. Ha egy másik Linux-terjesztés használatához az általános lépéseket hasonló, de a megfelelő terjesztési package manager. Részletek és további példákért lásd: [használata Azure fájlok Linux](../storage/files/storage-how-to-use-files-linux.md).

Először a rendszergazda felhasználói identitást, telepítse a `cifs-utils` csomagot, majd a csatlakoztatási pont létrehozása (például */mnt/MyAzureFileShare*) a helyi fájlrendszerben található. Egy mappa a csatlakoztatási pontok bárhol is létrehozható a fájlrendszerben, de a létrehozás menetét az általános egyezmény a `/mnt` mappát. Lehet, hogy ne hozzon létre egy csatlakoztatási pontjának közvetlenül `/mnt` (a Ubuntu) vagy `/mnt/resource` (a többi terjesztéseket).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Ezután futtassa a `mount` parancsot adja meg ezeket a hitelesítő adatokat a fájlmegosztás csatlakoztatásához:

* **Felhasználónév**: \<storageaccountname\>, például *mystorageaccountname*
* **Jelszó**: < StorageAccountKeyWhichEnds a == >, például *XXXXXXXXXXXXXXXXXXXXX ==*

A következő parancsot a fájlmegosztás csatlakoztatja *myfileshare* tárfiókban *mystorageaccountname* : */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Az egyszerűség kedvéért a Itt a példák a hitelesítő adatok továbbítása közvetlenül a szövegben. A gyakorlatban erősen ajánlott a hitelesítő adatokat, a környezeti változók, tanúsítványok vagy hasonló megoldással például az Azure Key Vault kezeléséhez.

A Linux-készletben a egyesítése egyetlen start feladatban lépésekkel, vagy egy parancsfájlban futtatni azokat. A kezdő tevékenység futtassa egy rendszergazda felhasználó a készletet. Állítsa be a kezdő tevékenység vár arra, hogy a készlet a megosztás hivatkozó további feladatok futtatása előtt sikeresen befejeződik.

### <a name="python-example"></a>Python – példa

A következő Python-példa bemutatja, hogyan konfigurálhatja az indítási feladat a megosztás csatlakoztatásához egy Ubuntu címkészletet. Meghatározott állandóként átadja a csatlakoztatási pont, a fájl megosztási végpont és a tároló hitelesítő adatait. A kezdő tevékenység egy rendszergazda automatikus-felhasználói fiók alatt fut, készlet hatókörrel.

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

A fájlmegosztás csatlakoztatása, és a feladat meghatározása után a feladatütemezés parancssorokat megosztás használatára. Például a következő alapvető parancs `ls` a fájlmegosztást a listában fájlokat.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>További lépések

* Egyéb beállítások és adatok olvasási kötegben, tekintse meg a [Batch funkcióinak áttekintése](batch-api-basics.md) és [megőrizni a feladat- és kimeneti](batch-task-output.md).

* További információ a [kötegelt hajógyárnak](https://github.com/Azure/batch-shipyard) eszközkészlet, köztük a [hajógyárnak receptet](https://github.com/Azure/batch-shipyard/tree/master/recipes) kötegelt tároló munkaterhelések fájl rendszerek központi telepítéséhez.