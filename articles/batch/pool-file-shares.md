---
title: Azure-fájlmegosztás az Azure Batch-készletekhez | Microsoft dokumentumok
description: Azure-fájlok megosztásának csatlakoztatása az Azure Batch-ben lévő Linux- vagy Windows-készlet számítási csomópontjaiból.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: 156dad25af5abd1b4d5db32569faf09a23fadfb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022511"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Azure-fájlmegosztás használata kötegkészlettel

[Az Azure Files](../storage/files/storage-files-introduction.md) teljes körűen felügyelt fájlmegosztásokat kínál a felhőben, amelyek a Kiszolgálói üzenetblokk (SMB) protokollon keresztül érhetők el. Ez a cikk információkat és kódpéldákat tartalmaz az Azure-fájlmegosztás csatlakoztatásához és használatához a készlet számítási csomópontjain. A kódpéldák a Batch .NET és a Python SDK-k, de hasonló műveleteket végezhet más Batch SDK-k és eszközök használatával.

A Batch natív API-támogatást nyújt az Azure Storage-blobok használatával az adatok olvasásához és írásához. Bizonyos esetekben azonban előfordulhat, hogy szeretne hozzáférni egy Azure-fájlmegosztást a készlet számítási csomópontjaiból. Például van egy örökölt számítási feladat, amely egy SMB fájlmegosztástól függ, vagy a feladatoknak hozzá kell férniük a megosztott adatokhoz, vagy megosztott kimenetet kell létrehozniuk. 

## <a name="considerations-for-use-with-batch"></a>A Batch alkalmazással kapcsolatos szempontok

* Fontolja meg egy Azure-fájlmegosztás használatát, ha viszonylag kevés párhuzamos feladatot futtató készletekkel rendelkezik. Tekintse át a [teljesítmény- és méretezési célokat](../storage/files/storage-files-scale-targets.md) annak meghatározásához, hogy az Azure-fájlok (amely egy Azure Storage-fiókot használ) kell-e használni, a várható készletmérete és az eszközfájlok száma miatt. 

