---
title: Azure rövid útmutató – CNTK-betanítás a Batch AI és az Azure CLI használatával | Microsoft Docs
description: Rövid áttekintést kaphat arról, hogyan futtathat CNTK betanítási feladatokat a Batch AI és az Azure CLI használatával
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 82e3885021a2f2309dfed456d472e7027b8d6cf2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>CNTK-betanítási feladatok futtatása az Azure CLI-vel

Ez a rövid útmutató részletesen bemutatja, hogyan futtatható egy Microsoft Cognitive Toolkit (CNTK) betanítási feladat a Batch AI szolgáltatással az Azure parancssori felületről (CLI). Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható.

Ebben a példában a kézírásos rendszerképek MNIST-adatbázisát használja a konvolúciós neurális hálózat (CNN) betanításéhez egy egycsomópontos, Batch AI által felügyelt GPU-fürtön. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

A rövid útmutatóhoz az Azure CLI legújabb verziójára lesz szükség. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

Emellett a Batch AI erőforrás-szolgáltatókat egyszer regisztrálni kell az előfizetésben az Azure Cloud Shell vagy az Azure CLI használatával. A szolgáltató regisztrációja akár 15 percet is igénybe vehet.

```azurecli
az provider register -n Microsoft.BatchAI
az provider register -n Microsoft.Batch
```


## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

