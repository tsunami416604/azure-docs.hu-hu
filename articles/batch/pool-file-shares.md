---
title: Azure-fájlmegosztás Azure Batch készletekhez
description: Azure Files-megosztás csatlakoztatása a számítási csomópontokból egy Linux-vagy Windows-készletben a Azure Batchban.
ms.topic: article
ms.date: 05/24/2018
ms.openlocfilehash: 666ee6bd0e6287545c107427dffcc9f2ccde900a
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115448"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Azure-fájlmegosztás használata batch-készlettel

A [Azure Files](../storage/files/storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást biztosít a felhőben, amely a Server Message Block (SMB) protokollon keresztül érhető el. Ez a cikk a készlet számítási csomópontjain található Azure-fájlmegosztás csatlakoztatására és használatára vonatkozó példákkal kapcsolatos információkat és kódokat tartalmaz. A kód például a Batch .NET és a Python SDK-kat használja, de más batch SDK-k és eszközök használatával hasonló műveleteket is elvégezhet.

A Batch natív API-támogatást biztosít az Azure Storage-Blobok használatához az adatolvasáshoz és az íráshoz. Bizonyos esetekben azonban előfordulhat, hogy egy Azure-fájlmegosztást szeretne elérni a készlet számítási csomópontjairól. Például egy olyan örökölt számítási feladattal rendelkezik, amely egy SMB-fájlmegosztástől függ, vagy a tevékenységeknek megosztott adatokhoz kell hozzáférnie, vagy megosztott kimenetet kell létrehozniuk. 

## <a name="considerations-for-use-with-batch"></a>A Batch szolgáltatással való használat szempontjai

* Érdemes lehet egy Azure-fájlmegosztást használni, ha olyan készletekkel rendelkezik, amelyek viszonylag alacsony számú párhuzamos feladatot futtatnak. Tekintse át a [teljesítmény-és méretezési célokat](../storage/files/storage-files-scale-targets.md) annak megállapításához, hogy a Azure Files (amely egy Azure Storage-fiókot használ) a várt készlet mérete és az adategységek száma alapján kell-e használni. 

* Az Azure-fájlmegosztás [költséghatékony](https://azure.microsoft.com/pricing/details/storage/files/) , és egy másik régióba történő adatreplikálással is konfigurálható, így globálisan redundánsak. 

* Egy Azure-fájlmegosztást egyszerre csatlakoztathat egy helyszíni számítógépről.

* Tekintse meg az Azure-fájlmegosztás általános [tervezési szempontjait](../storage/files/storage-files-planning.md) is.


## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

[Hozzon létre egy fájlmegosztást](../storage/files/storage-how-to-create-file-share.md) egy olyan Storage-fiókban, amely a Batch-fiókjához van csatolva, vagy egy különálló Storage-fiókban.

## <a name="mount-a-share-on-a-windows-pool"></a>Megosztás csatlakoztatása Windows-készleten

Ez a szakasz a Windows-csomópontok készletén található Azure-fájlmegosztás csatlakoztatásához és használatához szükséges lépéseket és kódokat ismerteti. További háttérért tekintse meg az Azure-fájlmegosztás Windows rendszeren való csatlakoztatásának [dokumentációját](../storage/files/storage-how-to-use-files-windows.md) . 

A Batch-ben minden alkalommal csatlakoztatni kell a megosztást, amikor egy feladat egy Windows-csomóponton fut. Jelenleg nem lehet megtartani a hálózati kapcsolatot a Windows-csomópontokon lévő feladatok között.

Például adjon meg egy `net use` parancsot a fájlmegosztás csatlakoztatásához az egyes feladatok parancssorának részeként. A fájlmegosztás csatlakoztatásához a következő hitelesítő adatok szükségesek:

* **Felhasználónév**: Azure\\\<-storageaccountname\>, például Azure\\*mystorageaccountname*
* **Password**: \<StorageAccountKeyWhichEnds in = =>, például *XXXXXXXXXXXXXXXXXXXXX = =*

A következő parancs egy fájlmegosztási *myfileshare* csatlakoztat a Storage-fiók *mystorageaccountname* az *S:* meghajtóként:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Az egyszerűség kedvéért a példák a hitelesítő adatokat közvetlenül a szövegbe továbbítják. A gyakorlatban erősen ajánlott a hitelesítő adatok kezelése környezeti változók, tanúsítványok vagy egy megoldás, például Azure Key Vault használatával.

A csatlakoztatási művelet leegyszerűsítése érdekében opcionálisan megőrzi a hitelesítő adatokat a csomópontokon. Ezután a megosztást hitelesítő adatok nélkül is csatlakoztathatja. Hajtsa végre a következő két lépést:

1. Futtassa a `cmdkey` parancssori segédprogramot egy indítási tevékenységgel a készlet konfigurációjában. Ez minden Windows-csomóponton megőrzi a hitelesítő adatokat. Az indítási tevékenység parancssora a következőhöz hasonló:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Csatlakoztassa a megosztást az egyes csomópontokon az egyes feladatok részeként a használatával `net use`. Például a következő feladat parancssora az *S:* meghajtóként csatlakoztatja a fájlmegosztást. Ezt egy olyan parancs vagy parancsfájl követheti, amely hivatkozik a megosztásra. A gyorsítótárazott hitelesítő adatok a hívásakor `net use`használatosak. Ez a lépés azt feltételezi, hogy ugyanazt a felhasználói identitást használja, mint a készlet indítási tevékenységében használt feladatok esetében, ami nem felel meg az összes forgatókönyvnek.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C# példa
A következő C#-példa azt szemlélteti, hogyan lehet megőrizni a hitelesítő adatokat egy Windows-készleten egy indítási feladat használatával. A tárolási fájl szolgáltatás neve és a tároló hitelesítő adatai megadott konstansként lesznek átadva. Itt az indítási tevékenység egy standard (nem rendszergazda) automatikus felhasználói fiókban fut a készlet hatókörével.

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

A hitelesítő adatok tárolása után a feladat parancssorával csatlakoztassa a megosztást, és hivatkozzon a megosztásra az olvasási vagy írási műveletekben. Alapszintű példaként az alábbi kódrészletben szereplő feladat parancssora a `dir` parancsot használja a fájlmegosztás fájljainak listázásához. Győződjön meg arról, hogy az egyes feladatok feladatait ugyanazzal a [felhasználói identitással](batch-user-accounts.md) futtatja, amelyet az indítási feladat a készletben való futtatásához használt. 

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

## <a name="mount-a-share-on-a-linux-pool"></a>Megosztás csatlakoztatása Linux-készleten

Az Azure-fájlmegosztás Linux-disztribúciókban a [CIFS kernel-ügyféllel](https://wiki.samba.org/index.php/LinuxCIFS)is csatlakoztatható. Az alábbi példa bemutatja, hogyan csatlakoztathat fájlmegosztást az Ubuntu 16,04 LTS számítási csomópontok készletén. Ha más Linux-disztribúciót használ, az általános lépések hasonlóak, de az elosztáshoz megfelelő csomagkezelő használható. A részleteket és a további példákat lásd: [Azure Files használata Linux rendszeren](../storage/files/storage-how-to-use-files-linux.md).

Először a rendszergazda felhasználói identitás alatt telepítse a `cifs-utils` csomagot, és hozza létre a csatlakoztatási pontot (például */mnt/MyAzureFileShare*) a helyi fájlrendszerben. A csatlakoztatási ponthoz tartozó mappa bárhol létrehozható a fájlrendszerben, de ez az `/mnt` általános egyezmény a mappában való létrehozásához. Ügyeljen arra, hogy ne hozzon létre csatlakoztatási `/mnt` pontot közvetlenül az (Ubuntu `/mnt/resource` rendszeren) vagy (más disztribúción).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Ezután futtassa a `mount` parancsot a fájlmegosztás csatlakoztatásához, és adja meg a következő hitelesítő adatokat:

* **Felhasználónév**: \<storageaccountname\>, például *mystorageaccountname*
* **Password**: \<StorageAccountKeyWhichEnds in = =>, például *XXXXXXXXXXXXXXXXXXXXX = =*

Az alábbi parancs egy fájlmegosztási *myfileshare* csatlakoztat a Storage-fiók *mystorageaccountname* a következő helyen: */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Az egyszerűség kedvéért a példák a hitelesítő adatokat közvetlenül a szövegbe továbbítják. A gyakorlatban erősen ajánlott a hitelesítő adatok kezelése környezeti változók, tanúsítványok vagy egy megoldás, például Azure Key Vault használatával.

Linux-készleteken egyetlen indítási feladatban egyesítheti ezeket a lépéseket, vagy futtathatja őket egy parancsfájlban. Futtassa a Start feladatot rendszergazda felhasználóként a készleten. Állítsa be a kezdési feladatot úgy, hogy a rendszer a megosztásra hivatkozó további feladatok futtatása előtt várjon a sikeres befejezésre.

### <a name="python-example"></a>Python-példa

A következő Python-példa bemutatja, hogyan konfigurálhat egy Ubuntu-készletet a megosztás egy indítási feladatba való csatlakoztatásához. A csatlakoztatási pont, a fájlmegosztás végpontja és a tároló hitelesítő adatai megadott konstansként lesznek átadva. Az indítási tevékenység a készlet hatókörével rendelkező rendszergazda automatikus felhasználói fiók alatt fut.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

A megosztás csatlakoztatása és a feladat definiálása után használja a megosztást a feladat parancssorában. A következő alapszintű parancs `ls` például a fájlmegosztás fájljainak listázására használható.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>További lépések

* A Batch-adatok olvasására és írására vonatkozó egyéb lehetőségekért lásd a [Batch funkcióinak áttekintése](batch-api-basics.md) és a feladatok [és tevékenységek kimenetének](batch-task-output.md)megőrzése című témakört.

* Lásd még a [Batch-hajógyár](https://github.com/Azure/batch-shipyard) eszközkészletét, amely tartalmazza a [hajógyári recepteket](https://github.com/Azure/batch-shipyard/tree/master/recipes) a Batch-tároló munkaterhelések fájlrendszerének üzembe helyezéséhez.