* Az Azure-fájlmegosztások [költséghatékonyak,](https://azure.microsoft.com/pricing/details/storage/files/) és konfigurálhatók egy másik régióba irányuló adatreplikációval, így globálisan redundánsak. 

* Az Azure-fájlmegosztást egyidejűleg csatlakoztathatja egy helyszíni számítógépről.

* Tekintse meg az Azure-fájlmegosztások általános [tervezési szempontjait](../storage/files/storage-files-planning.md) is.


## <a name="create-a-file-share"></a>Fájlmegosztás létrehozása

[Hozzon létre egy fájlmegosztást](../storage/files/storage-how-to-create-file-share.md) egy tárfiókban, amely a Batch-fiókhoz van csatolva, vagy egy külön tárfiókban.

## <a name="mount-a-share-on-a-windows-pool"></a>Megosztás csatlakoztatása Windows-készletre

Ez a szakasz lépéseket és kódpéldákat tartalmaz az Azure-fájlmegosztás csatlakoztatásához és használatához a Windows-csomópontok készletén. További háttér, tekintse meg az Azure-fájlmegosztás windowsos csatlakoztatásához szükséges [dokumentációt.](../storage/files/storage-how-to-use-files-windows.md) 

A Batch alkalmazásban minden alkalommal csatlakoztatnia kell a megosztást, amikor egy feladatot egy Windows-csomóponton futtatnak. Jelenleg nem lehet megtartani a Windows-csomópontokon lévő feladatok közötti hálózati kapcsolatot.

Például adjon `net use` meg egy parancsot a fájlmegosztás csatlakoztatására az egyes feladatparancssorok részeként. A fájlmegosztás csatlakoztatásához a következő hitelesítő adatokra van szükség:

* **Felhasználónév**:\\\<AZURE\>storageaccountname ,\\például AZURE*mystorageaccountname*
* **Jelszó** \<: StorageAccountKeyWhichEnds in==>, például *XXXXXXXXXXXXXXXXXX==*

A következő parancs csatolja a fájlmegosztás *myfileshare* a tárfiók *mystorageaccountname* az *S:* meghajtó:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Az egyszerűség kedvéért az itt látható példák közvetlenül a szövegben adják át a hitelesítő adatokat. A gyakorlatban azt javasoljuk, hogy a hitelesítő adatok kezelése környezeti változók, tanúsítványok vagy egy megoldás, például az Azure Key Vault használatával.

A csatlakoztatási művelet egyszerűsítése érdekében tetszés szerint továbbra is meg kell persistni a hitelesítő adatokat a csomópontokon. Ezután hitelesítő adatok nélkül csatlakoztathatja a megosztást. Hajtsa végre az alábbi két lépést:

1. Futtassa a `cmdkey` parancssori segédprogramot egy indítási feladattal a készletkonfigurációban. Ez továbbra is megőrzi a hitelesítő adatokat az egyes Windows-csomópontokon. A kezdési feladat parancssora a következőhöz hasonló:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Csatlakoztassa a megosztást az egyes csomópontokra `net use`az egyes feladatok részeként a használatával. A következő feladatparancssor például *s:* meghajtóként csatlakoztatja a fájlmegosztást. Ezt egy olyan parancs vagy parancsfájl követné, amely a megosztásra hivatkozik. A gyorsítótárazott hitelesítő adatok `net use`a hívásban használatosak. Ez a lépés feltételezi, hogy ugyanazt a felhasználói identitást használja a készlet indítási feladatában használt feladatokhoz, amely nem minden esetben megfelelő.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C# példa
A következő C# példa bemutatja, hogyan lehet megtartani a hitelesítő adatokat a Windows-készlet egy indítási feladat használatával. A tárolófájl-szolgáltatás nevét és a tárolási hitelesítő adatokat meghatározott állandókként adják át. Itt az indítási feladat egy szabványos (nem rendszergazdai) automatikus felhasználói fiók alatt fut készlethatókörrel.

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

A hitelesítő adatok tárolása után a feladat parancssoraival csatlakoztathatja a megosztást, és hivatkozhat a megosztásra az olvasási vagy írási műveletekben. Alappéldaként a következő kódrészlet feladatparancssora a `dir` paranccsal listázhatja a fájlmegosztásban lévő fájlokat. Győződjön meg arról, hogy minden feladatfeladatot ugyanazzal a [felhasználói identitással](batch-user-accounts.md) futtat, mint a készletben lévő indítási feladat. 

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

## <a name="mount-a-share-on-a-linux-pool"></a>Megosztás csatlakoztatása Linux-készletre

Az Azure fájlmegosztások linuxos disztribúciókban csatlakoztathatók a [CIFS kernelügyfél](https://wiki.samba.org/index.php/LinuxCIFS)használatával. A következő példa bemutatja, hogyan csatlakoztathat egy fájlmegosztást az Ubuntu 16.04 LTS számítási csomópontok készletére. Ha egy másik Linux-disztribúciót használ, az általános lépések hasonlóak, de használja a disztribúciónak megfelelő csomagkezelőt. További részleteket és további példákat az [Azure-fájlok használata Linuxmal](../storage/files/storage-how-to-use-files-linux.md).

Először egy rendszergazdai felhasználói identitás `cifs-utils` alatt telepítse a csomagot, és hozza létre a csatlakoztatási pontot (például */mnt/MyAzureFileShare)* a helyi fájlrendszerben. A csatlakoztatási pont mappája a fájlrendszer bármely pontján létrehozható, de `/mnt` a mappa alatt gyakran felelő, hogy ez a mappa alatt hozható létre. Ügyeljen arra, hogy ne hozzon létre egy csatlakoztatási pontot közvetlenül `/mnt` (ubuntu) vagy `/mnt/resource` (más disztribúciókon).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Ezután futtassa a `mount` parancsot a fájlmegosztás csatlakoztatásához, és adja meg a következő hitelesítő adatokat:

* **Felhasználónév** \<: storageaccountname\>, például *mystorageaccountname*
* **Jelszó** \<: StorageAccountKeyWhichEnds in==>, például *XXXXXXXXXXXXXXXXXX==*

A következő parancs csatolja a fájlmegosztás *myfileshare* a tárfiók *mystorageaccountname* a */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Az egyszerűség kedvéért az itt látható példák közvetlenül a szövegben adják át a hitelesítő adatokat. A gyakorlatban azt javasoljuk, hogy a hitelesítő adatok kezelése környezeti változók, tanúsítványok vagy egy megoldás, például az Azure Key Vault használatával.

Egy Linux-készleten egyesítheti ezeket a lépéseket egyetlen indítási feladatban, vagy futtathatja őket egy parancsfájlban. Futtassa a kezdő feladatot rendszergazdai felhasználóként a készleten. Állítsa be, hogy a kezdőfeladat várjon a sikeres befejezéssel, mielőtt további feladatokat futtatna a megosztásra hivatkozó készleten.

### <a name="python-example"></a>Példa pythonra

A következő Python-példa bemutatja, hogyan konfigurálhat egy Ubuntu készletet a megosztás csatlakoztatásához egy kezdő feladatban. A csatlakoztatási pont, a fájlmegosztási végpont és a tárolási hitelesítő adatok meghatározott állandókként kerülnek átadásra. Az indítási feladat egy rendszergazdai automatikus felhasználói fiók alatt fut készlethatókörrel.

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

A megosztás csatlakoztatása és a feladat definiálása után használja a megosztást a feladat parancssoraiban. A következő alapvető parancs `ls` például a fájlmegosztásban lévő fájlok felsorolására szolgál.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>További lépések

* Az adatok Batch alkalmazásban való olvasásának és írásának egyéb lehetőségeiről a [Köteg szolgáltatás áttekintése](batch-api-basics.md) és [a Feladat- és feladatkimenet megőrzése című témakörben](batch-task-output.md)olvashat.

* Lásd még a [Batch Hajógyár](https://github.com/Azure/batch-shipyard) eszközkészletet, amely tartalmazza a [Hajógyár receptjeit](https://github.com/Azure/batch-shipyard/tree/master/recipes) a batch tárolószámítási feladatok fájlrendszereinek üzembe helyezéséhez.