A Batch AI-fürtök és feladatok Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen. Ezután az [az configure](/cli/azure/reference-index#az_configure) paranccsal beállítjuk az erőforráscsoportot és helyet alapértelmezettként.

```azurecli
az group create --name myResourceGroup --location eastus

az configure --defaults group=myResourceGroup

az configure --defaults location=eastus
```

>[!NOTE]
>Az `az` parancs alapértelmezett értékeinek beállítása egy választható lépés. Az alapértelmezett értékeket nem muszáj beállítani. Ha az alapértelmezések beállítása mellett dönt, az oktatóanyag befejezése után távolítsa el az alapértelmezett beállításokat. Az alapértelmezett beállításokat a következő parancsokkal távolíthatja el:
>
>```azurecli
>az configure --defaults group=''
>
>az configure --defaults location=''
>```
>

## <a name="create-a-storage-account"></a>Create a storage account

Ez a rövid útmutató egy Azure-tárfiókot használ a betanítási feladat adatainak és szkriptjeinek tárolására. Az [az storage account create](/cli/azure/storage/account#az_storage_account_create) paranccsal hozzon létre egy tárfiókot.

```azurecli
az storage account create --name mystorageaccount --sku Standard_LRS
```

>[!NOTE]
>Minden tárfióknak egyedi névvel kell rendelkeznie. Az előző `az` parancs és az oktatóanyagban szereplő más hasonló parancsok esetében cserélje ki a `mystorageaccount` beállítás értékét a saját tárfiókja nevére.

## <a name="prepare-azure-file-share"></a>Azure-fájlmegosztás előkészítése

Illusztrációs célok esetében ez a rövid útmutató az Azure-fájlmegosztást használja a betanítási feladatok betanítási adatainak és szkriptjeinek tárolására.

1. Hozzon létre egy *batchaiquickstart* nevű fájlmegosztást az [az storage share create](/cli/azure/storage/share#az_storage_share_create) paranccsal.

  ```azurecli
  az storage share create --account-name mystorageaccount --name batchaiquickstart
  ```
2. Hozzon létre egy *mnistcntksample* könyvtárat a megosztásban az [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create) paranccsal.

  ```azurecli
  az storage directory create --share-name batchaiquickstart  --name mnistcntksample
  ```

3. Töltse le a [mintacsomagot](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D), és bontsa ki. Töltse fel a tartalmát a könyvtárba az [az storage file upload](/cli/azure/storage/file#az_storage_file_upload) paranccsal:

  ```azurecli
  az storage file upload --share-name batchaiquickstart --source Train-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source Test-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source ConvNet_MNIST.py --path mnistcntksample
  ```


## <a name="create-gpu-cluster"></a>GPU-fürt létrehozása
Az [az batchai cluster create](/cli/azure/batchai/cluster#az_batchai_cluster_create) paranccsal hozzon létre egy Batch AI-fürtöt, amely egyetlen GPU VM csomópontból áll. Ebben a példában a VM az alapértelmezett Ubuntu LTS rendszerképet futtatja. Adja meg ehelyett az `image UbuntuDSVM` használatát a Microsoft Deep Learning virtuális gép futtatásához, amely támogatja a további betanítási keretrendszereket. A NC6 méret egy NVIDIA K80 GPU-t tartalmaz. Csatlakoztassa a fájlmegosztást egy *azurefileshare* nevű mappához. A mappa teljes elérési útja a GPU számítási csomóponton: $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.


```azurecli
az batchai cluster create --name mycluster --vm-size STANDARD_NC6 --image UbuntuLTS --min 1 --max 1 --storage-account-name mystorageaccount --afs-name batchaiquickstart --afs-mount-path azurefileshare --user-name <admin_username> --password <admin_password>
```


A fürt létrejötte után a kimenet a következőhöz hasonló:

```azurecli
{
  "allocationState": "resizing",
  "allocationStateTransitionTime": "2017-10-05T02:09:03.194000+00:00",
  "creationTime": "2017-10-05T02:09:01.998000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
  "location": "eastus",
  "name": "mycluster",
  "nodeSetup": {
    "mountVolumes": {
      "azureBlobFileSystems": null,
      "azureFileShares": [
        {
          "accountName": "batchaisamples",
          "azureFileUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart",
          "credentialsInfo": {
            "accountKey": null,
            "accountKeySecretUrl": null
          },
          "directoryMode": "0777",
          "fileMode": "0777",
          "relativeMountPath": "azurefileshare"
        }
      ],
      "fileServers": null,
      "unmanagedFileSystems": null
    },
    "setupTask": null
  },
  "nodeStateCounts": {
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T02:09:02.857000+00:00",
  "resourceGroup": "myresourcegroup",
  "scaleSettings": {
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": {
    "id": null
  },
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "adminUserName": "demoUser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": null
  },
  "virtualMachineConfiguration": {
    "imageReference": {
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04-LTS",
      "version": "latest"
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
```
## <a name="get-cluster-status"></a>A fürt állapotának lekérése

A fürt állapotának áttekintése az [az batchai cluster list](/cli/azure/batchai/cluster#az_batchai_cluster_list) paranccsal kérhető le:

```azurecli
az batchai cluster list -o table
```

A kimenet a következőkhöz hasonló:

```azurecli
Name        Resource Group    VM Size        State     Idle    Running    Preparing    Unusable    Leaving
---------   ----------------  -------------  -------   ------  ---------  -----------  ----------  --------
mycluster   myresourcegroup   STANDARD_NC6   steady    1       0          0            0            0
```

További részletekért futtassa az [az batchai cluster show](/cli/azure/batchai/cluster#az_batchai_cluster_show) parancsot. Ez visszaadja a fürt létrehozása után látható összes fürttulajdonságot.

A fürt akkor áll készen, amikor a csomópontok le lettek foglalva és befejeződött az előkészítés (lásd a `nodeStateCounts` attribútumot). Ha hiba történt, akkor az `errors` attribútum tartalmazza a hiba leírását.

## <a name="create-training-job"></a>Betanítási feladat létrehozása

Miután a fürt elkészült, konfigurálja és küldje el a képzési feladatot.

1. Hozzon létre egy JSON-sablonfájlt job.json néven a feladat létrehozásához:

  ```JSON
  {
    "properties": {
        "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
       "inputDirectories": [{
            "id": "SAMPLE",
            "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
        }],
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
            "pathSuffix": "model",
            "type": "custom"
        }],
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0"
            }
        },
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL"
        }
    }
  }
  ```
2. Az [az batch job create](/cli/azure/batchai/job#az_batchai_job_create) paranccsal hozzon létre egy *myjob* nevű feladatot a fürtön való futtatáshoz.

  ```azurecli
  az batchai job create --name myjob --cluster-name mycluster --config job.json
  ```

A kimenet a következőkhöz hasonló:

```azurecli
{
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
    "resourceGroup": "myresourcegroup"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxTaskRetryCount": null,
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": {
    "imageSourceRegistry": {
      "credentials": null,
      "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0",
      "serverUrl": null
    }
  },
  "creationTime": "2017-10-05T06:41:42.163000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "lastRetryTime": null,
    "retryCount": null,
    "startTime": "2017-10-05T06:41:44.392000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2017-10-05T06:41:44.953000+00:00",
  "experimentName": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.BatchAI/jobs/myjob",
  "inputDirectories": [
    {
      "id": "SAMPLE",
      "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
    }
  ],
  "jobPreparation": null,
  "location": null,
  "name": "cntk_job",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
      "pathSuffix": "model",
      "type": "Custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T06:41:44.238000+00:00",
  "resourceGroup": "demo",
  "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "CNTK",
  "type": "Microsoft.BatchAI/Jobs"
}
```

## <a name="monitor-job"></a>Feladat monitorozása

A feladat állapotának áttekintése az [az batchai job list](/cli/azure/batchai/job#az_batchai_job_list) paranccsal kérhető le:

```azurecli
az batchai job list -o table
```

A kimenet a következőkhöz hasonló:

```azurecli
Name        Resource Group    Cluster    Cluster RG      Nodes  State    Exit code
----------  ----------------  ---------  --------------- -----  -------  -----------
myjob       myresourcegroup   mycluster  myresourcegroup 1      running

```

További részletekért futtassa az [az batchai job show](/cli/azure/batchai/job#az_batchai_job_show) parancsot.

Az `executionState` tartalmazza a feladat jelenlegi végrehajtási állapotát:

* `queued`: a feladat arra vár, hogy fürtcsomópontok elérhetők legyenek
* `running`: a feladat fut
*   `succeeded` (vagy `failed`) : a feladat befejeződött és az `executionInfo` részleteket tartalmaz az eredményről


## <a name="list-stdout-and-stderr-output"></a>Az stdout és az stderr kimenetek listázása
Használja az [az batchai job list-files](/cli/azure/batchai/job#az_batchai_job_list_files) parancsot az stdout és az stderr naplófájlokhoz tartozó hivatkozások listázásához:

```azurecli
az batchai job list-files --name myjob --output-directory-id stdouterr
```

A kimenet a következőkhöz hasonló:

```azurecli
[
  {
    "contentLength": 733,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stderr.txt?sv=2016-05-31&sr=f&sig=Rh%2BuTg9C1yQxm7NfA9YWiKb%2B5FRKqWmEXiGNRDeFMd8%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:38+00:00",
    "name": "stderr.txt"
  },
  {
    "contentLength": 300,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stdout.txt?sv=2016-05-31&sr=f&sig=jMhJfQOGry9jr4Hh3YyUFpW5Uaxnp38bhVWNrTTWMtk%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:29+00:00",
    "name": "stdout.txt"
  }
]
```


## <a name="observe-output"></a>Kimenet megfigyelése

A feladat végrehajtása közben streamelheti a feladat kimeneti fájljait, illetve megjelenítheti a végüket. Az alábbi példa az [az batchai job stream-file](/cli/azure/batchai/job#az_batchai_job_stream_file) parancsot használja a stderr.txt napló streameléséhez:

```azurecli
az batchai job stream-file --job-name myjob --output-directory-id stdouterr --name stderr.txt
```

A kimenet a következőkhöz hasonló. A kimenet a [Ctrl]-[C] billentyűkombináció lenyomásával megszakítható.

```azurecli
…
Finished Epoch[2 of 40]: [Training] loss = 0.104846 * 60000, metric = 3.00% * 60000 3.849s (15588.5 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.077043 * 60000, metric = 2.23% * 60000 3.902s (15376.7 samples/s);
Finished Epoch[4 of 40]: [Training] loss = 0.063050 * 60000, metric = 1.82% * 60000 3.811s (15743.9 samples/s);
…

```

## <a name="delete-resources"></a>Erőforrások törlése

Használja az [az batchai job delete](/cli/azure/batchai/job#az_batchai_job_delete) parancsot a feladat törléséhez:

```azurecli
az batchai job delete --name myjob
```
Használja az [az batchai cluster delete](/cli/azure/batchai/cluster#az_batchai_cluster_delete) parancsot a fürt törléséhez:

```azurecli
az batchai cluster delete --name mycluster
```

Használja az `az group delete` parancsot az ehhez a rövid útmutatóhoz létrehozott erőforráscsoport törléséhez:

```azurecli
az group delete --name myResourceGroup
```

## <a name="restore-azure-cli-20-default-settings"></a>Azure CLI 2.0 alapértelmezett beállításainak visszaállítása

Távolítsa el a hely és az erőforráscsoport előzőleg konfigurált alapértelmezett beállításait:

```azurecli
az configure --defaults group=''

az configure --defaults location=''
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megismerhette, hogyan futtathat egy CNTK-betanítási feladatot egy Batch AI-fürtön az Azure CLI használatával. Ha többet szeretne tudni a Batch AI különböző eszközkészletekkel történő használatáról, tekintse meg a [betanítási módszereket](https://github.com/Azure/BatchAI).

Ha többet szeretne tudni a Batch AI kezeléséről az Azure CLI 2.0 használatával, tekintse meg a [github dokumentációját](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
